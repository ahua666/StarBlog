---
title: flutter 读取bmp格式图片并转换16进制
tags:
  - flutter
  - android
top_img: 'https://cdn.cbd.int/ahua666-panimg@1.0.6/img/20230731171324.png'
cover: "https://cdn.cbd.int/ahua666-panimg@1.0.6/img/20230731171324.png"
categories: 
  - flutter
abbrlink: 27745
date: 2023-04-27 22:11:18
---

## 总结

2023 年 5 月 10 日 12:52:25 更新

1. 这个方式获取的数据会附带 BMP 的头文件数据，如果不需要头文件的话处理方式看最后。

## 读取图片

```dart
Future<Uint8List> loadAsset(String path) async {
    final byteData = await rootBundle.load(path);
    print(byteData.buffer.asUint8List());
    return Uint8List.sublistView(byteData);
  }
```

## 转换 16 进制

```dart
String bytesToHexString(Uint8List bytes) {
    final buffer = StringBuffer();

    for (final b in bytes) {
      buffer.write(b.toRadixString(16).padLeft(2, '0'));
      print(b.toRadixString(16).padLeft(2, '0'));
    }

    return buffer.toString();
}
```

## 调用

```dart
void main() async {
    final bytes = await loadAsset('assets/images/bmp/1234.bmp');
    String hexString = bytesToHexString(bytes);
    print(hexString);
}
```

## 完整代码

```dart
  void main() async {
    final bytes = await loadAsset('assets/images/bmp/1234.bmp');
    String hexString = bytesToHexString(bytes);
    print(hexString);
    print("测试bmp");
  }

  Future<Uint8List> loadAsset(String path) async {
    final byteData = await rootBundle.load(path);
    print(byteData.buffer.asUint8List());
    return Uint8List.sublistView(byteData);
  }

  String bytesToHexString(Uint8List bytes) {
    final buffer = StringBuffer();

    for (final b in bytes) {
      buffer.write(b.toRadixString(16).padLeft(2, '0'));
      print(b.toRadixString(16).padLeft(2, '0'));
    }

    return buffer.toString();
  }
```

## 不需要头文件

此方法可以获取图片的像素数据 如 240*360 的单色 bmp 图片 能正常获取到 240*360/8=10800 个数据

```dart
import 'package:image/image.dart' as img;
  Test() async {
  final ceshi=  await decodeBmpAsset('assets/images/bmp/demo2.bmp');
  Uint8List? byte2=ceshi?.data?.buffer.asUint8List();
  String hexString = bytesToHexString(byte2!);
  print(hexString);
  }
//读取图片
Future<img.Image?> decodeBmpAsset(String assetName) async {
    final ByteData imageData = await rootBundle.load('assets/images/bmp/demo2.bmp');
    final Uint8List bytes = imageData.buffer.asUint8List();
    final image = img.decodeBmp(bytes);
    return image;
  }
///转16进制字符串
static String bytesToHexString(Uint8List byteArr) {
    if (byteArr == null || byteArr.length == 0) {
      return "";
    }
    Uint8List result = Uint8List(byteArr.length << 1);//创建一个byteArr.length两倍大的数组以存储16进制字符
    var hexTable = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F']; //16进制字符表
    //下面的for循环是通过codeUnitAt()方法取byteArr每一位对应的字符串的“UTF-16代码单元”，如果去掉.codeUnitAt()其实也就直接是16进制字符串
    for (var i = 0; i < byteArr.length; i++) {
      var bit = byteArr[i]; //取传入的byteArr的每一位
      var index = bit >> 4 & 15; //右移4位,取剩下四位,&15相当于&F,也就是&1111
      var i2 = i << 1; //byteArr的每一位对应结果的两位,所以对于结果的操作位数要乘2
      result[i2] = hexTable[index].codeUnitAt(0); //左边的值取字符表,转为Unicode放进resut数组
      index = bit & 15; //取右边四位,相当于01011010&00001111=1010
      result[i2 + 1] = hexTable[index].codeUnitAt(0); //右边的值取字符表,转为Unicode放进resut数组
    }
    //这里为了优化转换成一整个String的效率，所以就在上面的循环中先转成codeUnit再通过String的实例方法来生成字符串，否则需要用result.join("")，这个join()方法的效率低于String.fromCharCodes()方法
    return String.fromCharCodes(result); //Unicode转回为对应字符,生成字符串返回
  }
```
