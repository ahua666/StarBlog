---
title: uniapp GBK编码解码 解决中文乱码(物联网开发)
abbrlink: 64988
date: 2023-01-12 22:18:49
tags: 
	- Vue
	- uniapp
	- 物联网
categories: 前端
top_img: 'https://pans.ahuaaa.cn/img/vue.png'
cover: 'https://pans.ahuaaa.cn/img/vue.png'
#sticky: 1000
feature: true
---
# 前言
最近因为太忙了，实在没有时间更新博客，最近因为在弄APP开发，特此记录下。

## 2023年3月2日10:33:44更新
沁恒CH582M单片机 582 583通用 
设置MTU方法 uni.setBLEMTU 需要加一秒延时

## 2023年2月24日22:15:24更新

因为RSSI问题 所以需要去掉解码库的判断是否为偶数的异常

## 编码库和解码库
因为前端是无法直接做到GBK编码转换的，所以用到了字典库。

{% note info simple %}
[编码库 urlEncodeGBK.js](https://pans.ahuaaa.cn/js/urlEncodeGBK.js)
{% endnote %}
{% note info simple %}
[解码库 Decoder.js](https://pans.ahuaaa.cn/js/Decoder.js)
{% endnote %}

## 引入
```javascript
	import urlEncodeGBK from '../utils/urlEncodeGBK.js';
	import Decoder from '../utils/Decoder.js';
```
## 编码
```javascript
let input= '测试编码'
let ss = encodegbk(input).replace(/%/g, '')
console.log("测试编码======>", ss)
//测试编码======>b2e2cad4b1e0c2eb
```
## 解码
```javascript
let msg = 'b2e2cad4bde2c2eb'
let ss = GBKHexstrToString2(msg)
console.log("测试解码======>", ss)
//测试解码======>测试解码
```
## 收到蓝牙接受的二进制数据后
```javascript
/**
 * 二进制转16进制
 */
ab2hex(buffer){
	const hexArr = Array.prototype.map.call(
	    new Uint8Array(buffer),
	    function(bit) {
		    return ('00' + bit.toString(16)).slice(-2)
	    }
	)
	return hexArr.join('')
}
//转中文
let two =this.ab2hex(characteristic.value)
GBKHexstrToString2(two)
```
