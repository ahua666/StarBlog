---
title: android的webview不能显示ip地址形式的网页问题
date: 2023-10-08 16:23:14
tags:
  - flutter
  - android
top_img: 'https://cdn.cbd.int/ahua666-panimg@1.0.56/img/20230731171324.png'
cover: 'https://cdn.cbd.int/ahua666-panimg@1.0.56/img/20230731171324.png'
categories:
  - flutter
main_color: "#4f6977"
---

## 遇到webview不能显示ip地址形式的网页问题

非必要不去设置

## 在AndroidManifest.xml中添加如下代码

android:usesCleartextTraffic="true"添加到application标签中

```xml

<application
        android:usesCleartextTraffic="true">
</application>
```

## android:usesCleartextTraffic

指示应用是否打算使用明文网络流量，如明文 HTTP。 对于目标 API 级别为 27 或更低级别的应用，默认值为 "true"。对于以 API 级别 28
或更高级别为目标的应用，默认值为 "false"。
如果将此属性设为 "false"，平台组件（例如 HTTP 和 FTP 堆栈、DownloadManager 和 MediaPlayer）会拒绝应用要使用明文流量的请求。

强烈建议第三方库也遵循此设置。避免使用明文流量的主要原因是缺乏机密性、真实性和防止篡改的保护措施。网络攻击者可以窃听传输的数据，还可以在不被检测到的情况下修改数据。

此标志的遵循只能尽力而为，因为鉴于提供给 Android 应用的访问权限级别，不可能阻止来自这些应用的所有明文流量。例如，不要期望
Socket API 遵循此标志，因为它无法确定其流量是否采用明文形式。

不过，来自应用的大部分网络流量都由较高级别的网络堆栈和组件处理，这些堆栈和组件可通过从 ApplicationInfo.flags 或
NetworkSecurityPolicy.isCleartextTrafficPermitted() 读取来遵循此标志。
{% note primary simple %}
注意：对于以 API 级别 26 及更高级别为目标的应用，WebView 遵循此属性。
{% endnote %}
在应用开发过程中，可使用 StrictMode 识别来自应用的任何明文流量。如需了解详情，请参阅
StrictMode.VmPolicy.Builder.detectCleartextNetwork()。

此属性是在 API 级别 23 中添加的。

如果存在 Android 网络安全配置，则会在 Android 7.0（API 级别 24）及更高版本上忽略此标志。
