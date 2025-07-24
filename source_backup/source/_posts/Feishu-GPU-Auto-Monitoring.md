---
title: Feishu GPU Auto Monitoring
date: 2025-07-24 14:20:54
index_img: /img/cover/fs.jpg
excerpt: My Github project - Scripts for monitoring GPU usage on remote machine via feishu bot.
categories:
  - Project
tags:
  - Python
  - Automatic Scripts
  - Finished
---

# Feishu GPU Auto Monitoring

{% note primary %}

All the source code is available in [Github](https://github.com/xiyuanyang-code/Feishu-GPU-Auto-Monitoring)

{% endnote %}

This project provides a simple way to monitor GPU usage on remote servers and receive notifications through a Feishu bot. It connects to servers via SSH, retrieves GPU statistics using `nvidia-smi`, and sends formatted messages to a specified Feishu group.

## Features

- **Remote Monitoring:** Connects to multiple servers to gather GPU data.
- **Two Report Types:**
  1.  **Overall GPU Status:** Reports utilization and memory usage for all GPUs on a server.
  2.  **User-Specific Processes:** Lists all GPU processes for a specific user.
- **Feishu Integration:** Sends alerts and reports directly to a Feishu chat.
- **Configurable:** All settings, including server IPs, credentials, and Feishu bot details, are managed through a `config.yaml` file.


## Installation

-   Create a `config.yaml` file in the root directory.
-   Add the server details, user credentials, and Feishu bot information. See the example below for the required structure.

> [!TIP]
> You need to get **Feishu Webhook URL**, see [this blog](https://open.feishu.cn/community/articles/7271149634339422210) for more info.

Create a `config.yaml` file with the following structure:

```yaml
# list of ip to be monitored
ip_list:
  - "192.168.1.101"
  - "192.168.1.102"
# ssh port
port: 22
# ssh password
password: "your_ssh_password"
# ssh username
user: "your_ssh_username"

# feishu bot webhook url
feishu_url: "your_feishu_webhook_url"
# feishu keyword
feishu_keyword: "your_feishu_keyword"
```

## Usage

```bash
# running on tmux is recommended 
python -m src.run
```

All the logs will be decorated in `gpu_log` file.

## Demo

```json
{
    "NVIDIA GeForce RTX 3090": [
        {
            "id": "db94",
            "pid": 577961,
            "process_name": "python",
            "used_memory": "448 MiB"
        },
        {
            "id": "db95",
            "pid": 148218,
            "process_name": "python",
            "used_memory": "448 MiB"
        },
        {
            "id": "db19",
            "pid": 2871586,
            "process_name": "python",
            "used_memory": "450 MiB"
        },
        {
            "id": "db21",
            "pid": 3103532,
            "process_name": "python",
            "used_memory": "450 MiB"
        }
    ]
}
```

```
【Monitoring】
GPU for db93
0: 0%, Mem=21/24576 MiB
1: 0%, Mem=7132/24576 MiB
2: 0%, Mem=8664/24576 MiB
3: 0%, Mem=20804/24576 MiB
4: 0%, Mem=20804/24576 MiB
5: 0%, Mem=1/24576 MiB
6: 0%, Mem=1/24576 MiB
7: 0%, Mem=1/24576 MiB


GPU for db94
0: 100%, Mem=17248/24576 MiB
1: 0%, Mem=10468/24576 MiB
2: 0%, Mem=20814/24576 MiB
3: 0%, Mem=13020/24576 MiB
4: 0%, Mem=9492/24576 MiB
5: 100%, Mem=12128/24576 MiB
6: 0%, Mem=0/24576 MiB
7: 0%, Mem=0/24576 MiB

GPU for db95
0: 3%, Mem=454/24576 MiB
1: 0%, Mem=17288/24576 MiB
2: 0%, Mem=16256/24576 MiB
3: 0%, Mem=4/24576 MiB
4: 0%, Mem=1/24576 MiB
5: 0%, Mem=1/24576 MiB
6: 0%, Mem=1/24576 MiB
7: 0%, Mem=1/24576 MiB
```
# Code Demo

## Code Structure

```bash
.
├── README.md
├── config_template.yaml         # template configuration file
└── src
    ├── connect.py               # component of connecting to remote control via SSH
    ├── feishu_msg.py            # send message to predefined Feishu bot
    ├── gpu_stat.py              # split command line string to get GPU message info
    ├── logging_config.py        # loading config message, including username and password
    ├── run.py                   # automation scripts
    └── utils.py                 # several utility function
```

- `connect.py`

```python
import logging
import paramiko
import os

from src.logging_config import SHARED_LOGGER_NAME

logger = logging.getLogger(SHARED_LOGGER_NAME)

# !remove useless file
# def _load_ssh_config_and_connect_params(gpu_server_ip: str) -> dict:
#     """
#     Loads SSH configuration from ~/.ssh/config and returns connection parameters for the given host.
#     """
#     ssh_config = paramiko.SSHConfig()
#     user_config_file = os.path.expanduser("~/.ssh/config")
#     if os.path.exists(user_config_file):
#         try:
#             with open(user_config_file) as f:
#                 logger.info(f)
#                 ssh_config.parse(f)
#             logger.debug(f"Loaded SSH config from {user_config_file}")
#         except Exception as e:
#             logger.warning(f"Failed to parse SSH config file {user_config_file}: {e}")
#     else:
#         logger.debug(f"SSH config file not found at {user_config_file}")

#     return ssh_config.lookup(gpu_server_ip)


def _connect_to_ssh_server(
    hostname: str, port: int, username: str, password: str, timeout: int = 15
) -> paramiko.SSHClient | None:
    """
    Establishes an SSH connection to the specified server.

    Returns:
        paramiko.SSHClient: An active SSHClient instance if connection is successful, None otherwise.
    """
    logger.info(f"Attempting SSH connection to {username}@{hostname}:{port}...")
    ssh_client = paramiko.SSHClient()
    ssh_client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

    try:
        ssh_client.connect(
            hostname=hostname,
            username=username,
            port=port,
            password=password,
            timeout=timeout,
            sock=None,
        )
        logger.info("SSH connection successful.")
        return ssh_client
    except paramiko.AuthenticationException:
        logger.error(
            f"SSH Authentication failed for {username}@{hostname}:{port}. Check credentials."
        )
    except paramiko.SSHException as e:
        logger.error(f"SSH connection failed for {username}@{hostname}:{port}: {e}")
    except Exception as e:
        logger.error(
            f"An unexpected error occurred during SSH connection to {hostname}:{e}",
            exc_info=True,
        )
    return None


def _execute_remote_command(ssh_client: paramiko.SSHClient, command: str) -> str | None:
    """
    Executes a command on the remote SSH server and returns its stdout.
    Returns None if the command fails.
    """
    logger.info(f"Executing remote command: '{command}'")
    try:
        stdin, stdout, stderr = ssh_client.exec_command(command)
        exit_status = stdout.channel.recv_exit_status()

        if exit_status == 0:
            output_str = stdout.read().decode("utf-8").strip()
            logger.debug(
                f"Remote command output:\n{output_str}"
            )  # Use debug for verbose output
            return output_str
        else:
            error_message = stderr.read().decode("utf-8").strip()
            logger.error(
                f"Remote command '{command}' failed with exit status {exit_status}: {error_message}"
            )
            return None
    except Exception as e:
        logger.error(f"Error executing remote command '{command}': {e}", exc_info=True)
        return None
```

- `feishu_msg`:

```python
import requests
import logging
import paramiko

from src.logging_config import SHARED_LOGGER_NAME
from src.utils import read_config

logger = logging.getLogger(SHARED_LOGGER_NAME)
app_config = read_config("./config.yaml")
FEISHU_BOT_KEYWORD = app_config.get('feishu_keyword')
FEISHU_BOT_WEBHOOK_URL = app_config.get('feishu_url')


def send_feishu_message(message: str):
    """
    Sends a text message directly to the Feishu bot's Webhook.

    Args:
        message (str): The text message to send.

    Returns:
        bool: True if the message was sent successfully, False otherwise.
    """
    if FEISHU_BOT_KEYWORD not in message:
        message = f"【{FEISHU_BOT_KEYWORD}】{message}"

    headers = {"Content-Type": "application/json"}
    payload = {"msg_type": "text", "content": {"text": message}}

    logger.info(f"Sending message to Feishu: '{message}'")
    try:
        response = requests.post(
            FEISHU_BOT_WEBHOOK_URL, json=payload, headers=headers, timeout=10
        )
        response.raise_for_status()

        response_data = response.json()
        if response_data.get("code") == 0 or response_data.get("StatusCode") == 0:
            logger.info("Message sent to Feishu successfully.")
            return True
        else:
            logger.error(f"Failed to send message to Feishu. Response: {response_data}")
            return False

    except requests.exceptions.RequestException as e:
        logger.error(f"An error occurred while sending request to Feishu: {e}")
        return False
```

- `gpu_stat.py`:

```python
import logging
import paramiko
import subprocess
from typing import Optional, List, Tuple, Dict, Any

# Assuming these are available from your project structure
from src.logging_config import SHARED_LOGGER_NAME
from src.connect import (
    _execute_remote_command,
    _connect_to_ssh_server,
)

logger = logging.getLogger(SHARED_LOGGER_NAME)


def _parse_overall_gpu_stats_output(output_str: str) -> List[Tuple[int, int, int]]:
    """
    Parses the output string from nvidia-smi command for overall GPU stats
    into a list of GPU stats tuples (utilization, used_memory, total_memory).
    """
    results = []
    if not output_str:
        logger.warning(
            "No output received from nvidia-smi command to parse for overall stats."
        )
        return results

    for line in output_str.split("\n"):
        if not line:
            continue
        try:
            parts = line.split(",")
            if len(parts) == 3:
                util = int(parts[0].strip())
                mem_used = int(parts[1].strip())
                mem_total = int(parts[2].strip())
                results.append((util, mem_used, mem_total))
            else:
                logger.warning(
                    f"Skipping malformed nvidia-smi line: '{line}'. Expected 3 comma-separated values for overall stats."
                )
        except (ValueError, IndexError) as e:
            logger.warning(
                f"Could not parse nvidia-smi line '{line}' for overall stats: {e}"
            )
    return results


def _get_command_output(
    command: str, ssh_client: Optional[paramiko.SSHClient] = None
) -> Optional[str]:
    """
    Executes a shell command, either locally or remotely via SSH, and returns its stdout.
    Returns None if the command fails or no output is received.
    """
    if ssh_client:
        output_str = _execute_remote_command(ssh_client, command)
        if output_str is None:
            logger.error(f"Failed to retrieve output for remote command: {command}")
        return output_str
    else:
        try:
            result = subprocess.run(
                command, shell=True, capture_output=True, text=True, check=True
            )
            return result.stdout.strip()
        except subprocess.CalledProcessError as e:
            logger.error(f"Local command failed: {command}\nStderr: {e.stderr}")
            return None
        except FileNotFoundError:
            logger.error(f"Local command not found: {command}")
            return None


def _get_gpu_info(ssh_client: Optional[paramiko.SSHClient] = None) -> Dict[str, str]:
    """Get the mapping of GPU UUID to name, either locally or remotely."""
    command = "nvidia-smi --query-gpu=uuid,name --format=csv,noheader"
    try:
        output = _get_command_output(command, ssh_client)
        if output is None:
            return {}
        gpu_map = {}
        for line in output.split("\n"):
            if line:
                uuid, name = line.split(",", 1)
                gpu_map[uuid.strip()] = name.strip()
        return gpu_map
    except Exception as e:
        logger.error(f"Failed to get GPU info: {e}")
        return {}


def _get_pid_to_username_map(
    ssh_client: Optional[paramiko.SSHClient] = None,
) -> Dict[int, str]:
    """Get a mapping from all system process PIDs to usernames, either locally or remotely."""
    command = "ps -eo pid,user --no-headers"
    try:
        output = _get_command_output(command, ssh_client)
        if output is None:
            return {}
        pid_username_map = {}
        for line in output.split("\n"):
            if line:
                parts = line.strip().split()
                if len(parts) == 2:
                    try:
                        pid = int(parts[0])
                        username = parts[1]
                        pid_username_map[pid] = username
                    except ValueError:
                        pass  # Ignore non-numeric PIDs
        return pid_username_map
    except Exception as e:
        logger.error(f"Failed to get process usernames: {e}")
        return {}


def get_gpu_data(
    gpu_server_ip: Optional[str] = None,
    gpu_server_port: Optional[int] = None,
    gpu_server_user: Optional[str] = None,
    gpu_server_password: Optional[str] = None,
    target_username: Optional[str] = None,
) -> Any:
    """
    Retrieves either overall GPU statistics or specific user's GPU process information.
    Can operate locally or on a specified remote server.

    Args:
        gpu_server_ip (str, optional): The IP address or hostname of the GPU server.
                                       If None, attempts to get local GPU data.
        gpu_server_port (int, optional): The SSH port of the GPU server. Required if gpu_server_ip is provided.
        gpu_server_user (str, optional): The username for SSH connection. Required if gpu_server_ip is provided.
        gpu_server_password (str, optional): The password for SSH connection. Required if gpu_server_ip is provided.
        target_username (str, optional): The username whose GPU processes are to be retrieved.
                                         If None, returns overall GPU stats.
                                         If provided, returns process details for that user.

    Returns:
        If target_username is None:
            A list of tuples, where each tuple contains (compute_utilization_percent, used_memory_MiB, total_memory_MiB).
            Returns an empty list on failure.
        If target_username is provided:
            A dictionary where keys are GPU names (e.g., "NVIDIA GeForce RTX 3090") and values
            are lists of dictionaries, each representing a process:
            `{"pid": int, "process_name": str, "username": str, "used_memory": str}`.
            Returns an empty dictionary on failure.
    """
    ssh_client = None
    try:
        if gpu_server_ip:
            if not all([gpu_server_port, gpu_server_user, gpu_server_password]):
                logger.error(
                    "SSH connection details (port, user, password) are required for remote access."
                )
                return [] if target_username is None else {}
            ssh_client = _connect_to_ssh_server(
                gpu_server_ip, gpu_server_port, gpu_server_user, gpu_server_password
            )
            if not ssh_client:
                logger.error(f"Failed to establish SSH connection to {gpu_server_ip}.")
                return [] if target_username is None else {}
            logger.info(f"Connected to {gpu_server_ip} for GPU data.")
        else:
            logger.info("Getting GPU data from local machine.")

        if target_username is None:
            # Get overall GPU stats
            command = "nvidia-smi --query-gpu=utilization.gpu,memory.used,memory.total --format=csv,noheader,nounits"
            output_str = _get_command_output(command, ssh_client)
            if output_str is None:
                return []
            return _parse_overall_gpu_stats_output(output_str)
        else:
            # Get user-specific GPU processes
            nvidia_smi_command = "nvidia-smi --query-compute-apps=gpu_uuid,pid,process_name,used_memory --format=csv,noheader"
            gpu_process_lines_str = _get_command_output(nvidia_smi_command, ssh_client)
            if gpu_process_lines_str is None:
                return {}
            gpu_process_lines = gpu_process_lines_str.split("\n")

            pid_username_map = _get_pid_to_username_map(ssh_client)
            gpu_name_map = _get_gpu_info(ssh_client)

            user_gpu_data = {}

            for line in gpu_process_lines:
                if not line:
                    continue
                parts = line.split(",")
                if len(parts) == 4:
                    try:
                        gpu_uuid = parts[0].strip()
                        pid = int(parts[1].strip())
                        process_name = parts[2].strip()
                        used_memory = parts[3].strip()

                        username = pid_username_map.get(pid, "unknown_user")

                        if username == target_username:
                            if gpu_uuid not in user_gpu_data:
                                user_gpu_data[gpu_uuid] = []
                            user_gpu_data[gpu_uuid].append(
                                {
                                    "id": gpu_server_ip,
                                    "pid": pid,
                                    "process_name": process_name,
                                    # "username": username,
                                    "used_memory": used_memory,
                                }
                            )
                    except ValueError as e:
                        logger.warning(f"Error parsing NVIDIA-SMI line '{line}': {e}")
                else:
                    logger.warning(
                        f"Skipping malformed nvidia-smi process line: '{line}'. Expected 4 comma-separated values."
                    )

            final_display_data = {}
            for uuid, processes in user_gpu_data.items():
                display_name = gpu_name_map.get(uuid, f"Unknown GPU ({uuid})")
                final_display_data[display_name] = processes

            return final_display_data

    except Exception as e:
        logger.error(f"An error occurred while getting GPU data: {e}", exc_info=True)
        return [] if target_username is None else {}
    finally:
        if ssh_client:
            ssh_client.close()
            logger.info("SSH connection closed for GPU data retrieval.")
```

- `logging_config.py`:

{% note primary %}
This section can be reused in various projects.
{% endnote %}

```python
import logging
import os
import sys

# Define a constant for the shared logger name
SHARED_LOGGER_NAME = "gpu_monitor_app_logger"


def setup_logging_config():
    """
    Configures and returns a shared logger instance.
    Ensures that the configuration is done only once to avoid duplicate handlers.
    """
    # Attempt to get the existing logger instance
    logger = logging.getLogger(SHARED_LOGGER_NAME)

    # Check if the logger has already been configured with handlers; if so, return it directly.
    # This prevents adding handlers multiple times if setup_logging_config() is called more than once.
    if logger.handlers:
        return logger

    # If the logger is not configured, proceed with configuration.
    logger.setLevel(logging.INFO)  # Set the minimum logging level for the logger.

    log_dir_home = os.path.join(os.path.expanduser("~"), "gpu_scripts", "gpu_log")
    log_file_path = None

    try:
        os.makedirs(log_dir_home, exist_ok=True)
        potential_log_file_path = os.path.join(log_dir_home, "gpu_monitor.log")

        # Try to create or open the file to check for write permissions.
        with open(potential_log_file_path, "a") as f:
            f.write("")  # Try to write an empty string to ensure writability.
        log_file_path = potential_log_file_path
    except OSError as e:
        print(
            f"Warning: Could not create or write to log file at '{log_dir_home}'. Using /tmp instead. Error: {e}",
            file=sys.stderr,
        )
        log_file_path = os.path.join("/tmp", "gpu_monitor.log")
        try:
            with open(log_file_path, "a") as f:
                f.write("")  # Try again to ensure writability in /tmp.
        except OSError as e:
            print(
                f"Critical Warning: Could not create or write to log file in /tmp. File logging will be disabled. Error: {e}",
                file=sys.stderr,
            )
            log_file_path = None  # Could not write to file, disabling file logging.

    # Define the log format.
    formatter = logging.Formatter("%(asctime)s %(levelname)s [%(name)s]: %(message)s")

    # File Handler
    if log_file_path:
        file_handler = logging.FileHandler(log_file_path, encoding="utf-8")
        file_handler.setLevel(
            logging.INFO
        )  # The file handler records INFO level and above.
        file_handler.setFormatter(formatter)
        logger.addHandler(file_handler)

    # Console Handler
    console_handler = logging.StreamHandler(
        sys.stdout
    )  # Explicitly direct output to stdout.
    console_handler.setLevel(
        logging.WARNING
    )  # The console handler only records WARNING level and above.
    console_handler.setFormatter(formatter)
    logger.addHandler(console_handler)

    # Disable propagation to prevent log events from being passed to the root logger, which would cause duplicate output.
    logger.propagate = False

    return logger
```

- `run.py`:

```python
import time
import json

from src.logging_config import setup_logging_config
from src.gpu_stat import get_gpu_data
from src.feishu_msg import send_feishu_message
from src.utils import generate_timestamp, read_config

app_config = read_config("./config.yaml")
gpu_lists = app_config.get("ip_list")
port = app_config.get("port")
password = app_config.get("password")
user = app_config.get("user") 

def run_gpu_stat():
    # initialize logging components
    logger = setup_logging_config()
    message_sent = []

    for ip in gpu_lists:
        logger.info(f"Attempting to get GPU stats for {ip}...")
        gpu_stats = get_gpu_data(ip, port, user, password)
        message = ""

        if gpu_stats:
            logger.info(f"Successfully retrieved GPU stats for {ip}:")
            message += f"\nGPU for {ip}"
            for i, (util, used_mem, total_mem) in enumerate(gpu_stats):
                info_msg = f"{i}: {util}%, Mem={used_mem}/{total_mem} MiB"
                logger.info(info_msg)
                message += "\n" + info_msg
        else:
            logger.error(f"Failed to retrieve GPU stats for {ip}.")

        message_sent.append(message)

    result_string = "\n".join(message_sent)
    send_feishu_message(result_string)


def run_process_check():
    # initialize logging components
    logger = setup_logging_config()
    gpu_info = []

    for ip in gpu_lists:
        logger.info(f"Attempting to get GPU stats for {ip}...")
        gpu_stats = get_gpu_data(ip, port, user, password, target_username=user)

        if gpu_stats != {}:
            gpu_info.append(gpu_stats)

    merged_gpu_data = {}

    for d in gpu_info:
        for gpu_name, processes_list in d.items():
            if gpu_name in merged_gpu_data:
                merged_gpu_data[gpu_name].extend(processes_list)
            else:
                merged_gpu_data[gpu_name] = processes_list

    with open(f"./gpu_log/json_log/info_{generate_timestamp()}.json", "w") as file:
        json_string = json.dumps(merged_gpu_data, indent=4, sort_keys=True)
        file.write(json_string)
        file.close()
    
    send_feishu_message(f"Current time: {generate_timestamp()}:\n" + json_string)


def main():
    while True:
        marker = 1
        start_time = time.strftime("%Y-%m-%d %H:%M:%S")

        print(f"[INFO] Run at {start_time}")
        # =============RUNNING PROCESS=============
        run_process_check()
        if marker == 1:
            run_gpu_stat()
            pass
        marker = 1 - marker
        # =============RUNNING PROCESS=============
        print("[INFO] Sleeping for 1 hour...")

        try:
            time.sleep(3600)
        except KeyboardInterrupt:
            print("[INFO] Interrupted by user. \nExiting.")
            break
    pass


if __name__ == "__main__":
    main()
```

- `utils.py`:

```python
from datetime import datetime
import os
import yaml

# several preprocessing
os.makedirs("./gpu_log", exist_ok=True)
os.makedirs("./gpu_log/json_log", exist_ok=True)


def generate_timestamp():
    return datetime.now().strftime("%Y%m%d-%H:%M:%S")

def read_config(file_path = "./config.yaml"):
    """Reads a YAML configuration file and returns its content as a Python dictionary."""
    if not os.path.exists(file_path):
        print(f"Error: Configuration file not found at '{file_path}'")
        return None
    try:
        with open(file_path, 'r', encoding='utf-8') as file:
            config = yaml.safe_load(file)
            return config
    except yaml.YAMLError as e:
        print(f"Error parsing YAML file: {e}")
        return None
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
        return None

if __name__ == "__main__":
    config_file = 'config.yaml'
    app_config = read_config(config_file)

    if app_config:
        print("Configuration loaded successfully:")
        print(f"IP List: {app_config.get('ip_list')}")
        print(f"Password: {app_config.get('password')}")
        print(f"User: {app_config.get('user')}")
        print(f"Feishu URL: {app_config.get('feishu_url')}")
        print(f"Feishu Keyword: {app_config.get('feishu_keyword')}")

        first_ip = app_config['ip_list'][0]
        print(f"\nFirst IP in list: {first_ip}")
```