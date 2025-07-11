---
title: LLM Evaluating
date: 2025-05-30 15:31:49
index_img: /img/cover/llmeva.png
excerpt: Initial try for LLM evaluating on gsm8k dataset.
categories:
  - Artificial Intelligence
tags:
  - Tutorial
  - Finished
  - LLM
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# LLM Evaluating (from scratch)

## Introduction

本文将会**演示从零开始**如何部署构建自己的本地（或者在线API调用）LLM并使用Hugging Face上的数据集进行跑分评估，也作为笔者初入LLM领域的学习笔记之一。

相关代码开源在 [我的仓库](https://github.com/xiyuanyang-code/LLM_Learn/tree/master/evaluation) 中。

## Table of contents

我们的代码结构如下：

```plaintext
.
├── data
├── img
├── results
└── src
    ├── app.py
    ├── download.py
    ├── evaluate.py
    ├── main.py
    └── utils.py
```

可以看到

## Downloading Datasets

我们使用**Hugging Face**下载数据集，为了简单，我们使用`openai/gsm8k`的经典数据集，来评测本地大模型`Llama-3 8B`的评分表现。

```python
def download_dataset(dataset="openai/gsm8k"):
    """download the dataset online and revert to json file"""
    ds = load_dataset(dataset, "main")

    for split in ds:
        data = list(ds[split])

        with open(f"{split}.json", "w", encoding="utf-8") as f:
            json.dump(data, f, ensure_ascii=False, indent=2)
```

上面的函数使用了`dataset`库，故需要加上`from datasets import load_dataset`来导入相关的库函数。接着讲这个内容存储为`json`格式存储在本地之后读取。

当然，对于较大的数据集，也可以直接下载到本地为json格式，参见 [Hugging Face数据集下载](https://xiyuanyang-code.github.io/posts/My-Memo/#Hugging-Face-%E6%95%B0%E6%8D%AE%E9%9B%86%E4%B8%8B%E8%BD%BD)。

## Preparing Models

### Downloading Models and activating

我们在这里使用**Llama3-8B**的中小型模型，使用本地部署（建议至少有一块30系以上的显卡），部署的详细过程参见：

[Datawhale 大模型本地部署](https://github.com/datawhalechina/self-llm/blob/master/models/LLaMA3/01-LLaMA3-8B-Instruct%20FastApi%20%E9%83%A8%E7%BD%B2%E8%B0%83%E7%94%A8.md)

### Preparing API keys

对于闭源大模型和缺乏硬件支持的玩家来说，使用**API密钥**进行LLM调用是非常方便且高效的选择。

对于国内玩家，非常推荐使用 [智增增](https://gpt.zhizengzeng.com/#/my/) 作为API接口的第三方平台。在这个网站上申请得到API Key之后，我们就可以准备激活自己的LLM了。

首先，我们需要在当前目录下创建一个`.env`文件，用于后续读取`OPENAI_API_KEY`和`BASE_URL`，当然，你也可以直接写在环境变量之中，一劳永逸。

```python
def init_env_file(
    env_path=".env",
    default_content='OPENAI_API_KEY="1234567 (replace with your own)"\nBASE_URL="example.chat (replace with your own)"',
):
    if not os.path.exists(env_path):
        with open(env_path, "w", encoding="utf-8") as f:
            f.write(default_content)
        print("WARNING, remember to use your own api secret key")


def load_api_key():
    # init api file
    init_env_file()

    import dotenv
    dotenv.load_dotenv(override=True)
    OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
    BASE_URL = os.getenv("BASE_URL")
    # print(OPENAI_API_KEY)
    # print(BASE_URL)
    return OPENAI_API_KEY, BASE_URL
```

接下来，我们可以实现`app.py`即构建LLM回话的接口函数。

```python
import os
import requests
import json
from utils import load_api_key


def get_completions_online(prompt):
    from openai import OpenAI

    OPENAI_API_KEY, BASE_URL = load_api_key()
    # get prompts:
    added_prompts = "For the final answer you have given , please return: <answer>Your final answer</answer> at the end of your response, remember you are only allowed to return a float number! For example, 18 or 18.0 is allowed but $18 is not allowed"

    client = OpenAI(api_key=OPENAI_API_KEY, base_url=BASE_URL)
    resp = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": prompt + added_prompts},
        ],
    )
    return resp.choices[0].message.content


def get_completions_offline(prompt):
    """get completions from the local host LLMs

    Args:
        prompt (str): Given prompts into the LLM
        max_length (int, optional): max length ofn single query. Defaults to 100.

    Returns:
        str: final response from the model
    """
    headers = {"Content-Type": "application/json"}
    max_length = 100

    # get prompts:
    added_prompts = "For the final answer you have given , please return: <answer>Your final answer</answer> at the end of your response, remember you are only allowed to return a float number! For example, 18 or 18.0 is allowed but $18 is not allowed"

    data = {"prompt": prompt + added_prompts, "max_length": max_length}
    response = requests.post(
        url="http://127.0.0.1:6005",
        headers=headers,
        data=json.dumps(data),
        proxies={"http": None, "https": None},
    )

    return response.json().get("response", "No response from model")


def get_completions(prompt, type: str = "offline"):
    if type == "online":
        return get_completions_online(prompt)
    elif type == "offline":
        return get_completions_offline(prompt)


if __name__ == "__main__":
    print(get_completions_online("Hello, introduce yourself"))

```

## Evaluating

准备好了LLM和数据集，接下来我们该给LLM评测了，相关评测的代码写在了`evaluat.py`函数里面。

```python
import requests
import json
import re

from tqdm import tqdm
from app import get_completions


def test_evaluate(dataset):
    """a small test function for testing LLM's response

    Args:
        dataset (List): the loaded dataset from function load_local_dataset
    """
    get_completions("Hello, introduce yourself!")
    test_results = []
    single_evaluate(test_results, dataset[0])
    print(test_results)


def single_evaluate(results: list, item, status):
    """single evaluation for single query

    Args:
        results (list): result list
        item (dict): a single query from dataset
    """
    prompt: str = item["question"]
    expected_answer: str = item["answer"]

    # get LLM's response
    llm_response: str = get_completions(prompt, status)

    match = re.search(r"<answer>(.*?)</answer>(.*)", llm_response)

    # exception handling
    if match is None:
        final_answer = "LLM fails to response"
        results.append(
            {
                "prompt": prompt,
                "expected_answer": expected_answer,
                "model_response": llm_response,
                "final_answer": final_answer,
                # None means LLM fails to output in the right format
                "correct": None,
            }
        )
    else:
        final_answer = float(match.group(1).replace(",", ""))
        results.append(
            {
                "prompt": prompt,
                "expected_answer": expected_answer,
                "model_response": llm_response,
                "final_answer": final_answer,
                "correct": final_answer
                == float(expected_answer.split("####")[-1].strip().replace(",", "")),
            }
        )


# run tests and evaluate models
def evaluate(datasets, status):
    results = []
    for item in tqdm(datasets, desc="Evaluating"):
        single_evaluate(results=results, item=item, status=status)
    return results


def calculate_metrics(results):
    total = len(results)
    correct = sum(1 for result in results if result["correct"] is True)
    no_answer = sum(1 for result in results if result["correct"] is None)
    accuracy = correct / total if total > 0 else 0
    no_answer_rate = no_answer / total if total > 0 else 0
    return {
        "total": total,
        "correct": correct,
        "accuracy": accuracy,
        "no_answer_rate": no_answer_rate,
    }

```

## Main Function

```python
import argparse
import json
from datetime import datetime
from download import download_dataset, download_model, load_local_datasets
from utils import construct_folder, check_construct, split_print
from evaluate import evaluate, calculate_metrics


def main(status):
    print("Welcome! It is LLM 101")

    # section 1: construct and preparations
    folders = ["data", "src", "img", "results"]
    construct_folder(folders)
    assert check_construct(folders)

    # section 2: activate LLMs
    # ! let just skip that part
    # model_dir = download_model()
    # ! please run the model in the certain port, see https://github.com/datawhalechina/self-llm/blob/master/models/LLaMA3/01-LLaMA3-8B-Instruct%20FastApi%20%E9%83%A8%E7%BD%B2%E8%B0%83%E7%94%A8.md for reference
    # ! we assume that your model is available in localhost: 6005

    # section 3: download_data
    # download_dataset()
    datasets = load_local_datasets("data/test.json")

    # section 4: evaluate
    timestamp = datetime.now().strftime("%Y%m%d%H%M")
    results = evaluate(datasets, status=status)
    ans = calculate_metrics(results=results)

    split_print()
    print("Accuracy: ", ans["accuracy"])
    print("No answer rate: ", ans["no_answer_rate"])
    split_print()

    # section 5: save answers
    result_path = f"results/ans_{timestamp}.json"
    response_path = f"results/response_{timestamp}.json"

    with open(result_path, "w", encoding="utf-8") as f:
        json.dump(ans, f, ensure_ascii=False, indent=2)

    with open(response_path, "w", encoding="utf-8") as f:
        json.dump(results, f, ensure_ascii=False, indent=2)

    print(f"Results saved to {result_path}")
    print(f"All the responses for the LLM saved to {response_path}")


if __name__ == "__main__":
    # add argparse
    parser = argparse.ArgumentParser(description="Add arguments for LLM evaluating")

    parser.add_argument(
        "--type", type=str, default="online", help="ways to load LLM, online or offline"
    )
    args = parser.parse_args()

    status = "online" if args.type == "online" else "offline"
    main(status)

```

# Another Example!

这次，我们部署**残血版**的DeepSeek模型（16B的模型，其实还是偏小的）

同样，我们在HuggingFace下载好DeepSeek模型之后，使用如下的`app.py`代码启动监听端口：

```python
from fastapi import FastAPI, Request
from transformers import AutoTokenizer, AutoModelForCausalLM, GenerationConfig
import uvicorn
import json
import datetime
import torch
import os

os.environ["CUDA_VISIBLE_DEVICES"] = "3,4,5,6"
# Set device parameters
DEVICE = "cuda"  # Use CUDA
DEVICE_ID = "0"  # CUDA device ID, leave empty if not set
CUDA_DEVICE = (
    f"{DEVICE}:{DEVICE_ID}" if DEVICE_ID else DEVICE
)  # Combine CUDA device info


# Function to clear GPU memory
def torch_gc():
    if torch.cuda.is_available():  # Check if CUDA is available
        with torch.cuda.device(CUDA_DEVICE):  # Specify CUDA device
            torch.cuda.empty_cache()  # Clear CUDA cache
            torch.cuda.ipc_collect()  # Collect CUDA memory fragments


# Create FastAPI app
app = FastAPI()


# Endpoint to handle POST requests
@app.post("/")
async def create_item(request: Request):
    global model, tokenizer  # Declare global variables for model and tokenizer
    json_post_raw = await request.json()  # Get JSON data from POST request
    json_post = json.dumps(json_post_raw)  # Convert JSON data to string
    json_post_list = json.loads(json_post)  # Convert string to Python object
    prompt = json_post_list.get("prompt")  # Get prompt from request
    max_length = json_post_list.get("max_length")  # Get max_length from request

    # Build messages
    messages = [{"role": "user", "content": prompt}]
    # Build input tensor
    input_tensor = tokenizer.apply_chat_template(
        messages, add_generation_prompt=True, return_tensors="pt"
    )
    # Generate output from model
    outputs = model.generate(input_tensor.to(model.device), max_new_tokens=max_length)
    result = tokenizer.decode(
        outputs[0][input_tensor.shape[1] :], skip_special_tokens=True
    )

    now = datetime.datetime.now()  # Get current time
    time = now.strftime("%Y-%m-%d %H:%M:%S")  # Format time as string
    # Build response JSON
    answer = {"response": result, "status": 200, "time": time}
    # Build log information
    log = (
        "["
        + time
        + "] "
        + '", prompt:"'
        + prompt
        + '", response:"'
        + repr(result)
        + '"'
    )
    print(log)  # Print log
    torch_gc()  # Perform GPU memory cleanup
    return answer  # Return response


# Main entry point
if __name__ == "__main__":
    model_name_or_path = (
        "/GPFS/rhome/xiyuanyang/local_LLM/deepseek-ai/deepseek-moe-16b-chat"
    )
    # Load pretrained tokenizer and model
    tokenizer = AutoTokenizer.from_pretrained(
        model_name_or_path, trust_remote_code=True
    )
    model = AutoModelForCausalLM.from_pretrained(
        model_name_or_path,
        trust_remote_code=True,
        torch_dtype=torch.bfloat16,
        device_map="auto",
    )
    model.generation_config = GenerationConfig.from_pretrained(model_name_or_path)
    model.generation_config.pad_token_id = model.generation_config.eos_token_id
    model.eval()  # Set model to evaluation mode
    # Start FastAPI app
    # Using port 6006 allows port mapping from autodl to local, so the API can be used locally
    uvicorn.run(
        app, host="0.0.0.0", port=6000, workers=1
    )  # Start app on specified host and port

```

接着你就可以看到：

```plaintext
Loading checkpoint shards: 100%|████████████████████████████████████████████████████████████| 7/7 [00:19<00:00,  2.73s/it]
INFO:     Started server process [525302]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:6000 (Press CTRL+C to quit)
```

使用FastAPI在特定的监听端口监听：

```python
import requests
import json


def get_completion(prompt, max_length):
    headers = {"Content-Type": "application/json"}
    data = {"prompt": prompt, "max_length": max_length}
    response = requests.post(
        url="http://127.0.0.1:6000", headers=headers, data=json.dumps(data)
    )
    return response.json()["response"]


if __name__ == "__main__":
    prompt = input()
    content: str = get_completion(prompt, 10000000)
    contents = content.strip().split("\n")
    for con in contents:
        print(con)

```

这样就可以实现最基本的LLM和用户交互的逻辑。

```plaintext
Please input your prompts.😘
I am a student learning computer science and artificial intelligence. I want to learn the basic knowledge of front-back end developing in the summer vacation and become an excellent developer in the future, can you arrange me a plan for 40 dayas to improve my developing skills? I want to improve myself in aspects of how to conduct product research and task-planning, basic knowlwdge of front-back end developing and software engineering, generate a whole report for me. Thank you!
Sure, I can help you create a plan to improve your front-end and back-end developing skills and gain knowledge in product research and task planning. Here's a 4-step plan:

1. Learn the basics of front-end and back-end development:
* Front-end development: HTML, CSS, JavaScript, and frameworks like React, Angular, or Vue.js.
* Back-end development: Python, Java, or Ruby on Rails, and databases like MySQL, MongoDB, or PostgreSQL.
* Learn how to use version control systems like Git and GitHub.
2. Conduct product research and task planning:
* Learn how to conduct market research and user research to understand your target audience and their needs.
* Learn how to create user personas, user stories, and user journeys to understand user behavior and create a user-centered design.
* Learn how to create a project roadmap, including milestones, deadlines, and tasks.
3. Learn software engineering principles:
* Learn how to write clean, maintainable, and scalable code.
* Learn how to use design patterns and best practices for software development.
* Learn how to use agile methodologies like Scrum or Kanban for project management.
4. Generate a whole report:
* Create a report that summarizes your learning and skills gained during the summer vacation.
* Include a section on front-end and back-end development, including the technologies and frameworks you learned.
* Include a section on product research and task planning, including the methods and tools you used.
* Include a section on software engineering principles, including the design patterns and best practices you learned.
* Include a conclusion that summarizes your learning and future goals.

I hope this plan helps you improve your front-end and back-end developing skills and gain knowledge in product research and task planning. Good luck with your summer vacation!
```

