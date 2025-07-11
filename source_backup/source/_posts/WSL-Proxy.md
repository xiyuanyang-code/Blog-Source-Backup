---
title: 'WSL Proxy'
date: 2025-05-18 13:47:04
index_img: /img/cover/wslnet.jpg
excerpt: Tutorial for basic conception of Internet in WSL and ways to fix DNS pollution problem in WSL, including several tools to check internet.
categories:
  - Efficient Tools
tags:
  - WSL
  - Proxy
  - tutorial
---

# Proxy Settings in WSL

## WSL 网络基础概念

### NAT

NAT（**Network Address Translation**）是一种将私有IP地址转换为公有IP地址的技术。

#### IP Address

**IP地址**[^1]（英语：IP Address，全称Internet Protocol Address），又译为**网际协议地址**、**互联网协议地址**。是[网际协议](https://zh.wikipedia.org/wiki/网际协议)中用于标识**发送**或**接收数据报**的设备的一串数字。IP地址是唯一标识网络中设备的数字标签，分为两种：

- **IPv4**：如 `192.168.1.10`（32位，4组数字）。
- **IPv6**：如 `fe80::215:5dff:fe20:7c2d`（128位，16进制）。

#### Public IP and Private IP

##### 私有IP（Private IP）

- **定义**：在本地网络（如家庭、公司或虚拟网络）内部使用的IP地址，**不可直接从互联网访问**。
- **常见范围**：
	- IPv4：`10.0.0.0/8`、`172.16.0.0/12`、`192.168.0.0/16`。
	- WSL默认使用的子网：`172.16.0.0/12`（如 `172.28.1.1`）。
- **作用**：用于内部设备通信（如主机与WSL之间）。

##### 公有IP（Public IP）

- **定义**：互联网全局唯一的IP地址，由ISP（运营商）分配，可直接从外部访问。
- **示例**：如 `203.0.113.45`（此地址由你的路由器或ISP提供）。
- **作用**：主机通过NAT将私有IP转换为公有IP访问互联网。

WSL在本质上还是一个虚拟机，因此Windows主机和WSL实例各自拥有独立的IP地址。例如：

```powershell
# run in powershell
ipconfig
```

```plaintext
以太网适配器 VMware Network Adapter VMnet1:

   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::e986:a48d:3a87:372%15
   IPv4 地址 . . . . . . . . . . . . : 192.168.191.1
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . :

以太网适配器 VMware Network Adapter VMnet8:

   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::ffcd:ab80:84f0:628c%9
   IPv4 地址 . . . . . . . . . . . . : 192.168.79.1
   子网掩码  . . . . . . . . . . . . : 255.255.255.0
   默认网关. . . . . . . . . . . . . :

无线局域网适配器 WLAN:

   连接特定的 DNS 后缀 . . . . . . . :
   IPv6 地址 . . . . . . . . . . . . : 2403:d400:1000:12:95a6:a9db:f142:84c4
   临时 IPv6 地址. . . . . . . . . . : 2403:d400:1000:12:35e1:ae0c:4ba0:f169
   本地链接 IPv6 地址. . . . . . . . : fe80::4beb:4c8b:bb43:68d4%24
   IPv4 地址 . . . . . . . . . . . . : 10.181.219.220
   子网掩码  . . . . . . . . . . . . : 255.254.0.0
   默认网关. . . . . . . . . . . . . : fe80::56c6:ffff:fe7b:5802%24
                                       10.180.0.1

```

```bash
# run in WSL
ifconfig
```

```
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 4a:39:01:be:ca:fe  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.181.219.220  netmask 255.254.0.0  broadcast 10.181.255.255
        inet6 2403:d400:1000:12:95a6:a9db:f142:84c4  prefixlen 64  scopeid 0x0<global>
        inet6 2403:d400:1000:12:35e1:ae0c:4ba0:f169  prefixlen 128  scopeid 0x0<global>
        inet6 fe80::4beb:4c8b:bb43:68d4  prefixlen 64  scopeid 0x20<link>
        ether e8:c8:29:bb:02:92  txqueuelen 1000  (Ethernet)
        RX packets 54806  bytes 29007547 (29.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 71390  bytes 13607203 (13.6 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 8799  bytes 1461027 (1.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8799  bytes 1461027 (1.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

loopback0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        ether 00:15:5d:3f:7c:62  txqueuelen 1000  (Ethernet)
        RX packets 45719  bytes 46271562 (46.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 17592  bytes 44415200 (44.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

> 这里看**eth1**接口，私有IP为`10.181.219.220`.

- **WSL 内部 IP**: 您的 WSL 发行版（如 Ubuntu）会获得一个私有 IP 地址 (例如 `172.17.77.95`)，通常在 `eth0` 网络接口上。
- **Windows 主机在 WSL 虚拟网络中的 IP**: Windows 主机会有一个对应的虚拟网络适配器 (通常名为 `vEthernet (WSL)`)，它也有一个该虚拟网络内的 IP 地址 (例如 `172.17.64.1`)。这个 IP 地址非常重要，因为 WSL 通常通过它来访问主机提供的服务（包括 DNS 转发和本地代理）。

- **Mirrored 模式 (实验性)**: 这是 `.wslconfig` 中的一个实验性设置 (`networkingMode=mirrored`)，旨在让 WSL 共享 Windows 主机的网络接口和 IP 地址。理论上可以简化网络配置，但可能与某些代理、VPN 或防火墙不兼容，并可能导致 DNS 问题。**在我们的排查中，恢复到默认 NAT 模式是解决复杂网络问题的关键一步。**

## WSL 中的 DNS 解析 (`/etc/resolv.conf`)

**DNS 解析（Domain Name System Resolution）** 是将人类可读的域名（如 `google.com`）转换为计算机可识别的 **IP 地址**（如 `142.250.190.46`）的过程。它是互联网运作的基础，类似于“电话簿”，把易记的名字映射到实际的网络地址。

在DNS解析的流程中，计算机首先会尝试检查计算机的DNS缓存，如果如果缓存中有记录，直接返回 IP，否则继续。如果没有，这像本地配置的DNS服务器 (DNS Server)发起域名解析请求，递归服务器从根域名服务器（`.`）开始，逐级查询：根服务器告知 `.com` 的 TLD 服务器地址，TLD 服务器告知 `github.com` 的权威 DNS 服务器地址，最终从权威服务器获取 `github.com` 的 IP。

WSL 中的 `/etc/resolv.conf` 文件定义了它将使用哪个 **DNS 服务器来解析域名**。

排查方式：

- 首先`ping 8.8.8.8`，如果成功说明网络没问题，真正出问题的在DNS解析。
- 判断DNS解析是否出现问题：
	- 查看DNS server：`cat /etc/resolv.conf`
	- 测试DNS解析：`nslookup github.com`

```bash
$ cat /etc/resolv.conf
# This file was automatically generated by WSL. To stop automatic generation of this file, add the following entry to /etc/wsl.conf:
# [network]
# generateResolvConf = false
# nameserver 10.255.255.254
nameserver 8.8.8.8       # Google DNS
nameserver 8.8.4.4       # Google DNS for backup

$ nslookup github.com
Server:         8.8.8.8
Address:        8.8.8.8#53

Non-authoritative answer:
Name:   github.com
Address: 20.205.243.166
```

上面的实例中系统首先使用了`8.8.8.8`的谷歌DNS服务器，并成功将`github.com`的域名解析为`20.205.243.166`.

如果`nslookup`过程失败了，请参照后文教程手动修改DNS server设置。

### 临时修改DNS Server

我们可以修改`/etc/resolve.conf`来临时修改DNS配置。使用`sudo vim /etc/resolve.conf`来配置该文件。

{% note danger %}

**因为是系统文件**，所以要注意使用sudo权限，并且**注意安全**！

{% endnote %}

并且更换你的DNS服务器，例如更换为谷歌的DNS服务器：

```bash
nameserver 8.8.8.8       # Google DNS
nameserver 8.8.4.4       # Google DNS for backup
```

或者你也可以使用如下命令快速配置：

```bash
# 临时使用 Google 公共 DNS
sudo bash -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
sudo bash -c 'echo "nameserver 8.8.4.4" >> /etc/resolv.conf'
```

在更改DNS server之后，立即测试连通性：

```bash
nslookup github.com  # 现在应该能返回正确的 IP
ping github.com      # 测试连通性
```

```plaintext
❯ nslookup github.com
Server:         8.8.8.8
Address:        8.8.8.8#53

Non-authoritative answer:
Name:   github.com
Address: 20.205.243.166


❯ ping -c 4 github.com
PING github.com (20.205.243.166) 56(84) bytes of data.
64 bytes from 20.205.243.166: icmp_seq=1 ttl=112 time=94.2 ms
64 bytes from 20.205.243.166: icmp_seq=2 ttl=112 time=96.7 ms
64 bytes from 20.205.243.166: icmp_seq=4 ttl=112 time=90.6 ms

--- github.com ping statistics ---
4 packets transmitted, 3 received, 25% packet loss, time 3075ms
rtt min/avg/max/mdev = 90.640/93.857/96.741/2.501 ms
```

DNS 服务器问题可以被正确解决。

### 永久化配置

WSL 默认从 `/etc/resolv.conf` 读取 DNS 服务器，但该文件由 Windows 自动生成。因此，需要手动设置**不自动生成`/etc/resolve.conf`**工具来保证DNS服务器不被修改。

{% note danger %}

**不要使用奇奇怪怪的DNS**，小心DNS污染。

{% endnote %}

```bash
sudo vim /etc/wsl.conf
```

并添加以下两行：

```plaintext
[network]
generateResolvConf = false
```

## 诊断工具

- `ip addr` (WSL): 查看 WSL 网络接口和 IP 地址。
- `cat /etc/resolv.conf` (WSL): 查看 WSL 的 DNS 配置。
- `nslookup <domain>` (Windows & WSL): 测试 DNS 解析。
- `ping <ip_or_domain>` (Windows & WSL): 测试网络连通性。
- `nc -zv <host> <port>` (WSL): 测试 TCP 端口连通性。
- `curl -v <URL>` (Windows & WSL): 测试 HTTP/S 连接并查看详细过程。
- `ssh -vv <host>` (WSL): 获取 SSH 连接的详细调试信息。
- `netstat -ano | findstr "<port>"` (Windows CMD/PowerShell): 查看端口监听状态。

## References

[^1]: https://zh.wikipedia.org/wiki/IP%E5%9C%B0%E5%9D%80
