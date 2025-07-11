---
title: Code Line Counter
date: 2025-05-28 13:10:47
index_img: /img/cover/linecount.jpg
excerpt: My Github project - Code Line counter.
categories:
  - Project
tags:
  - Python
  - Finished
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Code Line Counting Tool

# Repo

<a class="btn" href="https://github.com/xiyuanyang-code/Repo-Code-Line-Counting" title="title">Repo</a>

# Code Line Counting Tool: README

This tool counts the number of code lines in a repository, with options to ignore specific folders and filter by file extensions. It logs detailed results to both the console and a log file.

## Installation

1.  Clone the repository:
    ```bash
    git clone https://github.com/xiyuanyang-code/Repo-Code-Line-Counting.git
    cd Repo-Code-Line-Counting
    ```
2.  Install the package (preferably in a virtual environment):
    ```bash
    pip install .
    ```
    For development, you can install in editable mode:
    ```bash
    pip install -e .
    ```

## Usage

### Command Line Interface

After installation, you can use the `count-code` command directly in your terminal.

```bash
count-code [OPTIONS]
```

**Options:**

*   `--path PATH`: The path to the repository to be counted. Defaults to the current working directory (`.`).
*   `--file_type FILE_TYPE [FILE_TYPE ...]`: List of file extensions to include (e.g., `.py .cpp .h`). If not provided, all file types are counted.
*   `--ignored_dir IGNORED_DIR [IGNORED_DIR ...]`: List of subdirectory names to ignore. Defaults to common ignore directories (`.git`, `.history`, `.vscode`, `build`, `__pycache__`, `log`).
*   `--log_level {DEBUG,INFO,WARNING,ERROR,CRITICAL,NOTICE}`: Set the logging level for console output. Defaults to `NOTICE`.

**Examples:**

Count lines in the current directory (using default ignored directories and counting all file types):

```bash
count-code
```

Count only `.cpp` and `.hpp` files in a specific repository:

```bash
count-code --path /home/xiyuanyang/ACM_course_DS/Data_structure --file_type ".cpp" ".hpp"
```

Count lines ignoring the `docs` and `tests` directories:

```bash
count-code --ignored_dir "docs" "tests"
```

Run with DEBUG logging level:

```bash
count-code --log_level DEBUG
```

### As a Python Module

You can also import and use the `count_code_lines` function directly in your Python code.

```python
import os
from code_counter.counter import count_code_lines, setup_logging, NOTICE_LEVEL
import logging
from datetime import datetime

# Setup logging (optional, but recommended for consistent output)
timestamp = datetime.now().strftime("%Y%m%d_%H-%M-%S")
log_dir = "log"
if not os.path.exists(log_dir):
    os.makedirs(log_dir)
log_file_path = os.path.join(log_dir, f"code-counting-{timestamp}.log")
setup_logging(log_file_path, console_level=NOTICE_LEVEL)
logger = logging.getLogger(__name__)


# Example usage:
repo_path = "/home/xiyuanyang/Hodgepodge/repo_counting"
ignored_dirs = [".git", "log"]
file_types = [".py", ".md"]

total_lines, lines_per_extension = count_code_lines(repo_path, ignored_dirs, file_types)

logger.log(NOTICE_LEVEL, f"--- Module Usage Report for: {repo_path} ---")
logger.log(NOTICE_LEVEL, f"Total lines counted: {total_lines}")
logger.log(NOTICE_LEVEL, "Lines per extension:")
for ext, count in sorted(lines_per_extension.items(), key=lambda item: item[1], reverse=True):
    logger.log(NOTICE_LEVEL, f"  {ext}: {count}")
logger.log(NOTICE_LEVEL, "-------------------------------------")

# You can also access the raw results:
print(f"Raw total lines: {total_lines}")
print(f"Raw lines per extension: {lines_per_extension}")
```

## Log Output

Detailed logs, including file-by-file line counts and any errors encountered, are saved to a file in the `log/` directory (e.g., `log/code-counting-YYYYMMDD_HH-MM-SS.log`). Summary results are also printed to the console based on the specified log level.

## Todo

- Add support for file matching using regular expressions.

- Add some test.

# Code

- `cli.py`: using `argparse` to accept user's input. In simplest cases, user will only input `count-code` and this program will count all lines of code in current directory!

