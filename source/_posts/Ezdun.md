---
title: 某传奇辅助山寨
abbrlink: 19791
date: 2023-01-18 17:35:29
tags: 
    - 逆向
    - 破解
categories: 破解
top_img: 'https://pans.ahuaaa.cn/img/52pojie300.jpg'
cover: 'https://pans.ahuaaa.cn/img/52pojie300.jpg'
---

# 前言 
博客发布的一切破解补丁、注册机和注册信息及软件的解密分析文章仅限用于学习和研究目的；不得将上述内容用于商业或者非法用途，否则，一切后果请用户自负。 本站信息来自网络，版权争议与本站无关。您必须在下载后的24个小时之内，从您的电脑中彻底删除上述内容。如果您喜欢该程序，请支持正版软件，购买注册，得到更好的正版服务。如有侵权请邮件与我们联系处理。

# 原理

将软件授权的IP地址转为本地地址，通过本地来进行授权

# 查壳
这里我找不到最开始的源文件了。壳是E盾企业个人版的

附上被火绒处理后查壳的图，火绒的处理日志也找不到了，（被火绒处理后就可以查到字符串了）

![](https://pans.ahuaaa.cn/img/2777a0d29ea22d71be8babab4882f76.png)

# 载入OD

E盾带有OD检测，需要装动态标题插件才能调试（我回滚快照了懒得装了，然后也不用调试了，不用去爆破了）

![](https://pans.ahuaaa.cn/img/bed213fdc03e9157b48a6b41acae529.png)

搜索字符串可以看到有个（7E21FFCD）是E盾企业个人版本特征码，E盾留的后门可以直接进入后台生成、查询、封禁卡密、封禁用户，给代理用户增加余额等等一系列操作。
# 直接制作补丁

根据字符串搜索到的IP制作劫持补丁，保存为winspool.drv 在待山寨软件同一个目录

![](https://pans.ahuaaa.cn/img/ed021ea518c4c4a3f8c5e9abf4408f7.png)

# 搭建E盾个人破解版

首先添加软件名 
![](https://pans.ahuaaa.cn/img/5fc1ddcb8bb2813829588d7f64bc127.png)

卡类型管理=> 修改好参数=> 勾选 通用软件卡允许充值全部软件
![](https://pans.ahuaaa.cn/img/f9ae485bc47d8d1f86d0246f51ca25c.png)

复制一张刚刚生成的卡密测试下

![](https://pans.ahuaaa.cn/img/fa6958f8899b02f02adb8fa1c0b7a4b.png)

弹出提示代表已经劫持成功

![](https://pans.ahuaaa.cn/img/67606940140277f8cd3744bf1a72301.png)

初始化软件

![](https://pans.ahuaaa.cn/img/ec9223d42a3c31b19ed9942b3479095.png)


完美山寨
![](https://pans.ahuaaa.cn/img/126ff8e9c6194d396c04e32d18c24a1.png)

# 后记
工具这里就不提供了，自行在网络上寻找资源。
另外可以做到直接进入作者后台生成卡密。（通过该盾作者自己留下的后门进入）

如图所示:
![](https://pans.ahuaaa.cn/img/3cd0de01c45a37c9424ef93d969d413.png)
