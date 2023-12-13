---
title: flutter蓝牙
tags:
  - flutter
  - android
top_img: 'https://cdn.cbd.int/ahua666-panimg@1.0.30/img/20230731171324.png'
cover: 'https://cdn.cbd.int/ahua666-panimg@1.0.30/img/20230731171324.png'
categories:
  - flutter
abbrlink: 17288
date: 2023-05-10 15:26:39
---

## 引入

```dart pubspec.yaml
  flutter_reactive_ble: ^5.0.3
```

### Android

需要将以下权限添加到 AndroidManifest.xml 文件中：

```xml

<uses-permission android:name="android.permission.BLUETOOTH_SCAN" android:usesPermissionFlags="neverForLocation"/>
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" android:maxSdkVersion="30"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" android:maxSdkVersion="30"/>
```

如果用于BLUETOOTH_SCAN确定位置，请修改 AndroidManfiest.xml 文件以包含以下条目：

```xml

<uses-permission android:name="android.permission.BLUETOOTH_SCAN"
                 tools:remove="android:usesPermissionFlags"
                 tools:targetApi="s"/>
```

如果在应用程序中使用位置服务，请android:maxSdkVersion="30"从位置权限标签中删除

## 使用

建议连接蓝牙之前先获取权限

```dart
// 定义变量
StreamSubscription<DiscoveredDevice>? _streamSubscription;
StreamSubscription<ConnectionStateUpdate>? _connectionStreamSubscription;
List<DiscoveredDevice> deviceList = [];
var flutterReactiveBle = FlutterReactiveBle();
QualifiedCharacteristic? BLCharacteristic;
QualifiedCharacteristic? BLCharacteristic2;
// 需要连接的蓝牙名称
barcode= 'Test';

// 连接蓝牙
connectBluetooth() async {
    _streamSubscription = flutterReactiveBle.scanForDevices(
        withServices: [], scanMode: ScanMode.lowLatency).listen((device) {
      //code for handling results,返回的是可以扫描到的设备
      deviceList.add(device);
      print(device);

      if (device.name == barcode) {
        print('扫描到${barcode}');
        _streamSubscription?.cancel();
        // showToast("扫描到设备");
        _connectionStreamSubscription = flutterReactiveBle
            .connectToDevice(
          id: device.id,
          servicesWithCharacteristicsToDiscover: {},
          connectionTimeout: const Duration(seconds: 3),
        )
            .listen((connectionState) async {
          if (connectionState.connectionState ==
              DeviceConnectionState.connected) {
            print(" connectionState=${connectionState.deviceId}");

            List<DiscoveredService> services =
                await flutterReactiveBle.discoverServices(device.id);
            print('获取所有服务 $services');
            services.forEach((service) async {
              var characteristics = service.characteristicIds;
              //找我们的特征值
              print(characteristics);

              for (Uuid char in characteristics) {
                print('获取到的特征值$char');
                //通过uuid过滤出我们需要的特征值
                if (char.toString().contains("0000fff1")) {
                  print("找到我们特征值$char");
                  BLCharacteristic = QualifiedCharacteristic(
                    characteristicId: char,
                    serviceId: service.serviceId,
                    deviceId: device.id,
                  );
                  print(BLCharacteristic);
                    //设置通知
                  await setNotifiys2();
                }
                //假设我们用这个,获取到特征值后保存，可以用来读写
                if (char.toString().contains("0000fff2")) {
                  BLCharacteristic2 = QualifiedCharacteristic(
                    characteristicId: char,
                    serviceId: service.serviceId,
                    deviceId: device.id,
                  );
                  print('获取到可以发送数据的特征值:$BLCharacteristic');
                  final mtu = await flutterReactiveBle.requestMtu(
                      deviceId: device.id, mtu: 500);
                  print('修改mtu为${mtu}');
                  update();
                }

              }
            });
            // _discoverServices(deviceId);
          }
          // Handle connection state updates
        }, onError: (Object error) {
          print("error1 =$error");
          connectBluetooth();
          // Handle a possible error
        });
      }
    }, onError: (e) async {
      print("onError = $e");
      showToast("权限错误");
      getPermissions();
    });
  }
```

### 设置通知

用到上面定义的变量

```dart
Future<void> setNotifiys2() async {
    //可以监听外设端主动发送过来的消息
    await flutterReactiveBle
        .subscribeToCharacteristic(BLCharacteristic!)
        .listen((data) {
      // List<int> datas = data.removeLast();
      // 删除因为网关设备带来的RSSI乱码
      // data = data.sublist(0, data.length - 1);
      
      // 通过gbk解码
      String msg = gbk_bytes.decode(data);
      print(data);
      print(msg.toString());
      // code to handle incoming data
    }, onError: (dynamic error) {
      print(error);
      // code to handle errors
    });
  }
```

