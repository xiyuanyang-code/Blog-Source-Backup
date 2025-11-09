---
title: >-
  Fine-Tuning for Enhancing Code Generation and Planning Capabilities in Small
  Models
date: 2025-11-09 14:11:48
math: True
excerpt: "Documenting a Hands-On Experiment with LLaMA Factory for Model Fine-Tuning: Leveraging Model Distillation and Data Generation to Enhance Reasoning and Planning Capabilities in Small Models for Code Generation Tasks"
index_img: /img/cover/fine-tuning-planning.png
categories:
  - Artificial Intelligence
  - LLM
tags:
  - Code Generations
  - LLM
  - Post Training
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Fine-Tuning for Enhancing Code Generation and Planning Capabilities in Small Models

Just for my personal trial and error, don't be so serious.

All the code has been open sourced into my-fork-version of [LLaMa-Factory](https://github.com/xiyuanyang-code/LLaMA-Factory).

## Introduction

Efficient fine-tuning taught by larger teacher models for small models has been proved to be an efficient way to enhance domain-specific capabilities. The author trying to use **LLama-Factory** as the main architecture to train and fine-tune `Qwen/Qwen2.5-3B`.

## Experiment Setup

- Devices: A100 * 2

## Workflow

### SFT for cold start and instructions

因为参数量过小的模型本身就存在输出不稳定的情况，因此首先使用通用数据集 `identity,alpaca_zh_demo,alpaca_en_demo` 来增强模型的指令遵循的基本能力作为冷启动。

下面是进行微调的基本参数设置

```yaml
### model
model_name_or_path: Qwen/Qwen2.5-3B
trust_remote_code: true

### method
stage: sft
do_train: true
finetuning_type: lora
lora_rank: 8
lora_target: all

### dataset
dataset: identity,alpaca_zh_demo,alpaca_en_demo
template: qwen
cutoff_len: 2048
max_samples: 1000
overwrite_cache: true
preprocessing_num_workers: 16
dataloader_num_workers: 4

### output
output_dir: saves/qwen2_sft/lora/dpo
logging_steps: 2
overwrite_output_dir: true
save_only_model: true
report_to: tensorboard  # choices: [none, wandb, tensorboard, swanlab, mlflow]

### train
per_device_train_batch_size: 1
gradient_accumulation_steps: 8
learning_rate: 1.0e-4
num_train_epochs: 3.0
lr_scheduler_type: cosine
warmup_ratio: 0.1
bf16: true
ddp_timeout: 180000000
resume_from_checkpoint: null
```

### SFT for Planning Enhancement

由于受到参数量的限制，直接通过后训练显著增强高度泛化的代码能力是不现实的，因此，在第二阶段的监督微调中，作者决定**自己代码蒸馏数据并且生成指令数据集**，实现 Enhancement for Planning Instructions.

#### Data Preparations

考虑到数据质量，容量和成本等一系列因素，我最终采用了 [CodeAlpaca_20K](https://huggingface.co/datasets/HuggingFaceH4/CodeAlpaca_20K) 作为最终的原始数据集。数据集一共包含 20022 个用例（分为 train and test split），使用训练集的测试样例如下：

```json
{
  "prompt": "Create a Java class which sorts the given array of numbers. [9, 2, 4, 3, 6, 1]",
  "completion": "class ArraySort {

void sort(int arr[]) {
int n = arr.length;

// One by one move boundary of unsorted subarray
for (int i = 0; i < n-1; i++) {

// Find the minimum element in unsorted array
int min_index = i;
for (int j = i+1; j < n; j++)
if (arr[j] < arr[min_index])
min_index = j;

// Swap the found minimum element with the first element
int temp = arr[min_index];
arr[min_index] = arr[i];
arr[i] = temp;
}
}

// Prints the array
void printArray(int arr[]) {
int n = arr.length;
for (int i=0; i<n; ++i)
System.out.print(arr[i] + " ");
System.out.println();
}

// Driver code to test above
public static void main(String args[])
{
ArraySort ob = new ArraySort();
int arr[] = {9, 2, 4, 3, 6, 1};
ob.sort(arr);
System.out.println(\"Sorted array\");
ob.printArray(arr);
}
}"
}
```

#### Data Generation Pipeline

使用满血 DeepSeek V3.1 作为教师模型（太贵了，用不起闭源模型），使用提示词诱导等方法实现数据生成，脚本和生成的训练样例如下：

```python
import json
from datasets import load_dataset
import openai
from tqdm import tqdm
import os
import concurrent.futures 

OUTPUT_FILE = "data/planning_coding.jsonl"
OPENAI_MODEL = "deepseek-v3"
MAX_WORKERS = 5 
PLANNING_PROMPT = """你是一个高级算法工程师。真正优秀的算法工程师总会在写代码之前做好任务的规划，因此，请你在解决这些问题之前**显式地做出行之有效的任务规划**，并说“真正的大师总是会在规划之后再动手”。

你需要解决的问题是：
{task_description}

你可以看到标准答案，但是**请你根据你的规划逐步生成代码**，你必须输出你的一步步完成规划的轨迹！最关键的不是答案，而是你的规划能力！

{GT}

请严格按照以下格式回答：

真正的大师总是需要规划之后再动手！我先做一个规划：
### Planning
1. ...
2. ...
3. ...

### Code

#### Solving Sub-Tasks1
```the programming language
...
/`/`/`

#### Solving Sub-Tasks2
```the programming language
...
/```

#### Solving Sub-Tasks3
```the programming language
...
/```

#### Solving Sub-Tasks4
```the programming language
...
/```

...

#### Final Code
```the programming language
...
/```

"""

def get_dataset():
    """加载数据集并返回编码任务列表。"""
    ds = load_dataset("HuggingFaceH4/CodeAlpaca_20K", split="train")
    coding_tasks = [x for x in ds]
    print(f"The length of the coding tasks: {len(coding_tasks)}")
    return coding_tasks


def generate_planning_code(task_description: str, GT: str) -> str:
    """调用OpenAI API生成带规划的代码，这是I/O密集型操作。"""
    prompt = PLANNING_PROMPT.format(task_description=task_description, GT=GT)
    # 注意：在实际部署中，您需要确保这里的base_url和api_key配置正确
    client = openai.OpenAI(api_key="EMPTY", base_url="http://127.0.0.1:18889/v1")
    try:
        response = client.chat.completions.create(
            model=OPENAI_MODEL,
            messages=[{"role": "user", "content": prompt}],
            temperature=0.7,
            max_tokens=2048,
        )
        return response.choices[0].message.content.strip()
    except Exception as e:
        # 记录错误，防止程序中断
        print(f"[ERROR] API call failed: {e}")
        return f"[ERROR] {e}"

def process_task(task: dict) -> dict:
    """单个任务的处理器，用于在线程池中执行。"""
    full_response = generate_planning_code(
        task_description=task["prompt"],
        GT=task["completion"]
    )
    # 构造最终要写入JSONL的字典项
    return {"instruction": task, "input": "", "output": full_response}


def main():
    """主函数，负责加载数据、设置并发执行和结果收集。"""
    print("Loading Datasets...")
    coding_tasks = get_dataset()
    
    os.makedirs(os.path.dirname(OUTPUT_FILE), exist_ok=True)
    
    tasks_completed = 0

    print(f"Starting concurrent data generation with {MAX_WORKERS} workers...")
    
    with open(OUTPUT_FILE, "w", encoding="utf-8") as f:
        with concurrent.futures.ThreadPoolExecutor(max_workers=MAX_WORKERS) as executor:
            future_to_task = {executor.submit(process_task, task): task for task in coding_tasks}
            
            for future in tqdm(
                concurrent.futures.as_completed(future_to_task), 
                total=len(coding_tasks), 
                desc="Generating planning data (Writing on completion)"
            ):
                try:
                    result_item = future.result()
                    f.write(json.dumps(result_item, ensure_ascii=False) + "\n")
                    f.flush() # 强制写入磁盘
                    tasks_completed += 1
                except Exception as exc:
                    task_failed = future_to_task[future]
                    print(f'\n[Task Error] Task for prompt "{task_failed.get("prompt", "Unknown")[:30]}..." generated an exception: {exc}')
    
    print(f"\n生成完成！成功写入 {tasks_completed} 个任务数据至：{OUTPUT_FILE}")


def test_calling():
    client = openai.OpenAI(api_key="EMPTY", base_url="http://127.0.0.1:18889/v1")
    response = client.chat.completions.create(
        model=OPENAI_MODEL,
        messages=[{"role": "user", "content": "Introduce yourself"}],
        temperature=0.7,
        max_tokens=1024,
    )
    print(response)

def get_file_data(line: str):
    line_data = json.loads(line)
    return {
        "instruction": "Solving the problems below, remember to draft a planning in chinese, and then finish your sub-tasks step-by-step",
        "input": line_data["instruction"]["prompt"],
        "output": line_data["output"]
    }

def get_json_file_path():
    file_path = "./data/planning_coding.jsonl"
    with open(file_path, "r", encoding="utf-8") as file:
        data = [get_file_data(line=line) for line in file]
    with open("./data/planning_coding.json", "w", encoding="utf-8") as file:
        json.dump(data, file, ensure_ascii=False, indent=2)
    

if __name__ == "__main__":
    # main()
    get_json_file_path()
```

生成的数据样例如下，是标准的 alpaca 格式的数据集。

```json
[
  {
    "instruction": "Solving the problems below, remember to draft a planning in chinese, and then finish your sub-tasks step-by-step",
    "input": "Change the following loop so that it prints out the numbers from 10 to 1 in descending order.\nfor i in range(1, 11):\n    print(i)",
    "output": "真正的大师总是需要规划之后再动手！我先做一个规划：\n### Planning\n1. 理解当前代码：当前循环使用`range(1, 11)`生成从1到10（包括1，不包括11）的数字，并按升序打印。\n2. 确定目标：需要改为打印从10到1的降序数字。\n3. 分析`range`函数：`range(start, stop, step)`可以接受三个参数，其中step为-1时可实现反向迭代。\n4. 确定新参数：起始值应为10（包含），终止值应为0（不包含），步长应为-1。\n5. 修改循环：将`range(1, 11)`改为`range(10, 0, -1)`。\n6. 验证：确保新循环能正确生成10, 9, 8, ..., 1。\n\n### Code\n\n#### Solving Sub-Tasks1: 理解当前代码行为\n```python\n# 当前代码：打印1到10（升序）\nfor i in range(1, 11):\n    print(i)\n```\n\n#### Solving Sub-Tasks2: 分析目标需求\n```plaintext\n需要输出：10, 9, 8, 7, 6, 5, 4, 3, 2, 1\n```\n\n#### Solving Sub-Tasks3: 设计新的range参数\n```python\n# 使用range(10, 0, -1)：\n# start=10（包含）, stop=0（不包含）, step=-1（递减）\n```\n\n#### Solving Sub-Tasks4: 修改循环并验证\n```python\n# 修改后的循环\nfor i in range(10, 0, -1):\n    print(i)\n```\n\n#### Final Code\n```python\nfor i in range(10, 0, -1):\n    print(i)\n```"
  },
]
```

接下来就是标准的 SFT 过程，和之前的 Cold-Start 的过程保持一致。

#### Result

目前未使用一些 Coding Benchmark 评测表现，因为 3B 的模型的数据确实不是很稳定，后续会在积累微调经验的基础上做更多微调的尝试性工作！

![](/images/fine-tune-result.png)


### GRPO for RL-FineTuning

This part to be done in the future.