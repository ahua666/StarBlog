---
title: flutter 添加启动画面 适配暗黑模式
tags:
  - flutter
  - android
abbrlink: 42957
top_img: 'https://cdn.cbd.int/ahua666-panimg@1.0.30/img/20230731171324.png'
cover: 'https://cdn.cbd.int/ahua666-panimg@1.0.30/img/20230731171324.png'
categories: 
  - flutter
date: 2023-04-06 14:37:26
---

## 前言

app在启动的时候会白屏一下，这个时候可以添加一个启动画面，让用户等待的时候不至于那么无聊。

### 安装依赖 flutter_native_splash

仓库地址:[flutter_native_splash](https://pub.dev/packages/flutter_native_splash)

方式1：在 pubspec.yaml 添加这样一行

```yaml
dependencies:
  flutter_native_splash: ^2.2.19
```

然后执行pub get

方式2：在终端执行

```bash
$ flutter pub add flutter_native_splash
```
这将向你的包的 pubspec.yaml 添加上面那一行（并运行隐式flutter pub get）


### 添加启动画面

在项目根目录新增 assets/images 目录
然后制作两张图片，一张是正常模式下的启动图，一张是暗黑模式下的启动图，图片的尺寸要一样(1152*1152)，然后放到 assets/images 目录下

然后添加如下配置

```yaml
dependencies:
  flutter_native_splash: ^2.2.19
flutter_native_splash:
  color: "#FFFFFF"
  color_dark: "#111B21"
  image: assets/images/splash_light.png
  image_dark: assets/images/splash_dark.png
  android_12:
    color: "#FFFFFF"
    color_dark: "#111B21"
    image: assets/images/splash_light.png
    image_dark: assets/images/splash_dark.png
    icon_background: "#FFFFFF"
    icon_background_dark: "#111B21"
```

配置的层级和dependencies一样

splash_dark.png 和 splash_light.png 是暗黑模式下的启动图和正常启动图

配置好后执行

```bash
$ flutter pub run flutter_native_splash:create
```

### 然后停止运行后再重新运行程序即可
