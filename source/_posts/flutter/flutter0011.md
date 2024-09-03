---
title: flutter开启高帧率
date: 2024-08-16 10:56:40
tags:
  - flutter
  - android
top_img: 'https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20230731171324.png'
cover: 'https://cdn.cbd.int/ahua666-panimg@1.0.62/img/20230731171324.png'
categories:
  - flutter
main_color: "#4f6977"
---

## flutter 开启高帧率

yaml文件中添加依赖
```shell
flutter_displaymode: ^0.6.0
```

main.dart中添加

WidgetsFlutterBinding.ensureInitialized();

如下示例
```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  runApp(const MyApp());
}
```

使用示例
```dart
final DisplayMode m = await FlutterDisplayMode.active;
print(m);
await FlutterDisplayMode.setHighRefreshRate();
```
