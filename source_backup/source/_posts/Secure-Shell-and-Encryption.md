---
title: Secure Shell and Encryption
date: 2025-05-13 10:50:23
excerpt: This article introduces the basic principles and specific operations of SSH, as well as an introduction to network security and cryptography.
index_img: /img/cover/ssh.jpg
categories:
  - Efficient Tools
  - Missing Semesters
tags:
  - tutorial
  - SSH
  - Missing Semester
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# SSH Remote Host

使用 `ssh` 和 `scp` 等工具实现服务器的远程连接（第一次体会到没有 sudo 权限的感觉）

## Introduction

自从半年前第一次使用 SSH 以来，使用实验室服务器进行工作已经一个学期了。SSH 现在已成为我日常工作中不可或缺的工具，期间也遇到过不少问题。因此，我想系统地整理 SSH 相关知识，并以此作为对计算机安全和加密技术的入门介绍。

## What is Secure Shell (SSH)？

SSH（Secure Shell）是一种网络协议，用于在不安全的网络上安全地访问和管理计算机。它通过加密方式在客户端和服务器之间建立安全通信通道，常用于远程登录、文件传输等网络服务。

传统身份认证方法（如密码认证）存在安全隐患，一旦密码泄露就会导致数据泄漏风险。SSH 的核心是使用加密算法生成公钥和私钥对进行身份认证：

- **公钥**：
	- 用于数据传输加密
	- 服务器用公钥验证签名（证明密钥持有者身份）
- **私钥**：
	- **绝对不可泄露**给任何第三方
	- 是证明身份的唯一凭证

### How it works?

{% note primary %}

1. 在本地生成密钥对（公钥和私钥）
2. 将公钥添加到服务器的 `~/.ssh/authorized_keys` 文件中（表示服务器信任该公钥）
3. 配置本地 `~/.ssh/config` 文件
4. 本地首先检查 `~/.ssh/known_hosts` 中是否有服务器公钥记录
5. 如果找到匹配记录，开始身份认证
6. 本地尝试使用存储的私钥进行认证
7. 服务器验证通过后建立加密会话

{% endnote %}

为了实现服务器的安全连接，我们需要在本地生成对应的密钥对：公钥 & 私钥。我们需要把公钥文件提供给服务器，放在服务器的`.ssh/authorized_keys`文件中，顾名思义，这**代表了服务器信任**的所有公钥。

接下来，配置`.ssh/config`文件中的对应的`Host`。以自己服务器的为例：

```
Host db94
    HostName 111111111
    User xiyuanyang
    Port 58122
    ProxyCommand ssh -q -W %h:%p db13
```

换句话说，我们把我们需要连接的远程服务器叫做**db94**，并且确定好了服务器的域名和需要登录的账户以及服务器的网络端口。

当开始ssh连接的时候，本地首先会会从`~/.ssh/known_hosts`中寻找相关记录，文件中会储存我们在服务器里面存放的公钥文件，例如：

```bash
debug1: Server host key: ssh-ed25519 SHA256:111
debug1: Host '[111.111.11.111]:58122' is known and matches the ED25519 host key.
```

这就说明**本地找到了对应的服务器**，现在我们需要尝试登录。

{% note primary %}

SSH 协议可以被中间人攻击。

即你在连接服务器时，中间人劫持你的请求并冒充服务器与你交互，同时冒充连接者利用你的交互信息尝试连接服务器。这样攻击者就可以成功连接到服务器以你的权限进行任意操作。同时，如果攻击者在自己的计算机上转发你的请求到服务器并将服务器的回复转发给你，便可成功监听你们的会话，并且难以被发现。

为了避免中间人攻击，ssh 客户端会在`~/.ssh/known_hosts`中存储连接过的主机及其指纹 ( 一个哈希值，可以认为这个哈希值唯一确认了目标主机 )。当尝试连接时，ssh 客户端会获取对方的指纹并在`~/.ssh/known_hosts`查找是否已经记录过该指纹。

{% endnote %}

我们会尝试自己在本地的多个密钥文件，看是否和**服务器给出的公钥文件**相匹配。

```bash
debug1: Offering public key: /home/xiyuanyang/.ssh/id_ed25519
debug1: Server accepts key: /home/xiyuanyang/.ssh/id_ed25519
```

> 这就是ssh中的**公钥认证**环节，可以实现及其安全的**免密登录**。

接下来，**服务器**认证了本地是正确的人！因此会打开一个加密会话实现隧道传输。

如果你是第一次登录服务器，可能会看到如下的信息：

