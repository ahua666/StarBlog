---
title: v2一键安装
date: 2024-06-07 09:00:10
tags :
categories:
---


## 命令
系统支持：Ubuntu，Debian，CentOS，推荐使用 Ubuntu 22，谨慎使用 CentOS，脚本可能无法正常运行！
```shell
#一键安装脚本
bash <(curl -s -L https://git.io/v2ray-setup.sh)
#关闭防火墙
systemctl stop firewalld; systemctl disable firewalld; ufw disable
```
