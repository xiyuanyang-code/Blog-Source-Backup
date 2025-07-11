---
title: CamelAI-automatic-essay-modification
date: 2025-04-11 14:40:50
index_img: /img/cover/autogenwritingen.jpg
excerpt: My Github project English Essay Revision using the agentic workflow.
categories:
  - Project
tags:
  - Agents
  - Essay Writing
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>
# English Essay Revision based on Camel-AI Agentic Framework

## Introduction

This is an English blog editing system that uses Camel-AI[^1] as an agent automation framework. This feature enables automated article enhancement, including organizing logic, improving word choice, and correcting grammar.

This is my first attempt at building a complex (maybe?) multi-agent system! I hope you can have fun!

## File Structure

All the files are demonstrated:

- `README.md`

- `data`

  - `Final.txt`: The final text (To be done)

  - `Original.txt`: The original text (replace it with your own!)

- `src`

  - `construct.py`: Construct files, reading original passage, etc.

  - `main.py`: The main scripts, coding the agents' society.

  - `prompts.py`: Recording all the prompts and system messages for the agents.

- `.gitignore`

- `requirements.txt`

## Usage

### Installation

Install requirements via:

```bash
pip install -r requirements.txt
```

Then you can just run the files and enjoy the world of Agents!😊

### Demo

> The video to be done

## Codes

### `main.py`

