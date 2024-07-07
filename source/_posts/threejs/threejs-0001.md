---
title: 记录一下在vue3中使用threejs的过程
date: 2024-07-06 22:56:50
tags:
    - three.js
cover: 'https://pans.ahuaaa.cn/img/vue.png'
top_img: 'https://pans.ahuaaa.cn/img/vue.png'
main_color: "#193d31"
---
## 效果图

![全屏效果图](https://cdn.cbd.int/ahua666-panimg@1.0.57/img/20240706225341.png)

## 创建一个vue3项目

```shell
pnpm create vue@latest
```
一步一步选择,创建成功后进入项目目录
执行

```shell
pnpm install
pnpm run dev
```

## 安装three.js

这一步建议开启魔法🔮,不然可能会失败

```shell
pnpm install three
pnpm install @types/three
```

## 演示代码(包括注释)
运行完成打开就是效果图的效果
```vue

<template>
  <div id="three"></div>
</template>

<script lang='ts' setup>
  import * as THREE from 'three'
  import {OrbitControls} from 'three/examples/jsm/controls/OrbitControls'
  import {onMounted} from 'vue'

  //创建一个三维场景
  const scene = new THREE.Scene()
  //添加光源
  const ambient = new THREE.AmbientLight(0xffffff, 0.5),
      light1 = new THREE.PointLight(0xffffff, 0.4),
      light2 = new THREE.PointLight(0xffffff, 0.4)

  scene.add(ambient)
  light1.position.set(200, 300, 400)
  scene.add(light1)
  light2.position.set(-200, -300, -400)
  scene.add(light2)
  //创建一个透视相机
  const width = window.innerWidth, height = window.innerHeight,
      camera = new THREE.PerspectiveCamera(45, width / height, 1, 1000)
  //设置相机位置
  camera.position.set(200, 200, 200)
  //设置相机方向
  camera.lookAt(0, 0, 0)

  //创建辅助坐标轴
  const axesHelper = new THREE.AxesHelper(100)
  scene.add(axesHelper)

  //创建一个物体（形状）
  const geometry = new THREE.BoxGeometry(100, 100, 100)
  //创建材质（外观）
  const material = new THREE.MeshLambertMaterial({
    color: 0x00ffff,//设置材质颜色
    transparent: true,//开启透明度
    opacity: 0.5 //设置透明度
  })
  //创建一个网格模型对象
  const mesh = new THREE.Mesh(geometry, material)
  //把网格模型添加到三维场景
  scene.add(mesh)

  //创建一个WebGL渲染器
  const renderer = new THREE.WebGLRenderer()
  renderer.setSize(width, height)
  renderer.render(scene, camera)

  const controls = new OrbitControls(camera, renderer.domElement)
  controls.addEventListener('change', () => {
    renderer.render(scene, camera)
  })
  onMounted(() => {
    document.getElementById('three')?.appendChild(renderer.domElement)
  })
</script>

<style>
  body {
    margin: 0;
    padding: 0;
  }
</style>

```
