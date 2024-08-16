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

将 MainActivity.kt 中的

```kotlin
import io.flutter.embedding.android.FlutterActivity
class MainActivity: FlutterActivity ()
```

改为

```kotlin
import io.flutter.embedding.android.FlutterFragmentActivity
class MainActivity: FlutterFragmentActivity ()
```

即可开启高帧率
