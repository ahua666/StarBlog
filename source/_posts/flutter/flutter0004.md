---
title: Flutter GetX和showModalBottomSheet使用
tags:
  - flutter
  - GetX
categories: 
  - flutter
top_img: 'https://cdn.cbd.int/ahua666-panimg@1.0.57/img/20230731171324.png'
cover: 'https://cdn.cbd.int/ahua666-panimg@1.0.57/img/20230731171324.png'
abbrlink: 17462
date: 2023-05-01 21:00:14
main_color: "#4f6977"
---

## GetX 状态管理

本文是以 Vue 开发经验为基础，对 GetX 状态管理的使用进行记录。

### 引入

[仓库地址](https://pub.flutter-io.cn/packages/get)

```dart
# 非空安全最后一个版本（flutter 2.0之前版本）
get: ^3.26.0

# 空安全版本 最新版本请查看  https://pub.flutter-io.cn/packages/get
get: ^4.6.5
```

```dart
import 'package:get/get.dart';
```

## 主入口配置

在主入口配置后就相当于 Vue 的全局引入了，可以在任意地方使用。
引入方式为将 MaterialApp 改成 GetMaterialApp

```dart
void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      home: CounterGetPage(),
    );
  }
}
```

然后各个模块导包 都是引入这个包

```dart
import 'package:get/get.dart';
```

## 使用

再使用 getx 后可以将状态管理的代码大大简化，不需要再写 setState 了,他会自动刷新页面。

### 简单使用

view 层 编写页面

```dart
class TestPage extends StatelessWidget {
  final logic = Get.put(TestLogic());
  final state = Get.find<TestLogic>().state;

  @override
  Widget build(BuildContext context) {
    return Container();
  }
}
```

logic 层 处理逻辑

```dart
class TestLogic extends GetxController {
  final state = TestState();
}


```

state 层 储存数据

```dart
class TestState {
  TestState() {
    ///Initialize variables
  }
}
```

我个人使用的方式是默认的，分三个层次来编写代码，这样代码结构清晰，逻辑清晰，方便维护。

## showModalBottomSheet 的使用

在不使用 GetX 时使用

```dart
  Future<Future<int?>> _showBasicModalBottomSheet(
    context,
  ) async {
    return showModalBottomSheet<int>(
      isScrollControlled: true,
      context: context,
      backgroundColor: Colors.transparent, //重点

      builder: (BuildContext context) {
        bool _isChecked = false;
        return AnimatedPadding(
          padding: EdgeInsets.only(
            // 下面这一行是重点
            bottom: MediaQuery.of(context).viewInsets.bottom,
          ),
          duration: Duration.zero,
          child: Container(
            height: 418,
            decoration:  BoxDecoration(
                color: Get.theme.primaryColor,
                borderRadius: const BorderRadius.only(
                    topLeft: Radius.circular(25.0),
                    topRight: Radius.circular(25.0))),
            child: Padding(
              padding: const EdgeInsets.all(30.0),
              child: Column(
                children: [
                  Row(
                    mainAxisAlignment: MainAxisAlignment.spaceBetween,
                    children: [
                      Expanded(
                        child: Row(
                          children: const [
                            Icon(Icons.swap_horiz_outlined,
                                size: 30, color: Coloors.iconModal),
                            Text(
                              "注册",
                              style: TextStyle(
                                  fontSize: 18,
                                  fontWeight: FontWeight.w600,
                                  color: Coloors.iconModal),
                            ),
                          ],
                        ),
                      ),
                      const Center(
                          child: Text(
                        "登录",
                        style: TextStyle(
                            fontWeight: FontWeight.bold, fontSize: 22),
                      )),
                      //关闭图标
                      Expanded(
                        child: Row(
                          mainAxisAlignment: MainAxisAlignment.end,
                          children: [
                            GestureDetector(
                              onTap: () {
                                //关闭底部抽屉
                                Get.back();
                              },
                              child: const Icon(Icons.close,
                                  size: 30, color: Coloors.iconModal),
                            ),
                          ],
                        ),
                      ),
                    ],
                  ),
                  const SizedBox(height: 30),
                  Expanded(
                    child: Column(
                      children: [
                        Container(
                          margin: const EdgeInsets.only(left: 10),
                          decoration: BoxDecoration(
                              color: Colors.white60,
                              border:
                                  Border.all(color: Colors.white60, width: 0.5),
                              borderRadius: BorderRadius.circular((20.0))),
                          child: const TextField(
                            decoration: InputDecoration(
                              border: OutlineInputBorder(
                                borderSide: BorderSide.none,
                                borderRadius:
                                    BorderRadius.all(Radius.circular(20)),
                              ),
                              //背景圆角
                              contentPadding:
                                  EdgeInsets.fromLTRB(20, 10, 10, 15),
                              filled: true,
                              hintText: "请输入用户名",
                              hintStyle: TextStyle(
                                fontSize: 16,
                                fontWeight: FontWeight.w600,
                                color: Coloors.greyDark,
                              ),
                              // 隐藏下划线
                              // border: InputBorder.none,
                            ),
                            // 光标颜色
                            cursorColor: Coloors.iconModal,
                          ),
                        ),
                        const SizedBox(
                          height: 15,
                        ),
                        Container(
                          margin: const EdgeInsets.only(left: 10),
                          decoration: BoxDecoration(
                              color: Colors.white60,
                              border:
                                  Border.all(color: Colors.white60, width: 0.5),
                              borderRadius: BorderRadius.circular((20.0))),
                          child: const TextField(
                            decoration: InputDecoration(
                              border: OutlineInputBorder(
                                borderSide: BorderSide.none,
                                borderRadius:
                                    BorderRadius.all(Radius.circular(20)),
                              ),
                              //背景圆角
                              contentPadding:
                                  EdgeInsets.fromLTRB(20, 10, 10, 15),
                              filled: true,
                              hintText: "请输入密码",
                              hintStyle: TextStyle(
                                fontSize: 16,
                                fontWeight: FontWeight.w600,
                                color: Coloors.greyDark,
                              ),
                              // 隐藏下划线
                              // border: InputBorder.none,
                            ),
                            // 光标颜色
                            cursorColor: Coloors.iconModal,
                          ),
                        ),
                        Row(
                          children: [
                            Checkbox(
                              value: true,
                              shape: CircleBorder(),
                              activeColor: Coloors.iconModal,
                              onChanged: (value) {
                                setState(() {
                                  _isChecked = value!;
                                });
                              },
                            ),
                            const Text('已仔细阅读并同意'),
                            GestureDetector(
                              child: const Text(
                                '《隐私协议》',
                                style: TextStyle(color: Coloors.iconModal),
                              ),
                            ),
                            const Text('和'),
                            GestureDetector(
                              child: const Text(
                                "《免责声明》",
                                style: TextStyle(color: Coloors.iconModal),
                              ),
                            ),
                          ],
                        ),
                        SizedBox(height: 20),
                        Container(
                          width: 400,
                          height: 50,
                          child: ElevatedButton(
                              style: ButtonStyle(
                                  backgroundColor: MaterialStateProperty.all(
                                      Coloors.iconModal), //背景颜色
                                  foregroundColor: MaterialStateProperty.all(
                                      Colors.white), //文字颜色
                                  shape: MaterialStateProperty.all(
                                      RoundedRectangleBorder(
                                    borderRadius:
                                        BorderRadius.circular(22), //设置圆角
                                  ))),
                              onPressed: () {},
                              child: const Text(
                                "登录",
                                style: TextStyle(fontSize: 20),
                              )),
                        ),
                      ],
                    ),
                  ),
                ],
              ),
            ),
          ),
        );
      },
    );
  }
```

在使用 getX 时候使用不需要使用 context。

```dart
// 调用
.....省略代码.....
Get.bottomSheet(
  isScrollControlled: true,
  backgroundColor: Colors.transparent,
  AnimatedPadding(
    padding: EdgeInsets.only(
    // 下面这一行是重点
    bottom: MediaQuery.of(Get.key.currentContext!)
        .viewInsets
        .bottom,
    ),
    duration: Duration.zero,
    child: loginWidget(),
  ),
);
.....省略代码.....

Widget loginWidget() {
    var loginHead = Row(
      mainAxisAlignment: MainAxisAlignment.spaceBetween,
      children: [
        Expanded(
          child: Row(
            children: const [
              Icon(Icons.swap_horiz_outlined,
                  size: 30, color: Coloors.iconModal),
              Text(
                "注册",
                style: TextStyle(
                    fontSize: 18,
                    fontWeight: FontWeight.w600,
                    color: Coloors.iconModal),
              ),
            ],
          ),
        ),
        const Center(
            child: Text(
          "登录",
          style: TextStyle(fontWeight: FontWeight.bold, fontSize: 22),
        )),
        //关闭图标
        Expanded(
          child: Row(
            mainAxisAlignment: MainAxisAlignment.end,
            children: [
              GestureDetector(
                onTap: () {
                  //关闭底部抽屉
                  Get.back();
                },
                child:
                    const Icon(Icons.close, size: 30, color: Coloors.iconModal),
              ),
            ],
          ),
        ),
      ],
    );
    /**
     *  用户名输入框
     */
    var userNameInput = Container(
      margin: const EdgeInsets.only(left: 10),
      decoration: BoxDecoration(
          color: Colors.white60,
          border: Border.all(color: Colors.white60, width: 0.5),
          borderRadius: BorderRadius.circular((20.0))),
      child: TextField(
        decoration: InputDecoration(
          border: const OutlineInputBorder(
            borderSide: BorderSide.none,
            borderRadius: BorderRadius.all(Radius.circular(20)),
          ),
          //背景圆角
          contentPadding: const EdgeInsets.fromLTRB(20, 10, 10, 15),
          filled: true,
          hintText: "请输入用户名",
          hintStyle: TextStyle(
            fontSize: 16.sp,
            fontWeight: FontWeight.w600,
            color: Coloors.greyDark,
          ),
          // 隐藏下划线
          // border: InputBorder.none,
        ),
        // 光标颜色
        cursorColor: Coloors.iconModal,
      ),
    );
    var passWordInput = Container(
      margin: const EdgeInsets.only(left: 10),
      decoration: BoxDecoration(
          color: Colors.white60,
          border: Border.all(color: Colors.white60, width: 0.5),
          borderRadius: BorderRadius.circular((20.0))),
      child: const TextField(
        decoration: InputDecoration(
          border: OutlineInputBorder(
            borderSide: BorderSide.none,
            borderRadius: BorderRadius.all(Radius.circular(20)),
          ),
          //背景圆角
          contentPadding: EdgeInsets.fromLTRB(20, 10, 10, 15),
          filled: true,
          hintText: "请输入密码",
          hintStyle: TextStyle(
            fontSize: 16,
            fontWeight: FontWeight.w600,
            color: Coloors.greyDark,
          ),
          // 隐藏下划线
          // border: InputBorder.none,
        ),
        // 光标颜色
        cursorColor: Coloors.iconModal,
      ),
    );
    var loginButton = Container(
      width: 400,
      height: 50,
      child: ElevatedButton(
          style: ButtonStyle(
              backgroundColor:
                  MaterialStateProperty.all(Coloors.iconModal), //背景颜色
              foregroundColor: MaterialStateProperty.all(Colors.white), //文字颜色
              shape: MaterialStateProperty.all(RoundedRectangleBorder(
                borderRadius: BorderRadius.circular(22), //设置圆角
              ))),
          onPressed: () {},
          child: const Text(
            "登录",
            style: TextStyle(fontSize: 20),
          )),
    );
    var empowerTitle = Row(
      children: [
        Obx(() => Checkbox(
              value: state.isChecked.value,
              shape: CircleBorder(),
              activeColor: Coloors.iconModal,
              onChanged: (value) {
                debugPrint("授权勾选框点击$value");
                logic.check(value!);
              },
            )),
        const Text('已仔细阅读并同意'),
        GestureDetector(
          child: const Text(
            '《隐私协议》',
            style: TextStyle(color: Coloors.iconModal),
          ),
        ),
        const Text('和'),
        GestureDetector(
          child: const Text(
            "《免责声明》",
            style: TextStyle(color: Coloors.iconModal),
          ),
        ),
      ],
    );
    return Container(
      height: 315.h,
      decoration: BoxDecoration(
          color: Get.theme.primaryColor,
          borderRadius: const BorderRadius.only(
              topLeft: Radius.circular(25.0), topRight: Radius.circular(25.0))),
      child: Padding(
        padding: const EdgeInsets.all(30.0),
        child: Column(
          children: [
            loginHead,
            SizedBox(height: 30.w),
            Expanded(
              child: Column(
                children: [
                  userNameInput,
                  const SizedBox(
                    height: 15,
                  ),
                  passWordInput,
                  empowerTitle,
                  const SizedBox(height: 20),
                  loginButton,
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
```

在上述代码中值得注意的一点是
在Flutter中，MediaQuery.of(context).viewInsets可以用于获取屏幕底部或顶部的安全区域大小。
使用了Get.key.currentContext来获取当前上下文（context），然后调用MediaQuery.of()方法获取viewInsets.bottom属性。这个属性表示屏幕底部的安全区域大小，通常会被键盘遮挡住。因此，如果你需要在bottom sheet中显示一些内容，并且希望它在键盘弹出时不被遮挡住，可以使用这个属性来设置bottomSheet的位置偏移量。

具体来说，你可以将bottomSheet的位置偏移量设置为viewInsets.bottom的值，从而让bottomSheet在键盘弹出时自动向上移动，避免被键盘遮挡住。
