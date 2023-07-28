---
title: Uniapp 蓝牙分包发送(物联网开发)
tags: 
    - uniapp
    - 物联网
    - 前端
categories: 前端
abbrlink: 20748
date: 2023-04-04 13:05:00
top_img: 'https://pans.ahuaaa.cn/img/vue.png'
cover: 'https://pans.ahuaaa.cn/img/vue.png'
---
## 前言

博客发布的一切破解补丁、注册机和注册信息及软件的解密分析文章仅限用于学习和研究目的；不得将上述内容用于商业或者非法用途，否则，一切后果请用户自负。 本站信息来自网络，版权争议与本站无关。您必须在下载后的24个小时之内，从您的电脑中彻底删除上述内容。如果您喜欢该程序，请支持正版软件，购买注册，得到更好的正版服务。如有侵权请邮件与我们联系处理。

## 问题

uniapp给蓝牙设备发送数据的时候，受到硬件和安卓平台限制，每次只能发送20个字节，如果超过20个字节，就可能发送失败，这个时候就需要分包发送。
如果硬件支持修改mtu，则可以一次性发送数据，将该发送方式的分包数调大即可。

## 解决方案

### 将十六进制格式的字符串转换成 ArrayBuffer

```js
  hexToArrayBuffer(hex) {
    const typedArray = new Uint8Array(
      hex.match(/[\da-f]{2}/gi).map((h) => parseInt(h, 16))
    );
    return typedArray.buffer;
  },
```

### 异步写入数据，返回 Promise 对象

```js
writeBLEData(deviceId, serviceId, characteristicId, buffer) {
    const chunkSize = 20;
    let index = 0;
    return new Promise((resolve, reject) => {
      function writeChunk() {
        if (index >= buffer.byteLength) {
          resolve();
          return;
        }
        const chunk = buffer.slice(index, index + chunkSize);
        index += chunkSize;
        console.log("执行写入数据:", chunk);
        uni.writeBLECharacteristicValue({
          deviceId: deviceId,
          serviceId: serviceId,
          characteristicId: characteristicId,
          value: chunk,
          success(res) {
            setTimeout(() => {
              writeChunk();
            }, 30);
          },
          fail(err) {
            console.log("发送数据失败，重试中...", err);
            setTimeout(() => {
              writeChunk();
            }, 30);
          },
        });
      }
      writeChunk();
    });
  },
```

### 调用

```js
sendBLEData(data, deviceId, serviceId, characteristicId) {
    const hex = encodegbk(data).replace(/%/g, "");
    const buffer = this.hexToArrayBuffer(hex);
    this.writeBLEData(deviceId, serviceId, characteristicId, buffer);
  }
```

此处的gbk编码可以看之前记录的gbk编码库

### 完整代码

```js
  // 将十六进制格式的字符串转换成 ArrayBuffer
  hexToArrayBuffer(hex) {
    const typedArray = new Uint8Array(
      hex.match(/[\da-f]{2}/gi).map((h) => parseInt(h, 16))
    );
    return typedArray.buffer;
  },
  // 异步写入数据，返回 Promise 对象
  writeBLEData(deviceId, serviceId, characteristicId, buffer) {
    const chunkSize = 20;
    let index = 0;
    return new Promise((resolve, reject) => {
      function writeChunk() {
        if (index >= buffer.byteLength) {
          resolve();
          return;
        }
        const chunk = buffer.slice(index, index + chunkSize);
        index += chunkSize;
        console.log("执行写入数据:", chunk);
        uni.writeBLECharacteristicValue({
          deviceId: deviceId,
          serviceId: serviceId,
          characteristicId: characteristicId,
          value: chunk,
          success(res) {
            setTimeout(() => {
              writeChunk();
            }, 30);
          },
          fail(err) {
            console.log("发送数据失败，重试中...", err);
            setTimeout(() => {
              writeChunk();
            }, 30);
          },
        });
      }
      writeChunk();
    });
  },
   sendBLEData(data, deviceId, serviceId, characteristicId) {
    const hex = encodegbk(data).replace(/%/g, "");
    const buffer = this.hexToArrayBuffer(hex);
    this.writeBLEData(deviceId, serviceId, characteristicId, buffer);
  }

```