### gbk编码和解码

```dart pubspec.yaml
  gbk_codec: ^0.4.0
```

```dart
// 解码
gbk_bytes.decode(data) 
// 编码
gbk_bytes.encode(data) 
```

如果是十六进制，但是是以字符串的形式,例如 
`String hex = 'b2e2cad4';`

则需要先转换为字节数组，再进行解码

```dart
  String hex = 'b2e2cad4';
  hexStringToBytes(hex);
  // 将16进制字符串转换为字节列表
  List<int> hexStringToBytes(String hex) {
    List<int> bytes = [];
    for (int i = 0; i < hex.length; i += 2) {
      String part = hex.substring(i, i + 2);
      bytes.add(int.parse(part, radix: 16));
    }
    return bytes;
  }
```

## 发送一个测试的字符串

```dart
 await flutterReactiveBle.writeCharacteristicWithResponse(BLCharacteristic2!,
         value: [0xb2, 0xe2, 0xca, 0xd4]);
```

## 发送中文字符串

```dart
 await flutterReactiveBle
        .writeCharacteristicWithResponse(BLCharacteristic2!, value:gbk_bytes.encode('测试')
    )
```

## 分包发送数据

每次发送数据后 等待 2 秒钟，如果没有收到回复，就抛出异常

```dart
Future<void> sendHexDataInPackets2(
      String hexData,
      QualifiedCharacteristic characteristic,
      int packetSize,
      int delayMs,
      int maxRetries) async {
    // 计算总共需要发送的包数
    int numPackets = (hexData.length / (packetSize * 2)).ceil();
    // 测试命令 
    String start = '';
    print(start);
    await flutterReactiveBle
        .writeCharacteristicWithResponse(BLCharacteristic2!, value:gbk.encode(start)
    )
        .timeout(Duration(seconds: 5), onTimeout: () {
      throw TimeoutException("Packet write timed out");
    });
    final threshold2 = Duration(seconds: 2); // 最长等待时间为 2 秒钟
    final startTime2 = DateTime.now(); // 记录开始时间
    while (true) {
      if (state.listenForStatus == '回复的数据') {
        state.listenForStatus= '';
        break; // 状态符合要求，跳出循环
      }
      final elapsed = DateTime.now().difference(startTime2); // 计算已经过去的时间
      if (elapsed > threshold2) {
        showToast('发送失败');
        throw TimeoutException('Wait for status timed out'); // 超时，抛出异常
      }
      await Future.delayed(Duration(milliseconds: 5)); // 等待 10 毫秒后再次检查状态
    }
    List<String> packets = [];
    // 分包
    for (int i = 0; i < numPackets; i++) {
      // 获取要发送的数据段
      String segment = hexData.substring(
          i * packetSize * 2, min((i + 1) * packetSize * 2, hexData.length));
      packets.add(segment);
    }
    print('分包${packets.length}');
    showToast('分包个数${packets.length}');
    for (int i = 0; i < maxRetries; i++) {
      for (int j = 0; j < packets.length; j++) {
        try {
          // 将十六进制字符串转换为字节数组并写入 BLE 设备
          List<int> bytes = hexStringToUint8List(packets[j]);
          showToast('发送第${j}包');

          await flutterReactiveBle
              .writeCharacteristicWithResponse(BLCharacteristic2!, value: bytes)
              .timeout(Duration(seconds: 5), onTimeout: () {
            throw TimeoutException("Packet write timed out");
          });
        } catch (e) {
          print("Error sending packet $j in attempt ${i + 1}: $e");
          if (i == maxRetries - 1) {
            rethrow;
          } else {
            await Future.delayed(Duration(milliseconds: delayMs));
          }
        }
        // 延迟一段时间再发送下一个数据包
        await Future.delayed(Duration(milliseconds: delayMs));
      }
    }
  }
```

```dart
  ///16进制转Uint8List
  Uint8List hexStringToUint8List(String hexString) {
    List<String> hexList = hexString.split("");
    List<String> byteStrings = [];
    for (int i = 0; i < hexList.length; i += 2) {
      byteStrings.add(hexList[i] + hexList[i + 1]);
    }
    List<int> byteValues = byteStrings
        .map((byteString) => int.parse(byteString, radix: 16))
        .toList();
    return Uint8List.fromList(byteValues);
  }
```
