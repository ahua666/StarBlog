---
title: ubuntu和Windows双系统开机时切换
date: 2024-07-06 16:43:50
tags:
    - ubuntu
cover: https://cdn.cbd.int/ahua666-panimg@1.0.56/img/20240706165251.png
top_img: https://cdn.cbd.int/ahua666-panimg@1.0.56/img/20240706165251.png
main_color: "#eed8cf"
---

## 无法切换系统
装好最新版ubuntu 24 后发现默认进入ubuntu 系统，无法切换到windows系统，解决方法如下：

## 解决方法
进入ubuntu系统后,修改grub配置文件 

路径为 /etc/default/grub

打开终端，输入命令
```shell
sudo vim /etc/default/grub
```
修改超时时间
```shell
GRUB_TIMEOUT=10
```
注释掉下面这行将会显示引导菜单
```shell
#GRUB_HIDDEN_TIMEOUT=0
```
保存退出
```shell
:wq
```