```python
import os
import dotenv
import construct
import prompts as prompts

from pydantic import BaseModel
from typing import List
from camel.models import ModelFactory
from camel.types import ModelPlatformType, ModelType, TaskType, RoleType
from camel.configs import ChatGPTConfig
from camel.agents import ChatAgent, TaskPlannerAgent
from camel.messages import BaseMessage
from camel.prompts import TextPrompt
from camel.societies import RolePlaying
from camel.societies.workforce import Workforce
from camel.toolkits import ThinkingToolkit, SearchToolkit, HumanToolkit, FunctionTool
from camel.tasks import Task


# Load api key and url
dotenv.load_dotenv()
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
BASE_URL = os.getenv("BASE_URL")

# define prompts and models
system_message = prompts.total_system_message
base_model = ModelFactory.create(
    model_platform=ModelPlatformType.OPENAI,
    model_type=ModelType.GPT_4O_MINI,
    model_config_dict=ChatGPTConfig().as_dict(),
    api_key=OPENAI_API_KEY,
    url=BASE_URL,
)

# For the settings of base agents
single_agent = ChatAgent(
    system_message=system_message,
    model=base_model,
    message_window_size=10,
    tools=[
        # *SearchToolkit().get_tools(),
        # *ThinkingToolkit().get_tools(),
        # *HumanToolkit().get_tools(),
    ],
)

# Add several prompts for the single agents
topic_message = BaseMessage(
    role_name="User",
    role_type=RoleType.USER,
    content=f"The text topic is {prompts.text_topic}",
    meta_dict={},
)

initial_reuqirements = BaseMessage(
    role_name="User",
    role_type=RoleType.USER,
    content=f"The Initial requirements are as follows: {prompts.requirements}",
    meta_dict={},
)

original_text = construct.read_file("data/Original.txt")
# print(original_text)
original_passage = BaseMessage(
    role_name="User",
    role_type=RoleType.USER,
    content=f"""the text of my argumentative essay is given below:\n\n\
        =================ORIGINAL passage BEGIN================\n\
            {original_text}\n\
        =================ORIGINAL passage END================
            """,
    meta_dict={},
)


class ResponseFormat(BaseModel):
    whole_text_after_edited: str
    Modification_summary: str


max_length = 250


# Act for the single agents
def run_single_agents():
    single_agent.record_message(initial_reuqirements)
    single_agent.record_message(topic_message)
    single_agent.record_message(original_passage)
    UserMessage = "Start revising the essay and generate a report"
    response = single_agent.step(UserMessage, response_format=ResponseFormat)
    # print(type(response))
    print(response.msgs[0].content)


# Define the Agentic workflow
work_force = Workforce(
    description="Automated English Essay Revision",
    new_worker_agent_kwargs={"model": base_model},
    coordinator_agent_kwargs={"model": base_model},
    task_agent_kwargs={"model": base_model},
)

# Define worker
# search_tool = FunctionTool(SearchToolkit.search_baidu(max_results=6))

# search_agent = ChatAgent(
#     system_message=system_message
#     + "For your specified task, you need to search the web for more information and provide them with the downstream agents",
#     model=base_model,
#     message_window_size=10,
#     tools=[search_tool],
# )

task_agent = ChatAgent(
    model=base_model,
    system_message=f"""
            The total task: {prompts.total_prompt}
            

            You are an expert in task decomposition. Your responsibilities:
            1. Analyze requirements: \n{prompts.requirements}\n
            2. Read the original passage, the passage is shown below\n:{original_text}\n
            3. You need to provide a more specific modification plan based on the requirements, 
            combining it with the original text, but without making specific changes. 
            For example: the relative clause in a certain sentence does not conform to specific grammatical rules and needs to be revised; 
            or the word choice in a certain part is too simplistic and needs to be optimized; 
            or the logic in a certain section needs to be further strengthened.
            4. More detailed modification requirements are needed, covering all aspects such as grammar, logic, word choice, and sentence structure.
            

            Response format:
            ### Specific Requirements
            [Return specified and modified requirements]

            """,
    message_window_size=10,
)

conservative_agent = ChatAgent(
    system_message=f"""

            {prompts.total_prompt}\n
            You will receive the specific requirements from the previous agents.\n
            The original text: {original_text}\n

            Attention!!!, particularly for you, as a more meticulous writer, your revisions should focus on the logic and organizational structure of the article, making it more coherent.
            
            Provide complete edited text and brief feedback (<50 words).
            Ensure native English usage and <{1.2 * max_length} word limit.
            
            Response format:
            ### Version ###
            [full edited text]
            
            ### Feedback ###
            [comments]
            """,
    model=base_model,
    message_window_size=10,
)

imaginative_agent = ChatAgent(
    system_message=f"""

            {prompts.total_prompt}\n
            You will receive the specific requirements from the previous agents.\n
            The original text: {original_text}\n

            Attention!!!, particularly for you, as a more meticulous writer, your revisions should focus on the logic and organizational structure of the article, making it more coherent.
            
            Provide complete edited text and brief feedback (<50 words).
            Ensure native English usage and <{1.2 * max_length} word limit.
            
            Response format:
            ### Version ###
            [full edited text]
    
            ### Feedback ###
            [comments]
            """,
    model=base_model,
    message_window_size=10,
)

integrator_agent = ChatAgent(
    system_message=f"""
            {prompts.total_prompt}\n
            You are the final integrator. Your responsibilities:
            1.You will receive three documents: the original article and two modified articles by two editors(one conservative and one creative)
            2.You need to take an overall perspective to compare the highlights of the two revised drafts against the original manuscript, and integrate the two articles, taking the strengths from each.
            3.!!Attention: You need to make sure your passage (after integrated) is no more than {1.5 * max_length} words.

            Response format:
            ### Final Version ###
            [text after integrated]
            
            ### Feedback ###
            [comments]
            """,
    model=base_model,
    message_window_size=10,
)

reporter_agent = ChatAgent(
    system_message=f"""
            {prompts.total_prompt},\n
            The original article is: {original_text}\n
            You are the final reporter, you will receive the final scripts modified, and make the last modifications:
            1. Make sure all your modifications adhere to the English Usage.
            2. Make sure the total length is no more than {max_length} words.
            
            Response format:
            ### Final version ###
            [final text]

            ### Feedback ###
            In this section, you are asked to generate a report about the modifications between the final version and the original version.
            """,
    model=base_model,
    message_window_size=10,
)

# Add WorkNode
work_force.add_single_agent_worker(
    description="Specify the task into subtasks",
    worker=task_agent,
).add_single_agent_worker(
    description="Make some modifications conservatively",
    worker=conservative_agent,
).add_single_agent_worker(
    description="Make some modifications imaginaively",
    worker=imaginative_agent,
).add_single_agent_worker(
    description="Integrate all the modifications",
    worker=integrator_agent,
).add_single_agent_worker(
    description="Generate the modifications report",
    worker=reporter_agent,
)

def run_workforce():
    task = Task(
        content=prompts.total_task,
        id = "0",
    )

    task = work_force.process_task(task)
    print(task.result)

if __name__ == "__main__":
    # If you want to use the single agent...
    run_single_agents()

    # If you want to use the multiagent framework
    run_workforce()

    
```

