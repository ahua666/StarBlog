---
title: flutter flutter_tts 合成语音
tags:
  - flutter
  - android
abbrlink: 55048
top_img: 'https://cdn.cbd.int/ahua666-panimg@1.0.56/img/20230731171324.png'
cover: 'https://cdn.cbd.int/ahua666-panimg@1.0.56/img/20230731171324.png'
categories: 
  - flutter
date: 2023-04-06 20:11:27
main_color: "#4f6977"
---
## 支持的语音

ja-JP, el-GR, en-AU, ar-SA, hu-HU, sv-SE, zh-CN, fr-CA, en-US, it-IT, ro-RO, sk-SK, ko-KR, en-IE, zh-HK, fr-FR, nl-NL, id-ID, pt-BR, pt-PT, cs-CZ, en-GB, de-DE, da-DK, es-ES, pl-PL, ru-RU, zh-TW, es-MX, en-ZA, hi-IN, en-IN, th-TH, no-NO, tr-TR, fi-FI, nl-BE, he-IL


### 安装依赖

```yaml
dependencies:
  flutter_tts: ^3.6.3
```

执行flutter pub get
Windows环境需要在设置中开启开发者模式，不然会报错。

仓库地址:[flutter_tts](https://pub.dev/packages/flutter_tts)

### 配置

在文件中将最低 Android sdk 版本更改为 21（或更高）android/app/build.gradle

```xml
minSdkVersion 21
```

### Android 配置

Android 11使用文本语音转换的应用程序应该在其清单的查询元素中声明 TextToSpeech.Engine.INTENT_ACTION_TTS_SERVICE

在`<manifest>`标签和`<application>`标签之间添加如下配置

```xml
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.BLUETOOTH"/>
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
    <uses-permission android:name="android.permission.BLUETOOTH_CONNECT"/>
    <queries>
        <intent>
            <action android:name="android.speech.RecognitionService"/>
        </intent>
    </queries>
```

IOS没试 按照官方文档配置即可

### 简单使用

```dart
import 'package:flutter_tts/flutter_tts.dart';

FlutterTts flutterTts = FlutterTts();
try {
    // 设置语言
    await flutterTts.setLanguage("zh-CN");
    // 设置音量
    await flutterTts.setVolume(1);
    // 设置语速
    await flutterTts.setSpeechRate(0.6);
    // 音调
    await flutterTts.setPitch(0.4);
    await flutterTts.isLanguageAvailable("zh-CN");
    flutterTts.speak("测试TTS功能");
    print('tts初始化');
    } catch (e) {
        print("Failed to initialize TTS: $e");
    }
```

### 封装一个简单的工具类

```dart
import 'package:flutter_tts/flutter_tts.dart';

class TTSUtil {
  TTSUtil._();

  static final TTSUtil _manager = TTSUtil._();

  factory TTSUtil() {
    return _manager;
  }

  late FlutterTts flutterTts;

  void initTTS() {
    flutterTts = FlutterTts();
  }

  Future<void> speak(String text) async {
    // 设置语言
    await flutterTts.setLanguage("zh-CN");

    // 设置音量
    await flutterTts.setVolume(0.8);

    // 设置语速
    await flutterTts.setSpeechRate(0.5);

    // 音调
    await flutterTts.setPitch(1.0);

    // text = "你好，我的名字是Star，你是不是韩梅梅？";
    if (text.isNotEmpty) {
      await flutterTts.speak(text);
    }
  }

  /// 暂停
  Future<void> pause() async {
    await flutterTts.pause();
  }

  /// 结束
  Future<void> stop() async {
    await flutterTts.stop();
  }
}
```

### 使用

```dart
import 'package:tts_demo/utils/ttsUtil.dart';

  //...其他代码
class _WelcomePageState extends State<WelcomePage> {
  late TTSUtil _ttsUtil;
  @override
  void initState() {
    super.initState();
    initTTS(); // 在组件挂载完毕后执行初始化
  }
  //调用初始化方法
  void initTTS() {
    _ttsUtil = TTSUtil();
    _ttsUtil.initTTS();
  }

  //...其他代码

  //比如在按钮点击事件中调用
  onPressed: () {
    _ttsUtil.stop();
    _ttsUtil.speak('这是一段测试语音');
  },

  //...其他代码
}
```

目前暂时不需要其他的功能，后续有需要再进行补充。
