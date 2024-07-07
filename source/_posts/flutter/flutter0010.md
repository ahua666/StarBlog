---
title: flutter 底部状态栏透明沉浸
date: 2023-12-05 22:33:23
tags:
  - flutter
  - android
top_img: 'https://cdn.cbd.int/ahua666-panimg@1.0.57/img/20230731171324.png'
cover: 'https://cdn.cbd.int/ahua666-panimg@1.0.57/img/20230731171324.png'
categories:
  - flutter
main_color: "#4f6977"
---

## flutter沉浸式-底部状态栏透明沉浸

在main入口配置即可

```dart
main() {
  runApp(MyApp());
  
  if (Platform.isAndroid) {
      SystemUiOverlayStyle systemUiOverlayStyle = const SystemUiOverlayStyle(
        statusBarColor: Colors.transparent,
        systemNavigationBarColor: Color(0x002196f3),
      );
      SystemChrome.setSystemUIOverlayStyle(systemUiOverlayStyle);
      SystemChrome.setEnabledSystemUIMode(SystemUiMode.edgeToEdge);
    }
}
```
