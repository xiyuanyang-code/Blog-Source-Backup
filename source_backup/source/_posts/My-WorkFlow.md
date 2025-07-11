---
title: My-WorkFlow
date: 2025-02-11 11:30:05
hide: false
excerpt: This blog is about some efficiency tools I use daily, which help me build my workflow.
tags: 
  - Announcement
  - Finished
index_img: /img/cover/toolchain.jpg
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

![Welcome to Xiyuan Yang's Blog!](https://s1.imagehub.cc/images/2025/02/07/329668c81128e08f84c1d1ea140bc31b.jpg)

# My WorkFlow

## About this Blog

As a student of Computer Science and Artificial Intelligence, **efficiency** is of great importance! In this Blog below, I will recommend several efficient tools I have experienced and used during my exploration. This Blog will be updated permanently, not only for a long-time sharing project, but also for a systematic review of my personal workflow.  

My working environment:

- Microsoft Windows 11, x64-based PC
	- Intel(R) Core(TM) Ultra 5 125H (14 cores)
	- Intel(R) Arc(TM) Graphics
- For WSL:
	- WSL2: Ubuntu 22.04.1 LTS,Jammy

> I plan to buy a **MacBook** when I grow older, but who knows?

## Core Principles

**How to improve efficiency?** Here are several core principles I have concluded during my learning exploration:

- **Improving efficiency is the ultimate goal**.
- **Avoid Repetitive Labors**.
- **There is no best, only the most suitable**.

We will dive into these principles deeply later.

> **However**, it's worth mentioning the difference between **learning** and **working**. Learning is a process where input and absorption take up the main part, while working is mainly for output and producing fruitful contents.
>
> For both working and learning, **efficiency** is of great importance, but the core principles may differ from each other based on its different goals and methods. For example, repetitive work may be helpful in learning methods in some circumstances.
>
> This is another topic! We can discuss it further in the future. We may just **focusing on several core principles above** in today's Blog.

## Coding

**Integrated Development Environment (IDE)** is of great importance for every programmer.

### Visual Studio Code

<img src="https://media2.dev.to/dynamic/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F0sbafazseadoy4jupixj.png" alt="Vscode" style="zoom: 50%;" />

https://code.visualstudio.com/

Visual Studio Code (VS Code) is a free, open-source code editor supporting multiple programming languages. It offers powerful features like **IntelliSense, debugging, Git integration, and extensions**. Cross-platform for Windows, macOS, and Linux, it boasts a clean interface and high performance, making it a top choice for developers.

### Cursor

https://www.cursor.com/

Cursor is an AI-powered code editor designed for modern developers. It integrates **AI assistance for code completion, refactoring, and debugging, enhancing productivity.** Built on VS Code's foundation, it supports multiple languages and frameworks. Its sleek interface, real-time collaboration, and AI-driven insights make it a cutting-edge tool for efficient coding.

> For Learning, I often use Vscode only to practice my programming skills. For work, I will use **Cursor and Vscode** both. AI assistance is of great convenience, but it will interrupt my train of thought while coding.

## Command Line

**I strongly recommend everyone to use command line skillfully in our daily lives**.

### Windows Terminal

I have build a similar terminal environment based on this [Blog](https://xxyqwq.github.io/2024/01/27/build-windows-terminal/).[^1]

**Tools used**: **oh-my-posh** + **powerlevel10k**

To make **powershell** more likely to **Bash-like language**, I set some alias and customed functions as below, you can modify your config files by using commands below:

```powershell
code $PROFILE
```

It will open default editor (for me, it's cursor) and you can modify and custom settings freely!

```powershell
# my custom settings for powershell
oh-my-posh init pwsh --config "C:\Users\29349\powerlevel10k_lean.omp.json" | Invoke-Expression
Import-Module posh-git
Import-Module PSReadLine
Set-PSReadLineOption -PredictionSource History
Set-PSReadLineKeyHandler -Chord "Ctrl+RightArrow" -Function ForwardWord



Remove-Item -Path Alias:ls -Force
Remove-Item -Path Alias:cd -Force
Remove-Item -Path Alias:pwd -Force
Remove-Item -Path Alias:rm -Force
Remove-Item -Path Alias:cp -Force
Remove-Item -Path Alias:mv -Force
Remove-Item -Path Alias:cat -Force
Remove-Item -Path Alias:ps -Force
Remove-Item -Path Alias:kill -Force
Remove-Item -Path Alias:clear -Force
Remove-Item -Path Alias:man -Force


# 覆盖内置别名（强制覆盖）
Set-Alias -Name ls -Value Get-ChildItem -Force
Set-Alias -Name cd -Value Set-Location -Force
Set-Alias -Name pwd -Value Get-Location -Force
Set-Alias -Name rm -Value Remove-Item -Force
Set-Alias -Name cp -Value Copy-Item -Force
Set-Alias -Name mv -Value Move-Item -Force
Set-Alias -Name cat -Value Get-Content -Force
Set-Alias -Name ps -Value Get-Process -Force
Set-Alias -Name kill -Value Stop-Process -Force
Set-Alias -Name clear -Value Clear-Host -Force
Set-Alias -Name man -Value Get-Help -Force
Set-Alias -Name which -Value Get-Command -Force

# 定义函数代替别名（用于需要参数的命令）
function mkdir {
    param(
        [string]$Path
    )
    New-Item -ItemType Directory -Path $Path
}

function find {
    param(
        [string]$Path
    )
    Get-ChildItem -Recurse -Path $Path
}

# 定义函数以支持参数传递
function ls {
    param(
        [string]$Options
    )
    if ($Options -eq "-l") {
        Get-ChildItem | Format-Table -AutoSize
    } else {
        Get-ChildItem
    }
}

function grep {
    param(
        [string]$Pattern,
        [string]$Path
    )
    Select-String -Pattern $Pattern -Path $Path
}

function touch {
    param(
        [string]$Path
    )
    if (-Not (Test-Path $Path)) {
        New-Item -ItemType File -Path $Path
    } else {
        (Get-Item $Path).LastWriteTime = Get-Date
    }
}

# 其他常用函数
function ll {
    Get-ChildItem | Format-Table -AutoSize
}

function la {
    Get-ChildItem -Force | Format-Table -AutoSize
}

function .. {
    Set-Location ..
}

function ... {
    Set-Location ..\..
}

function ~ {
    Set-Location ~
}

# 重新加载配置文件
function reload {
    . $PROFILE
}


```

### Linux Terminal

**My version**: **Ubuntu 22.04**

I use **Oh-my-zsh** and **Powerlevel-10k** for my custom settings[^2]. The famous course in MIT: [Missing semester of my CS education](https://missing.csail.mit.edu/) includes a lecture[^3] teaching you to customize your dotfiles to make your shell more shell more powerful.

https://ohmyz.sh/

https://github.com/romkatv/powerlevel10k

You can search github for **dotfiles** where you can peek others' custom settings and migrate to your own settings. It's also a great way to learn Bash commands and improve proficiency step by step while practicing.

> It's common for green hands to make mistakes! Don't be afraid, try to spend more time and conquer it! 

My dotfiles for zsh settings are as below.

```bash
vim ~/.zshrc
# entering vim and make your custom settings
source ~/.zshrc  #run the files or you can just reopen the terminal
```

```bash
# Files for zshrc
# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
# Initialization code that may require console input (password prompts, [y/n]
# confirmations, etc.) must go above this block; everything else may go below.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

# If you come from bash you might have to change your $PATH.
# export PATH=$HOME/bin:$HOME/.local/bin:/usr/local/bin:$PATH

# Path to your oh-my-zsh installation.
export ZSH="$HOME/.oh-my-zsh"

# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
ZSH_THEME="powerlevel10k/powerlevel10k"

# Set list of themes to pick from when loading at random
# Setting this variable when ZSH_THEME=random will cause zsh to load
# a theme from this variable instead of looking in $ZSH/themes/
# If set to an empty array, this variable will have no effect.
# ZSH_THEME_RANDOM_CANDIDATES=( "robbyrussell" "agnoster" )

# Uncomment the following line to use case-sensitive completion.
#plugins=(zsh-syntax-highlighting)# CASE_SENSITIVE="true"

# Uncomment the following line to use hyphen-insensitive completion.
# Case-sensitive completion must be off. _ and - will be interchangeable.
# HYPHEN_INSENSITIVE="true"

# Uncomment one of the following lines to change the auto-update behavior
# zstyle ':omz:update' mode disabled  # disable automatic updates
# zstyle ':omz:update' mode auto      # update automatically without asking
# zstyle ':omz:update' mode reminder  # just remind me to update when it's time

# Uncomment the following line to change how often to auto-update (in days).
# zstyle ':omz:update' frequency 13

# Uncomment the following line if pasting URLs and other text is messed up.
# DISABLE_MAGIC_FUNCTIONS="true"

# Uncomment the following line to disable colors in ls.
# DISABLE_LS_COLORS="true"

# Uncomment the following line to disable auto-setting terminal title.
# DISABLE_AUTO_TITLE="true"

# Uncomment the following line to enable command auto-correction.
# ENABLE_CORRECTION="true"

# Uncomment the following line to display red dots whilst waiting for completion.
# You can also set it to another string to have that shown instead of the default red dots.
# e.g. COMPLETION_WAITING_DOTS="%F{yellow}waiting...%f"
# Caution: this setting can cause issues with multiline prompts in zsh < 5.7.1 (see #5765)
# COMPLETION_WAITING_DOTS="true"

# Uncomment the following line if you want to disable marking untracked files
# under VCS as dirty. This makes repository status check for large repositories
# much, much faster.
# DISABLE_UNTRACKED_FILES_DIRTY="true"

# Uncomment the following line if you want to change the command execution time
# stamp shown in the history command output.
# You can set one of the optional three formats:
# "mm/dd/yyyy"|"dd.mm.yyyy"|"yyyy-mm-dd"
# or set a custom format using the strftime function format specifications,
# see 'man strftime' for details.
# HIST_STAMPS="mm/dd/yyyy"

# Would you like to use another custom folder than $ZSH/custom?
# ZSH_CUSTOM=/path/to/new-custom-folder

# Which plugins would you like to load?
# Standard plugins can be found in $ZSH/plugins/
# Custom plugins may be added to $ZSH_CUSTOM/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(git zsh-syntax-highlighting)

source $ZSH/oh-my-zsh.sh

# User configuration

# export MANPATH="/usr/local/man:$MANPATH"

# You may need to manually set your language environment
# export LANG=en_US.UTF-8

# Preferred editor for local and remote sessions
# if [[ -n $SSH_CONNECTION ]]; then
#   export EDITOR='vim'
# else
#   export EDITOR='mvim'
# fi

# Compilation flags
# export ARCHFLAGS="-arch x86_64"

# Set personal aliases, overriding those provided by oh-my-zsh libs,
# plugins, and themes. Aliases can be placed here, though oh-my-zsh
# users are encouraged to define aliases within the ZSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
# alias zshconfig="mate ~/.zshrc"
# alias ohmyzsh="mate ~/.oh-my-zsh"
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/xiyuanyang/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/xiyuanyang/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/xiyuanyang/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/xiyuanyang/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

export PATH="$HOME/.cargo/bin:$PATH"

eval $(thefuck --alias)
# You can use whatever you want as an alias, like for Mondays:
eval $(thefuck --alias FUCK)




export PATH="$HOME/.cargo/bin:$PATH"


source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh

# setting for alias here
alias cls='clear'
alias ll='la -lah'
alias v='vim'
alias gc='git commit'
alias gs='git status'
alias mv='mv -i'
alias df='df -h'
alias ..='cd ..'


# 设置默认字符串颜色为淡蓝色
ZSH_HIGHLIGHT_STYLES[default]='fg=#87CEEB'

# 设置管道颜色为淡粉色
ZSH_HIGHLIGHT_STYLES[pipe]='fg=#FFB6C1'

# 设置命令颜色为淡紫色
ZSH_HIGHLIGHT_STYLES[command]='fg=#DDA0DD'

# 设置别名颜色为淡橙色
ZSH_HIGHLIGHT_STYLES[alias]='fg=#FFDAB9'

# 设置路径颜色为淡黄色
ZSH_HIGHLIGHT_STYLES[path]='fg=#FFFACD'

# 设置错误颜色为淡红色
ZSH_HIGHLIGHT_STYLES[error]='fg=#FFCCCB'

# 设置内置命令颜色为淡紫色
ZSH_HIGHLIGHT_STYLES[builtin]='fg=#DDA0DD'

```

> For usage of Vim, readers can skip to my Blog for basic vim-tutorial.[^4]
>
> https://xiyuanyang-code.github.io/posts/Vim-tutorial/

#### Several Packages in Ubuntu

The block below shows all the packages I have installed manually. You can also use the list below to check your installation.

```bash
 touch target_filenames.txt
 apt-mark showmanual > target_filenames.txt
 cat target_filenames.txt #all files that have been installed in Ubuntu,including system files.
 touch all_packages.txt
 dpkg --list > all_packages.txt
 cat all_packages.txt
 touch final_answers.txt
 grep -Ff target_filenames.txt all_packages.txt > final_answers.txt
 cat final_answers.txt
```

```bash
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
ii  alsa-topology-conf            1.2.5.1-2                               all          ALSA topology configuration files
ii  alsa-ucm-conf                 1.2.6.3-1ubuntu1.12                     all          ALSA Use Case Manager configuration files
ii  base-files                    12ubuntu4.2                             amd64        Debian base system miscellaneous files
ii  base-passwd                   3.5.52build1                            amd64        Debian base system master password and group files
ii  bash                          5.1-6ubuntu1.1                          amd64        GNU Bourne Again SHell
ii  bash-completion               1:2.11-5ubuntu1                         all          programmable completion for the bash shell
ii  bc                            1.07.1-3build1                          amd64        GNU bc arbitrary precision calculator language
ii  binutils-common:amd64         2.38-4ubuntu2.6                         amd64        Common files for the GNU assembler, linker and binary utilities
ii  bsdutils                      1:2.37.2-4ubuntu3.4                     amd64        basic utilities from 4.4BSD-Lite
ii  build-essential               12.9ubuntu3                             amd64        Informational list of build-essential packages
ii  bzip2                         1.0.8-5build1                           amd64        high-quality block-sorting file compressor - utilities
ii  cargo                         1.75.0+dfsg0ubuntu1~bpo0-0ubuntu0.22.04 amd64        Rust package manager
ii  cmake                         3.22.1-1ubuntu1.22.04.2                 amd64        cross-platform, open-source make system
ii  cmake-data                    3.22.1-1ubuntu1.22.04.2                 all          CMake data files (modules, templates and documentation)
ii  command-not-found             22.04.0                                 all          Suggest installation of packages in interactive bash sessions
ii  cpio                          2.13+dfsg-7ubuntu0.1                    amd64        GNU cpio -- a program to manage archives of files
ii  curl                          7.81.0-1ubuntu1.20                      amd64        command line tool for transferring data with URL syntax
ii  dash                          0.5.11+git20210903+057cd650a4ed-3build1 amd64        POSIX-compliant shell
ii  debconf                       1.5.79ubuntu1                           all          Debian configuration management system
ii  debconf-i18n                  1.5.79ubuntu1                           all          full internationalization support for debconf
ii  deborphan                     1.7.35                                  amd64        program that can find unused packages, e.g. libraries
ii  diffutils                     1:3.8-0ubuntu2                          amd64        File comparison utilities
ii  distro-info-data              0.52ubuntu0.7                           all          information about the distributions' releases (data files)
ii  dosfstools                    4.2-1build3                             amd64        utilities for making and checking MS-DOS FAT filesystems
ii  e2fsprogs                     1.46.5-2ubuntu1.1                       amd64        ext2/ext3/ext4 file system utilities
ii  fd-find                       8.3.1-1ubuntu0.1                        amd64        Simple, fast and user-friendly alternative to find
ii  file                          1:5.41-3ubuntu0.1                       amd64        Recognize the type of data in a file using "magic" numbers
ii  findutils                     4.8.0-1ubuntu3                          amd64        utilities for finding files--find, xargs
ii  gdb                           12.1-0ubuntu1~22.04.2                   amd64        GNU Debugger
ii  git                           1:2.34.1-1ubuntu1.12                    amd64        fast, scalable, distributed revision control system
ii  git-man                       1:2.34.1-1ubuntu1.12                    all          fast, scalable, distributed revision control system (manual pages)
ii  gnupg-l10n                    2.2.27-3ubuntu2.1                       all          GNU privacy guard - localization files
ii  grep                          3.7-1build1                             amd64        GNU grep, egrep and fgrep
ii  gzip                          1.10-4ubuntu4.1                         amd64        GNU compression utilities
ii  hostname                      3.23ubuntu2                             amd64        utility to set/show the host name or domain name
ii  htop                          3.0.5-7build2                           amd64        interactive processes viewer
ii  init                          1.62                                    amd64        metapackage ensuring an init system is installed
ii  init-system-helpers           1.62                                    all          helper tools for all init systems
ii  libalgorithm-diff-perl        1.201-1                                 all          module to find differences between files
ii  libalgorithm-diff-xs-perl     0.04-6build3                            amd64        module to find differences between files (XS accelerated)
ii  libasound2-data               1.2.6.1-1ubuntu1                        all          Configuration files and profiles for ALSA drivers
ii  libatk1.0-data                2.36.0-3build1                          all          Common files for the ATK accessibility toolkit
ii  libaudit-common               1:3.0.7-1build1                         all          Dynamic library for security auditing - common files
ii  libavahi-common-data:amd64    0.8-5ubuntu5.2                          amd64        Avahi common data files
ii  libbz2-1.0:amd64              1.0.8-5build1                           amd64        high-quality block-sorting file compressor library - runtime
ii  libc-ares2:amd64              1.18.1-1ubuntu0.22.04.3                 amd64        asynchronous name resolver
ii  libc-bin                      2.35-0ubuntu3.9                         amd64        GNU C Library: Binaries
ii  libc-dev-bin                  2.35-0ubuntu3.9                         amd64        GNU C Library: Development binaries
ii  libc-devtools                 2.35-0ubuntu3.9                         amd64        GNU C Library: Development tools
ii  libc6:amd64                   2.35-0ubuntu3.9                         amd64        GNU C Library: Shared libraries
ii  libc6-dbg:amd64               2.35-0ubuntu3.9                         amd64        GNU C Library: detached debugging symbols
ii  libc6-dev:amd64               2.35-0ubuntu3.9                         amd64        GNU C Library: Development Libraries and Header Files
ii  libc6-i386                    2.35-0ubuntu3.9                         amd64        GNU C Library: 32-bit shared libraries for AMD64
ii  libcairo-gobject2:amd64       1.16.0-5ubuntu2                         amd64        Cairo 2D vector graphics library (GObject library)
ii  libcairo2:amd64               1.16.0-5ubuntu2                         amd64        Cairo 2D vector graphics library
ii  libcanberra0:amd64            0.30-10ubuntu1.22.04.1                  amd64        simple abstract interface for playing event sounds
ii  libcap-ng0:amd64              0.7.9-2.2build3                         amd64        An alternate POSIX capabilities library
ii  libcap2:amd64                 1:2.44-1ubuntu0.22.04.1                 amd64        POSIX 1003.1e capabilities (library)
ii  libcap2-bin                   1:2.44-1ubuntu0.22.04.1                 amd64        POSIX 1003.1e capabilities (utilities)
ii  libcbor0.8:amd64              0.8.0-2ubuntu1                          amd64        library for parsing and generating CBOR (RFC 7049)
ii  libcc1-0:amd64                12.3.0-1ubuntu1~22.04                   amd64        GCC cc1 plugin for GDB
ii  libcolord2:amd64              1.4.6-1                                 amd64        system service to manage device colour profiles -- runtime
ii  libcom-err2:amd64             1.46.5-2ubuntu1.1                       amd64        common error description library
ii  libcrypt-dev:amd64            1:4.4.27-1                              amd64        libcrypt development files
ii  libcrypt1:amd64               1:4.4.27-1                              amd64        libcrypt shared library
ii  libcryptsetup12:amd64         2:2.4.3-1ubuntu1.1                      amd64        disk encryption support - shared library
ii  libctf-nobfd0:amd64           2.38-4ubuntu2.6                         amd64        Compact C Type Format library (runtime, no BFD dependency)
ii  libctf0:amd64                 2.38-4ubuntu2.6                         amd64        Compact C Type Format library (runtime, BFD dependency)
ii  libcups2:amd64                2.4.1op1-1ubuntu4.11                    amd64        Common UNIX Printing System(tm) - Core library
ii  libcurl3-gnutls:amd64         7.81.0-1ubuntu1.20                      amd64        easy-to-use client-side URL transfer library (GnuTLS flavour)
ii  libcurl4:amd64                7.81.0-1ubuntu1.20                      amd64        easy-to-use client-side URL transfer library (OpenSSL flavour)
ii  libdebconfclient0:amd64       0.261ubuntu1                            amd64        Debian Configuration Management System (C-implementation library)
ii  libdebuginfod1:amd64          0.186-1build1                           amd64        library to interact with debuginfod (development files)
ii  libdrm-common                 2.4.110-1ubuntu1                        all          Userspace interface to kernel DRM services -- common files
ii  libelf1:amd64                 0.186-1build1                           amd64        library to read and write ELF files
ii  libext2fs2:amd64              1.46.5-2ubuntu1.1                       amd64        ext2/ext3/ext4 file system libraries
ii  libfile-fcntllock-perl        0.22-3build7                            amd64        Perl module for file locking with fcntl(2)
ii  libfreetype6:amd64            2.11.1+dfsg-1ubuntu0.2                  amd64        FreeType 2 font engine, shared library files
ii  libgcc-11-dev:amd64           11.4.0-1ubuntu1~22.04                   amd64        GCC support library (development files)
ii  libgdbm-compat4:amd64         1.23-1                                  amd64        GNU dbm database routines (legacy support runtime version) 
ii  libgdbm6:amd64                1.23-1                                  amd64        GNU dbm database routines (runtime version) 
ii  libgdk-pixbuf2.0-common       2.42.8+dfsg-1ubuntu0.3                  all          GDK Pixbuf library - data files
ii  libglib2.0-data               2.72.4-0ubuntu2.4                       all          Common files for GLib library
ii  libgtk-3-common               3.24.33-1ubuntu2.2                      all          common files for the GTK graphical user interface library
ii  libldap-common                2.5.16+dfsg-0ubuntu0.22.04.2            all          OpenLDAP common files for libraries
ii  liblocale-gettext-perl        1.07-4build3                            amd64        module using libc functions for internationalization in Perl
ii  libmagic-mgc                  1:5.41-3ubuntu0.1                       amd64        File type determination library using "magic" numbers (compiled magic file)
ii  libmagic1:amd64               1:5.41-3ubuntu0.1                       amd64        Recognize the type of data in a file using "magic" numbers - library
ii  libnsl-dev:amd64              1.3.0-2build2                           amd64        libnsl development files
ii  libpcre2-8-0:amd64            10.39-3ubuntu0.1                        amd64        New Perl Compatible Regular Expression Library- 8 bit runtime files
ii  libpcre3:amd64                2:8.39-13ubuntu0.22.04.1                amd64        Old Perl 5 Compatible Regular Expression Library - runtime files
ii  libplymouth5:amd64            0.9.5+git20211018-1ubuntu3              amd64        graphical boot animation and logger - shared libraries
ii  libpython3-dev:amd64          3.10.6-1~22.04.1                        amd64        header files and a static library for Python (default)
ii  libpython3.10-dev:amd64       3.10.12-1~22.04.8                       amd64        Header files and a static library for Python (v3.10)
ii  librsvg2-2:amd64              2.52.5+dfsg-3ubuntu0.2                  amd64        SAX-based renderer library for SVG files (runtime)
ii  librsvg2-common:amd64         2.52.5+dfsg-3ubuntu0.2                  amd64        SAX-based renderer library for SVG files (extra runtime)
ii  librtmp1:amd64                2.4+20151223.gitfa8646d.1-2build4       amd64        toolkit for RTMP streams (shared library)
ii  libsemanage-common            3.3-1build2                             all          Common files for SELinux policy management libraries
ii  libsource-highlight-common    3.1.9-4.1build2                         all          architecture-independent files for source highlighting library
ii  libstd-rust-dev:amd64         1.75.0+dfsg0ubuntu1~bpo0-0ubuntu0.22.04 amd64        Rust standard libraries - development files
ii  libstdc++-11-dev:amd64        11.4.0-1ubuntu1~22.04                   amd64        GNU Standard C++ Library v3 (development files)
ii  libtcl8.6:amd64               8.6.12+dfsg-1build1                     amd64        Tcl (the Tool Command Language) v8.6 - run-time library files
ii  libthai-data                  0.1.29-1build1                          all          Data files for Thai language support library
ii  libtirpc-common               1.3.2-2ubuntu0.1                        all          transport-independent RPC library - common files
ii  libtirpc-dev:amd64            1.3.2-2ubuntu0.1                        amd64        transport-independent RPC library - development files
ii  libvorbisfile3:amd64          1.3.7-1build2                           amd64        high-level API for Vorbis General Audio Compression Codec
ii  libwebp7:amd64                1.2.2-2ubuntu0.22.04.2                  amd64        Lossy compression of digital photographic images
ii  libxkbcommon0:amd64           1.4.0-1                                 amd64        library interface to the XKB compiler - shared library
ii  linux-libc-dev:amd64          5.15.0-131.141                          amd64        Linux Kernel Headers for development
ii  login                         1:4.8.1-2ubuntu2.2                      amd64        system login tools
ii  logsave                       1.46.5-2ubuntu1.1                       amd64        save the output of a command in a log file
ii  lsb-base                      11.1.0ubuntu4                           all          Linux Standard Base init script functionality
ii  lshw                          02.19.git.2021.06.19.996aaad9c7-2build1 amd64        information about hardware configuration
ii  lsof                          4.93.2+dfsg-1.1build2                   amd64        utility to list open files
ii  make                          4.3-4.1build1                           amd64        utility for directing compilation
ii  media-types                   7.0.0                                   all          List of standard media types and their usual file extension
ii  motd-news-config              12ubuntu4.2                             all          Configuration for motd-news shipped in base-files
ii  mount                         2.37.2-4ubuntu3.4                       amd64        tools for mounting and manipulating filesystems
ii  ncurses-base                  6.3-2ubuntu0.1                          all          basic terminal type definitions
ii  ncurses-bin                   6.3-2ubuntu0.1                          amd64        terminal-related programs and man pages
ii  ncurses-term                  6.3-2ubuntu0.1                          all          additional terminal type definitions
ii  net-tools                     1.60+git20181103.0eebece-1ubuntu5       amd64        NET-3 networking toolkit
ii  nodejs                        12.22.9~dfsg-1ubuntu3.6                 amd64        evented I/O for V8 javascript - runtime executable
ii  nodejs-doc                    12.22.9~dfsg-1ubuntu3.6                 all          API documentation for Node.js, the javascript platform
ii  openssh-server                1:8.9p1-3ubuntu0.10                     amd64        secure shell (SSH) server, for secure access from remote machines
ii  pastebinit                    1.5.1-1ubuntu1                          all          command-line pastebin client
ii  patch                         2.7.6-7build2                           amd64        Apply a diff file to an original
ii  plymouth                      0.9.5+git20211018-1ubuntu3              amd64        boot animation, logger and I/O multiplexer
ii  plymouth-theme-ubuntu-text    0.9.5+git20211018-1ubuntu3              amd64        boot animation, logger and I/O multiplexer - ubuntu text theme
ii  procps                        2:3.3.17-6ubuntu2.1                     amd64        /proc file system utilities
ii  psmisc                        23.4-2build3                            amd64        utilities that use the proc file system
ii  python3-dev                   3.10.6-1~22.04.1                        amd64        header files and a static library for Python (default)
ii  python3-gdbm:amd64            3.10.8-1~22.04                          amd64        GNU dbm database support for Python 3.x
ii  python3-pip                   22.0.2+dfsg-1ubuntu0.5                  all          Python package installer
ii  python3-setuptools            59.6.0-1.2ubuntu0.22.04.2               all          Python3 Distutils Enhancements
ii  python3-wadllib               1.3.6-1                                 all          Python 3 library for navigating WADL files
ii  python3-zipp                  1.0.0-3ubuntu0.1                        all          pathlib-compatible Zipfile object wrapper - Python 3.x
ii  python3.10-dev                3.10.12-1~22.04.8                       amd64        Header files and a static library for Python (v3.10)
ii  rake                          13.0.6-2                                all          ruby make-like utility
ii  readline-common               8.1.2-1                                 all          GNU readline and history libraries, common files
ii  ripgrep                       13.0.0-2ubuntu0.1                       amd64        Recursively searches directories for a regex pattern
ii  rpcsvc-proto                  1.4.2-0ubuntu6                          amd64        RPC protocol compiler and definitions
ii  rsync                         3.2.7-0ubuntu0.22.04.4                  amd64        fast, versatile, remote (and local) file-copying tool
ii  shellcheck                    0.8.0-2                                 amd64        lint tool for shell scripts
ii  squashfs-tools                1:4.5-3build1                           amd64        Tool to create and append to squashfs filesystems
ii  sysvinit-utils                3.01-1ubuntu1                           amd64        System-V-like utilities
ii  thefuck                       3.29-0.3                                all          spelling corrector of console commands
ii  tmux                          3.2a-4ubuntu0.2                         amd64        terminal multiplexer
ii  tree                          2.0.2-1                                 amd64        displays an indented directory tree, in color
ii  ubuntu-minimal                1.481                                   amd64        Minimal core of Ubuntu
ii  ubuntu-standard               1.481                                   amd64        The Ubuntu standard system
ii  ubuntu-wsl                    1.481                                   amd64        Ubuntu on Windows tools - Windows Subsystem for Linux integration
ii  ucf                           3.0043                                  all          Update Configuration File(s): preserve user changes to config files
ii  unzip                         6.0-26ubuntu3.2                         amd64        De-archiver for .zip files
ii  valgrind                      1:3.18.1-1ubuntu2                       amd64        instrumentation framework for building dynamic analysis tools
ii  vim-common                    2:8.2.3995-1ubuntu2.23                  all          Vi IMproved - Common files
ii  vim-gtk                       2:8.2.3995-1ubuntu2.23                  all          Vi IMproved - enhanced vi editor (dummy package)
ii  vim-gtk3                      2:8.2.3995-1ubuntu2.23                  amd64        Vi IMproved - enhanced vi editor - with GTK3 GUI
ii  vim-gui-common                2:8.2.3995-1ubuntu2.23                  all          Vi IMproved - Common GUI files
ii  vim-runtime                   2:8.2.3995-1ubuntu2.23                  all          Vi IMproved - Runtime files
ii  wget                          1.21.2-2ubuntu1.1                       amd64        retrieves files from the web
ii  xxd                           2:8.2.3995-1ubuntu2.23                  amd64        tool to make (or reverse) a hex dump
ii  zip                           3.0-12build2                            amd64        Archiver for .zip files
ii  zsh                           5.8.1-1                                 amd64        shell with lots of features
ii  zsh-common                    5.8.1-1                                 all          architecture independent files for Zsh

```

#### Other powerful open-source software

Github is a nice place to attain poweful and open-source developer tools! I will recommend two tools that I have used frequently.

##### thefuck

Thefuck[^6] is a powerful tool that can automatically correct your command-line mistakes. Everytime you type something wrong (like `sudo apt insyall update`), using the `fuck` command will help you find your bug instead of typing it or modifying it in a time-consuming process. You can set some interesting alias such as `FuckingNvidia` to make the command more powerful! (Just a joke.) 

Sometimes thefuck is a bit too slow... I will use `zsh-syntax-highlighting` instead to alert me to command input errors. My customed settings for zsh-syntax-highlighting have been written into my `~/.zshrc` dotfiles. You can skip to the [previous part](#Linux-Terminal).

##### Yazi

Yazi[^5] (means "duck") is a terminal file manager written in Rust, based on non-blocking async I/O. It aims to provide an efficient, user-friendly, and customizable file management experience.

It's quite useful for its vim-liked operations. I often use it to browse and search my files quickly and navigate through multiple files.

You can go through their websites to obtain a deeper insight!

https://github.com/sxyazi/yazi 

## Information Collection

**Several forums regarding AI**

| [Way to AGI](https://waytoagi.feishu.cn/wiki/QPe5w5g7UisbEkkow8XcDmOpn8e) | 通往 AGI 之路 - Feishu Docs            | The Feishu documentation for the development and usage of AI |
| ------------------------------------------------------------ | -------------------------------------- | ------------------------------------------------------------ |
| [AI canon](https://a16z.com/ai-canon/)                       | AI canon from a16z                     | A curated list of resources we’ve relied on to get smarter about modern AI |
| [Open AI Research](https://openai.com/research/)             | OpenAI Research                        | The Research and releasing blog for OpenAI                   |
| [Google Research](https://research.google/blog/)             | Google Research                        | The Research and releasing blog for Google                   |
| [Deepmind](https://deepmind.google/discover/blog/)           | Google Deepmind                        | The Research and releasing blog for Google Deepmind          |
| [Meta](https://ai.meta.com/blog/)                            | Meta                                   | AI at Meta Blog                                              |
| [Nvidia](https://blogs.nvidia.com/blog/category/deep-learning/) | Nvidia                                 | The Research and releasing blog for Nvidia                   |
| [Microsoft](https://blogs.microsoft.com/)                    | Microsoft                              | The Research and releasing blog for Microsoft                |
| [Hugging Face Daily Papers](https://huggingface.co/papers)   | Hugging Face                           | Hugging Face Daily Papers                                    |
| [Stratechery](https://stratechery.com/category/articles/)    | Articles – Stratechery by Ben Thompson | Articles – Stratechery by Ben Thompson                       |

## Office Software

### Markdown files

For me, **markdown** is really an efficient tool! Not only for its convenience compared with **Microsoft Word or WPS**, but also for its simple but powerful syntax. Markdown has unparalleled advantages over other formats in note-taking, text generation, and more.

For Chinese users, you can select this website as your Markdown tutorial: https://markdown.com.cn/ . [CommonMark](https://commonmark.org/) is also a great website for teaching markdown syntax.

In the text below, I will recommend **4** different tools to write markdown. These tools are applicable in different scenarios. 

#### Typora (Most Recommended)

The website of Typora[^1]: [Typora](https://typora.io/)

- Lightly and neat.
- You can choose your theme in the open-source market, or you can just use your own **css** files!
- The most frequently used markdown editor!

#### MarkText (Free and open-source)

Advantages over Typora: 

- **Free and Open-source**
- customized settings: hot-keys, etc.

Several disadvantages:

- Personally, I don't like its **UI** design...
- Sometimes less fluent than Typora.

#### SiYuan Note (For Note Management)

Typora don't focus on **File management**, you can only navigate files in the same root folder.

**Siyuan Note** is a free and open-source markdown editor, which have great advantages over **Note Management**! You can go through [this website](https://github.com/siyuan-note/siyuan) for more details.

#### Vscode (For Coding and Manuals)

eh... Actually I don't like writing markdown in vscode (except for writing it in Jupyter Lab). However, several vscode plugins support the visualization of markdown files and you can make some slight modifications of your docs (like `README.md`). 

### PDF viewers

[Sumatra PDF viewer](https://www.sumatrapdfreader.org/free-pdf-reader) is the PDF viewers that is most frequently used by myself. [SumatraPDF](https://www.sumatrapdfreader.org/) is a free [PDF, EPUB, MOBI, CHM, XPS, DjVu, CBZ and CBR](https://www.sumatrapdfreader.org/docs/Supported-document-formats) reader for Windows. It’s small, fast, customizable and full of features.

**I love open-source!** 

## References

[^1]: https://xxyqwq.github.io/2024/01/27/build-windows-terminal/
[^2]: https://xxyqwq.github.io/2023/07/31/build-linux-terminal/
[^3]: https://missing.csail.mit.edu/2020/command-line/
[^4]: https://xiyuanyang-code.github.io/posts/Vim-tutorial/
[^5]: https://github.com/sxyazi/yazi
[^6]: https://github.com/nvbn/thefuck
[^7]: https://commonmark.org/
[^8]: https://typora.io/
