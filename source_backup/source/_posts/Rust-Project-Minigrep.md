---
title: 'Rust Project: Minigrep'
date: 2025-08-29 15:46:40
index_img: /img/cover/minigrep.jpg
excerpt: A simple, grep-like command-line tool written in Rust. It allows you to search for a specific string pattern within a text file. More capabilities will be added in the future.
categories:
  - Code
  - Rust
tags:
  - tutorial
  - Rustk
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>


# mini-grep

A simple, grep-like command-line tool written in Rust. It allows you to search for a specific string pattern within a text file.

{% note primary %}

Initial primary release! Will integrate more functions and capabilities in the future.

{% endnote %}

## Features

*   Search for a text pattern in a specified file.
*   Case-sensitive search by default.
*   Case-insensitive search can be enabled via an environment variable.

## Usage

To use `mini-grep`, you need to provide two arguments: a search pattern and the path to the file you want to search in.

### Basic Search (Case-Sensitive)

To perform a case-sensitive search, run the program with the following command structure:

```sh
cargo run -- <pattern> <file_path>
```

**Example:**

```sh
cargo run -- "hello" poem.txt
```

This command will search for the exact word "hello" in the `poem.txt` file and print any lines that contain it.

### Case-Insensitive Search

You can perform a case-insensitive search by setting the `IGNORE_CASE` environment variable before running the command.

On Linux/macOS:
```sh
IGNORE_CASE=1 cargo run -- <pattern> <file_path>
```

On Windows (PowerShell):
```powershell
$env:IGNORE_CASE=1; cargo run -- <pattern> <file_path>
```

**Example:**

```sh
IGNORE_CASE=1 cargo run -- "rUsT" poem.txt
```

This command will search for "rUsT" (and "rust", "Rust", "RUST", etc.) in `poem.txt` and print all matching lines, regardless of their casing.

## Todo List

- feat: add iterator for function programming
- Add recursive search for folders
- Add Regex search
- Add fuzzy search

## Code

{% note info %}

Just as a toy implementation... Still needs to be improved.

{% endnote %}

```rust
// main.rs
\use mini_grep::{Config, run};
use std::{env, process};

fn main() {
    let config = Config::build(env::args()).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments: {err}");
        process::exit(1);
    });
    println!("Searching for \"{}\" in file {}...", config.query, config.file_path);

    if let Err(e) = run(config) {
        eprintln!("Application error: {e}");
        process::exit(1);
    }
}
```

```rust
// lib.rs
use std::error::Error;
use std::{env, fs};
pub struct Config {
    pub query: String,
    pub file_path: String,
    pub ignore_case: bool,
}
impl Config {
    pub fn build(mut args: impl Iterator<Item = String>) -> Result<Config, &'static str> {
        args.next();
        let query = match args.next() {
            Some(arg) => arg,
            None => return Err("Didn't get a query string"),
        };
        let file_path = match args.next() {
            Some(arg) => arg,
            None => return Err("Didn't get a file path"),
        };
        let ignore_case = env::var("IGNORE_CASE").is_ok();
        Ok(Config {
            query,
            file_path,
            ignore_case,
        })
    }
}

pub fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let contents =
        fs::read_to_string(config.file_path).expect("Should have been able to read the file");
    let results = if config.ignore_case {
        search_case_insensitive(&config.query, &contents)
    } else {
        search(&config.query, &contents)
    };

    for line in results {
        println!("{line}");
    }
    Ok(())
}

// public core function: search for minigrep
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    contents
        .lines()
        .filter(|line| line.contains(&query))
        .collect()
}

// public core function: search while case sensitive
pub fn search_case_insensitive<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let query = query.to_lowercase();
    contents
        .lines()
        .filter(|line| line.to_lowercase().contains(&query))
        .collect()
}

// test module
#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn one_result() {
        let query = "duct";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.";
        assert_eq!(vec!["safe, fast, productive."], search(query, contents));
    }

    #[test]
    fn case_sensitive() {
        let query = "duct";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.
Duct tape.";
        assert_eq!(vec!["safe, fast, productive."], search(query, contents));
    }
    #[test]
    fn case_insensitive() {
        let query = "rUsT";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.
Trust me.";
        assert_eq!(
            vec!["Rust:", "Trust me."],
            search_case_insensitive(query, contents)
        );
    }
}
```