### `prompts.py`

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-11 14:59:16
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-12 23:39:41
FilePath: /Autogen-English-Essay/Requirement.py
Description: All the prompot will be stored and written here.
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

# System message, for all agents
total_system_message = "You are an expert at writing argumentative essay of English and make revisions, \
                        make sure all your generations and modifications are logical and aligns with the habits of native English speakers.\
                        Think twice before you give your final answers."

text_topic = "Beyond internal evaluation systems, should students be allowed to post public comments and ratings about their teachers online, like people do with restaurant reviews?"

requirements = "1.Check the spelling and grammar mistakes for the whole passage.\
    2.Organize the article's ideas to make the language more fluid, the arguments more distinct, and the sentence structures more complex. But remember don’t make too many corrections! Just do some essential optimization.\
    3.Ensure that all of your modifications adhere to the conventions of English usage\
    4.Generate a report regarding your modification, including the ultimate modified passage and the detailed descriptions.\
    Ensure that all of your modifications adhere to the conventions of English usage. "

specified_requirements = ""
# Wait after the task_decomposer finish

total_task = f"The total task for you to be solved is to modify an argumentative essay based on specific topic and instructions.\
             [topic]: {text_topic}\
             [requirments]: {requirements}."

# Thus this prompts will be passed to all agents.
total_prompt = f"{total_system_message}\n\
                 The topic of the essay is {text_topic}\n\
                 {total_task}\n\
                 Try your best!"

if __name__ == "__main__":
    print("Testing...")
else:
    print("Importing all prompts...")
```

### `construct.py`

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-11 14:50:06
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-13 11:20:52
FilePath: /Autogen-English-Essay/construct.py
Description:
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

# Constructing file structures.

import os
from datetime import *


def read_file(filename="Original.txt"):
    """_summary_ :read file contents, especially for getting the original text

    Args:
        filename (str): file name for the original text
    """
    with open(filename, "r", encoding="utf-8") as file:
        return file.read().strip()


def write_file(content: str, filename="Final.txt"):
    """_summary_ :write file contents, especially for getting the original text

    Args:
        filename (str): file name for the original text
        content (str): contents to be written into
    """
    with open(filename, "w", encoding="utf-8") as file:
        file.write(content)


def create_dirs(log_folder: str):
    """_summary_: create the log folder for the settings

    Args:
        log_folder (str): The name of the file folder
    """
    # create the 'log' file folder if it doesn't exist
    if not os.path.exists(log_folder):
        # create new folder
        print("Constructing new folder...")
        os.makedirs(log_folder)
    else:
        print("Constructed already.")

    print("Finish!")


def get_log_filename(log_dir="log") -> str:
    """Generate timestamp-based log filename"""
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    return os.path.join(log_dir, f"log_{timestamp}.txt")


def log_conversation(message: str, log_dir="log"):
    """Record conversation to log file"""
    log_file = get_log_filename(log_dir)
    with open(log_file, "a", encoding="utf-8") as f:
        f.write(message + "\n\n")


def print_progress(message: str):
    """Print progress message and log it"""
    print(f"[PROGRESS] {message}")
    log_conversation(f"[SYSTEM] {message}")


if __name__ == "__main__":
    print("Testing...")
else:
    print("Constructing all files...")

```

## References

[^1]: [Camel_AI](https://github.com/camel-ai/camel)