```python
import os
import logging
import argparse

from colorama import Style, Fore
from .counter import count_code_lines, setup_logging, NOTICE_LEVEL
from datetime import datetime

# Get logger for this module. Note: setup_logging configures the root logger,
# but it's good practice to get a named logger for each module.
logger = logging.getLogger(__name__)


def main():
    """
    Main function to parse arguments and run the code counting.
    """
    parser = argparse.ArgumentParser(
        description="Count the number of code lines in a repository."
    )

    parser.add_argument(
        "--path",
        type=str,
        default=os.getcwd(),
        help="The path to the repository to be counted. Defaults to the current working directory.",
    )

    parser.add_argument(
        "--file_type",
        type=str,
        default=None,
        nargs="+",
        help="List of file extensions to include (e.g., .py .cpp .h). If None, all file types are counted.",
    )

    parser.add_argument(
        "--ignored_dir",
        type=str,
        default=[
            ".git",
            ".history",
            ".vscode",
            "build",
            "__pycache__",
            "log",
        ],  # Set default ignored directories
        nargs="+",
        help="List of subdirectory names to ignore. Defaults to common ignore directories.",
    )

    parser.add_argument(
        "--log_level",
        type=str,
        default="NOTICE",
        choices=[
            "DEBUG",
            "INFO",
            "WARNING",
            "ERROR",
            "CRITICAL",
            "NOTICE",
        ],  # Add choices for validation
        help="Set the logging level for console output (e.g., DEBUG, INFO, WARNING, ERROR, CRITICAL, NOTICE). Defaults to NOTICE.",
    )

    args = parser.parse_args()

    # Setup logging based on parsed arguments
    timestamp = datetime.now().strftime("%Y%m%d_%H-%M-%S")
    log_dir = "log"
    if not os.path.exists(log_dir):
        os.makedirs(log_dir)
    log_file_path = os.path.join(log_dir, f"code-counting-{timestamp}.log")

    # Convert log level string to numeric level
    numeric_level = getattr(logging, args.log_level.upper(), None)
    if not isinstance(numeric_level, int):
        # Handle custom NOTICE_LEVEL if needed, or rely on setup_logging
        if args.log_level.upper() == "NOTICE":
            numeric_level = NOTICE_LEVEL
        else:
            print(f"Invalid log level: {args.log_level}. Using NOTICE.")
            numeric_level = NOTICE_LEVEL  # Fallback

    setup_logging(log_file_path, console_level=numeric_level)

    try:
        # Call the counting function with parsed arguments
        total, per_ext = count_code_lines(args.path, args.ignored_dir, args.file_type)

        logger.log(NOTICE_LEVEL, f"--- Code Counting Report for: {args.path} ---")
        logger.log(NOTICE_LEVEL, f"Total lines of code: {total}")
        logger.log(NOTICE_LEVEL, "Lines per extension:")
        # Sort by lines in descending order for better readability
        for ext, count in sorted(
            per_ext.items(), key=lambda item: item[1], reverse=True
        ):
            logger.log(NOTICE_LEVEL, f"  {ext}: {count}")
        logger.log(NOTICE_LEVEL, "-------------------------------------")
        print(total)
        print(Fore.GREEN + str(per_ext) + Style.RESET_ALL)

    except ValueError as e:
        logger.error(f"Error: {e}")
    except Exception as e:
        logger.critical(f"An unexpected error occurred: {e}")


if __name__ == "__main__":
    main()

```



- `counter.py`: the main component of this section.