```bash
The authenticity of host '[111.111.11.111]:58122 (<no hostip for proxy command>)' can't be established.
ED25519 key fingerprint is SHA256:example.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[111.111.11.111]:58122' (ED25519) to the list of known hosts.
```

因为是第一次登录，因此本地无法在过去自己的“登录历史”中找到对应服务器的公钥，因此需要询问用户**是否为可信任**的公钥。用户信任之后公钥就是自动保存到`.ssh/known_hosts`中。

接下来的过程和之前的完全相同。

### How is it Safe?

服务器和本地的通讯的关键就在于**密钥对**的匹配。因此，只要保证自己的私钥不被泄漏，理论上就是安全的。

不过理论上你是可以登录到别人的账户上面的，如果没有permission denied的话。你可以将自己的公钥添加到其他用户的 `~/.ssh/authorized_keys` 文件中，从而允许你用对应的私钥登录他们的账户。**不推荐这样干**。

如果服务器发生了重置，旧的公钥将会被全部丢弃，本地电脑之前连接过旧服务器，保存了旧的公钥。服务器重置后，新密钥和本地记录的旧密钥不匹配，SSH 认为这可能是中间人攻击（有人冒充服务器），所以强制报错。因此需要重置自己本地的knownhosts的记录，就相当于在本地也删除旧的记录。

```bash
ssh-keygen -f ~/.ssh/known_hosts -R "[192.168.28.205]:58122"
```

## Operations

首先我们需要在本地生成一个ssh密钥：

```bash
 ssh-keygen -t ed25519 -C "yangxiyuan@sjtu.edu.cn"
```

`ssh-keygen` 是一个用于生成、管理和转换 SSH（Secure Shell）密钥对的命令行工具。SSH 密钥对由一个公钥和一个私钥组成，通常用于安全地连接到远程服务器。

- `-t ed25519`: 选择的ssh加密算法
- `-C` 为了标识的注释。

会得到`id_ed25519`和`id_ed25519.pub`两个文件存储在`.ssh`文件夹中。

`id_ed25519.pub`可能长的像这样子：

```bash
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEXSC79Qd1UsSlv6yrfm+TLXSKCJQzjHGm95bgAPUUni yangxiyuan@sjtu.edu.cn
```

{% note danger %}

**不要向任何网站，任何人，任何ai**泄漏你的SSH私钥！`id_ed25519`文件**是私钥文件**，只需要存储在本地机器上做身份认证就行了，但是公钥文件`id_ed25519.pub`是需要给别人看的。

{% endnote %}

## `scp` for File Transfer

使用scp工具可以快速实现本地文件和远程服务器文件的快速传递。

> 注意：**这些命令都需要在本地执行**！

```bash
scp -r -P 58122 alpaca_eval xiyuanyang@db95-db13:/GPFS/rhome/xiyuanyang/Agents/Agent_Test_alpaca
```

- `scp -r` means recursively **for folders**. (For single files, you can just remove that!)
- `-P`: **Port** for the remote machine! Very Important!
	- You can check this in the `config` file in `.ssh`. You can see the following **Port**.

```config
Host db94
    HostName 111111111
    User xiyuanyang
    Port 58122
    ProxyCommand ssh -q -W %h:%p db13
```

- `alpaca_eval`: The folder to be transferred **locally**.
- `xiyuanyang@db95-db13`: Your account in the remote host! (See `user` and `Host` in the config settings above)
- `/GPFS/rhome/xiyuanyang/Agents/Agent_Test_alpaca`: The target location in the remote host. (Absolute path recommended using `pwd` command).

当然， 你也可以**反过来**，把远端的文件传输到本地。

```bash
scp -r -P 58122 xiyuanyang@db95-db13:/GPFS/rhome/xiyuanyang/DL_learn/Basic_AI/KaggleCom_0410/Lec_10/submission.csv /mnt/d/DOWNLOADS
```

Another example:

```bash
scp -P 53392 sample_submission.csv root@connect.bjc1.seetacloud.com ~/test/data
```

Use `-v`, `-vv`, `-vvv` for more debugging info when some errors occurred.

## Other Info

- 注意不要随便改动终端的环境（例如把bash换成zsh），因为scp可能会出问题。
- 如果实验室远程服务器重置过，密钥就会失效。**为了强安全性**，我们需要在本地更换密钥。（见上文SSH安全）

# Introduction to Encryption

Lecture Notes for Missing Semester, last course.

To de done in the future.

推荐网址，讲的超级好：[Encryption](https://xflops.sjtu.edu.cn/hpc-start-guide/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/#_6)
