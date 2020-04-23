---
title: pi-hole搭建DNS服务器
tags:
  - RaspberryPi
  - 树莓派
categories: RaspberryPi
abbrlink: 61493
date: 2019-08-29 17:25:38
---

> pi hole 为互联网广告准备的黑洞.

## Software Env

> MacOS 10.15 Catalina
> RaspberryPi 4B

## 步骤

1. 进入树莓派，执行一键安装脚本 `curl -sSL https://install.pi-hole.net | bash`
2. 安装过程中选择需要安装的插件，如可视化界面等

    安装成功后信息如下：可登录web 界面查看 dashboard

    ```bash
    Configure your devices to use the Pi-hole as their DNS server
    using:
    IPv4:        192.168.0.100
    IPv6:        Not Configured
    If you set a new IP address, you should restart the Pi.
    The install log is in /etc/pihole.
    View the web interface at http://pi.hole/admin or http://192.168.0.100/admin
    Your Admin Webpage login password is xxxxxxxx
    ```

3. 使用 DNS 服务器，有三种方法：[How do I configure my devices to use Pi-hole as their DNS server?](https://discourse.pi-hole.net/t/how-do-i-configure-my-devices-to-use-pi-hole-as-their-dns-server/245),在这里我选择第三种方法(Manually configure each device)

### 参考

1. [家用网络环境折腾 篇一：在家用环境中利用NAS建立DNS缓存服务器Pi-hole](https://post.smzdm.com/p/710075/)
2. [安装Pi-hole，让树莓派为你过滤家中广告！](http://shumeipai.nxez.com/2018/02/09/install-pi-hole-for-raspberry-pi-to-filter-advertisement.html)
3. [pi-hole 官网](https://pi-hole.net/)
4. [pi-hole github](https://github.com/pi-hole/pi-hole/#one-step-automated-install)

---