```python
import os
import logging
import pathspec



NOTICE_LEVEL = 25
if not hasattr(logging, "NOTICE"):
    logging.addLevelName(NOTICE_LEVEL, "NOTICE")
    logging.NOTICE = NOTICE_LEVEL  # type: ignore

# Base logger for the module
logger = logging.getLogger(__name__)


def setup_logging(
    log_file_path=None, console_level=NOTICE_LEVEL, file_level=logging.DEBUG
):
    """
    Configures logging for the application.
    :param log_file_path: Optional path for the log file. If None, no file handler is added.
    :param console_level: Logging level for console output.
    :param file_level: Logging level for file output.
    """
    # Ensure handlers are not duplicated if setup_logging is called multiple times
    for handler in logger.handlers[:]:  # Iterate over a slice to allow modification
        logger.removeHandler(handler)

    logger.setLevel(min(console_level, file_level) if log_file_path else console_level)

    # Console handler
    console_handler = logging.StreamHandler()
    console_handler.setLevel(console_level)
    formatter = logging.Formatter("%(asctime)s - %(levelname)s - %(message)s")
    console_handler.setFormatter(formatter)
    logger.addHandler(console_handler)

    # File handler
    if log_file_path:
        file_handler = logging.FileHandler(log_file_path)
        file_handler.setLevel(file_level)
        file_handler.setFormatter(formatter)
        logger.addHandler(file_handler)


def load_gitignore_patterns(repo_path):
    """
    Load .gitignore patterns using pathspec, if available.
    Returns a PathSpec object or None if .gitignore does not exist or pathspec is not installed.
    """
    gitignore_path = os.path.join(repo_path, ".gitignore")
    if not os.path.isfile(gitignore_path) or pathspec is None:
        return None
    with open(gitignore_path, "r", encoding="utf-8") as f:
        patterns = f.read().splitlines()
    return pathspec.PathSpec.from_lines("gitwildmatch", patterns)


def is_ignored_by_gitignore(path, repo_path, gitignore_spec: pathspec.PathSpec):
    """
    Check if a path is ignored by .gitignore.
    """
    if gitignore_spec is None:
        return False
    # Make path relative to repo root
    rel_path = os.path.relpath(path, repo_path)
    return gitignore_spec.match_file(rel_path)


def count_code_lines(repo_path, ignored_dirs=None, file_types=None):
    """
    Count the number of code lines in a repository, with options to ignore folders and filter by file extensions.
    Also ignores files/directories specified in .gitignore if pathspec is installed.
    :param repo_path: Repository path
    :param ignored_dirs: List of directory names to ignore
    :param file_types: List of file extensions to include (e.g., ['.py', '.cpp']); if None, include all
    :return: (total_lines, lines_per_extension)
    """
    total_lines = 0
    lines_per_extension = {}
    default_ignored_dirs = [
        ".git",
        ".history",
        ".vscode",
        "build",
        "__pycache__",
        "log",
        "venv",
        ".DS_Store",
        "node_modules",
    ]

    # Default ignored directories if not provided
    if ignored_dirs is None:
        ignored_dirs = default_ignored_dirs
    else:
        ignored_dirs = list(set(default_ignored_dirs + ignored_dirs))

    # Load .gitignore patterns if possible
    gitignore_spec = load_gitignore_patterns(repo_path)

    if not os.path.isdir(repo_path):
        logger.error(f"Invalid path: {repo_path}")
        raise ValueError(
            f"Repository path does not exist or is not a directory: {repo_path}"
        )

    for root, dirs, files in os.walk(repo_path):
        # Remove directories to ignore in place
        dirs[:] = [d for d in dirs if d not in ignored_dirs]

        # Remove dirs ignored by .gitignore
        if gitignore_spec is not None:
            dirs[:] = [
                d
                for d in dirs
                if not is_ignored_by_gitignore(
                    os.path.join(root, d), repo_path, gitignore_spec
                )
            ]

        for file in files:
            file_path = os.path.join(root, file)
            file_name, ext = os.path.splitext(file)
            if ext == "":
                logger.debug(
                    f"Ignoring file without extension (likely a dotfile or binary): {file}"
                )
                continue

            # Ignore files matched by .gitignore
            if gitignore_spec is not None and is_ignored_by_gitignore(
                file_path, repo_path, gitignore_spec
            ):
                logger.debug(f"Skipping file ignored by .gitignore: {file_path}")
                continue

            # If file_types is specified, only count those extensions
            if (not file_types) or (
                ext.lower() in [ft.lower() for ft in file_types]
            ):  # Convert to lower for case-insensitivity
                try:
                    # Check if it's a symbolic link and skip to avoid infinite loops or errors
                    if os.path.islink(file_path):
                        logger.debug(f"Skipping symbolic link: {file_path}")
                        continue

                    # Attempt to open and read as text; will fail for true binaries
                    with open(file_path, "r", encoding="utf-8") as f:
                        lines = f.readlines()
                        num_lines = len(lines)
                        total_lines += num_lines
                        lines_per_extension[ext] = (
                            lines_per_extension.get(ext, 0) + num_lines
                        )
                        logger.debug(f"📄 {file_path}: {num_lines} lines")
                except UnicodeDecodeError:
                    logger.warning(f"Skipping binary or non-UTF-8 file: {file_path}")
                except Exception as e:
                    logger.error(f"Error reading {file_path}: {e}")

    return total_lines, lines_per_extension

```

