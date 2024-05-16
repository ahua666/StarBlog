---
title: Vue3+three.js大波浪效果
tags: 
      - three.js
      - Vue
      - 前端
categories: 前端
# top_img: 'https://s1.ax1x.com/2022/10/21/x6t29O.png'
cover: 'https://registry.npmmirror.com/ahua666-panimg/1.0.49/files/img/x6t29O.png'
abbrlink: 25998
date: 2022-03-26 19:25:31
---

# 效果图

![效果图](https://registry.npmmirror.com/ahua666-panimg/1.0.49/files/img/x6t29O.png)

# 示例
{% note info simple %}
看下示例[效果页面](https://pans.ahuaaa.cn/three/wave.html)

{% endnote %}


# Vue 代码
{% note info simple %}
Vue 使用前请先安装 three.js 依赖
{% endnote %}


```cmd
npm install --save three
```

{% note success simple %}
代码（复制可用） components 文件夹新建组件 waves.vue，直接复制代码如下，可直接运行：
{% endnote %}


```html
<template>
  <div id="iviewBg"></div>
</template>

<script>
  import * as THREE from "three";
  //显示右上角fps框
  // import Stats from "./stats.module";
  import { onMounted } from "vue";
  export default {
    props: {
      //控制x轴波浪的长度
      amountX: {
        type: Number,
        default: 50,
      },
      //控制y轴波浪的长度
      amountY: {
        type: Number,
        default: 50,
      },
      //控制点颜色
      color: {
        type: String,
        default: "#097bdb",
      },
      //控制波浪的位置
      top: {
        type: Number,
        default: 350,
      },
    },

    setup(props) {
      const SEPARATION = 100;

      // let stats;
      let container, camera, scene, renderer;

      let particles,
        count = 0;

      let mouseX = 0;

      let windowHalfX = window.innerWidth / 2;

      function init() {
        container = document.createElement("div");
        document.getElementById("iviewBg").appendChild(container);

        //创建透视相机
        camera = new THREE.PerspectiveCamera(
          75, //摄像机视锥体垂直视野角度
          window.innerWidth / window.innerHeight, //摄像机视锥体长宽比
          1, //摄像机视锥体近端面
          10000 //摄像机视锥体远端面
        );

        //设置相机z轴视野
        camera.position.z = 1000;

        //创建场景
        scene = new THREE.Scene();

        const numParticles = props.amountX * props.amountY;

        const positions = new Float32Array(numParticles * 3);
        const scales = new Float32Array(numParticles);

        let i = 0,
          j = 0;

        // 初始化粒子位置和大小
        for (let ix = 0; ix < props.amountX; ix++) {
          for (let iy = 0; iy < props.amountY; iy++) {
            positions[i] = ix * SEPARATION - (props.amountX * SEPARATION) / 2; // x
            positions[i + 1] = 0; // y
            positions[i + 2] =
              iy * SEPARATION - (props.amountY * SEPARATION) / 2; // z
            scales[j] = 1;
            i += 3;
            j++;
          }
        }

        //是面片、线或点几何体的有效表述。包括顶点位置，面片索引、法相量、颜色值、UV 坐标和自定义缓存属性值。使用 BufferGeometry 可以有效减少向 GPU 传输上述数据所需的开销
        const geometry = new THREE.BufferGeometry();
        geometry.setAttribute(
          "position",
          new THREE.BufferAttribute(positions, 3)
        );
        geometry.setAttribute("scale", new THREE.BufferAttribute(scales, 1));

        //着色器材质(ShaderMaterial),设置球的大小，颜色，等
        const material = new THREE.ShaderMaterial({
          uniforms: {
            //设置球的颜色
            color: { value: new THREE.Color(props.color) },
          },
          //控制球的大小
          vertexShader:
            "attribute float scale; void main() {vec4 mvPosition = modelViewMatrix * vec4( position, 1.0 );gl_PointSize = scale * ( 300.0 / - mvPosition.z );gl_Position = projectionMatrix * mvPosition;}",
          fragmentShader:
            "uniform vec3 color;void main() {if ( length( gl_PointCoord - vec2( 0.5, 0.5 ) ) > 0.475 ) discard;gl_FragColor = vec4( color, 1.0 );}",
        });

        //一个用于显示点的类。
        particles = new THREE.Points(geometry, material);
        //往场景中添加点
        scene.add(particles);

        //alpha - canvas是否包含alpha (透明度)。默认为 false。
        //渲染器的背景色默认为黑色，设置渲染器的背景色为透明
        renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        renderer.setPixelRatio(window.devicePixelRatio);
        renderer.setClearAlpha(0);
        renderer.setSize(window.innerWidth, window.innerHeight);
        container.appendChild(renderer.domElement);

        //显示右上角fps框
        // stats = new Stats();
        //   container.appendChild(stats.dom);

        container.style.touchAction = "none";
        //监听鼠标移动事件
        container.addEventListener("pointermove", onPointerMove);

        //调整波浪的位置
        container.style.position = "relative";
        container.style.top = `${props.top}px`;

        window.addEventListener("resize", onWindowResize);
      }

      function render() {
        camera.position.x += (mouseX - camera.position.x) * 0.05;
        camera.position.y = 400;
        camera.lookAt(scene.position);

        const positions = particles.geometry.attributes.position.array;
        const scales = particles.geometry.attributes.scale.array;

        // 设置粒子位置和大小
        let i = 0,
          j = 0;
        for (let ix = 0; ix < props.amountX; ix++) {
          for (let iy = 0; iy < props.amountY; iy++) {
            positions[i + 1] =
              Math.sin((ix + count) * 0.3) * 50 +
              Math.sin((iy + count) * 0.5) * 50;

            scales[j] =
              (Math.sin((ix + count) * 0.3) + 1) * 10 +
              (Math.sin((iy + count) * 0.5) + 1) * 10;

            i += 3;
            j++;
          }
        }

        particles.geometry.attributes.position.needsUpdate = true;
        particles.geometry.attributes.scale.needsUpdate = true;

        renderer.render(scene, camera);

        count += 0.1;
      }

      function onWindowResize() {
        windowHalfX = window.innerWidth / 2;
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
      }

      //监听鼠标移动事件
      function onPointerMove(event) {
        console.log(event);
        if (event.isPrimary === false) return;
        mouseX = event.clientX - windowHalfX;
      }

      function animate() {
        requestAnimationFrame(animate);
        render();
        //fps 实时更新
        // stats.update();
      }

      onMounted(() => {
        init();
        animate();
      });
      return {};
    },
  };
</script>

<style lang="scss" scoped>
  #iviewBg {
    width: 100%;
    height: 100vh;
    background: url("../assets/wavesBg.png") no-repeat;
    overflow: hidden;
  }
</style>
```

# 使用

直接在 login 登录页面引入组件使用

```html
<template>
  <wavesBg :top="250"></wavesBg>

  <div class="login-container"></div>
</template>

<script>
  import wavesBg from "../components/wavesBg";
  export default {
    name: "",
    components: {
      wavesBg,
    },
    setup() {
      return {};
    },
  };
</script>
```


# html版代码


```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"
    />
    <title>大波浪动画</title>
    <style type="text/css">
      body {
        margin: 0;
        background: #203260;
        overflow: hidden;
      }
    </style>
  </head>

  <body>
    <div id="m-points"></div>
    <script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.js"></script>
    <script src="https://cdn.bootcss.com/three.js/r56/three.js"></script>
    <script type="text/javascript">
      var SEPARATION = 100,
        AMOUNTX = 50,
        AMOUNTY = 50;

      var container;
      var camera, scene, renderer;

      var particles,
        particle,
        count = 0;

      var mouseX = 0,
        mouseY = 0;

      var windowHalfX = window.innerWidth / 2;
      var windowHalfY = window.innerHeight / 2;

      function init(dom) {
        container = dom;
        camera = new THREE.PerspectiveCamera(
          75,
          window.innerWidth / window.innerHeight,
          1,
          10000
        );
        camera.position.z = 1000;
        scene = new THREE.Scene();
        particles = new Array();
        var PI2 = Math.PI * 2;
        var material = new THREE.ParticleCanvasMaterial({
          color: "#3c78d8",
          program: function (context) {
            context.beginPath();
            context.arc(0, 0, 1, 0, PI2, true);
            context.fill();
          },
        });
        var i = 0;
        for (var ix = 0; ix < AMOUNTX; ix++) {
          for (var iy = 0; iy < AMOUNTY; iy++) {
            particle = particles[i++] = new THREE.Particle(material);
            particle.position.x = ix * SEPARATION - (AMOUNTX * SEPARATION) / 2;
            particle.position.z = iy * SEPARATION - (AMOUNTY * SEPARATION) / 2;
            scene.add(particle);
          }
        }
        renderer = new THREE.CanvasRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        container.appendChild(renderer.domElement);
        document.addEventListener("mousemove", onDocumentMouseMove, false);
        document.addEventListener("touchstart", onDocumentTouchStart, false);
        document.addEventListener("touchmove", onDocumentTouchMove, false);
        window.addEventListener("resize", onWindowResize, false);
      }

      function onWindowResize() {
        windowHalfX = window.innerWidth / 2;
        windowHalfY = window.innerHeight / 2;
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
      }

      function onDocumentMouseMove(event) {
        mouseX = event.clientX - windowHalfX;
        mouseY = event.clientY - windowHalfY;
      }

      function onDocumentTouchStart(event) {
        if (event.touches.length === 1) {
          event.preventDefault();
          mouseX = event.touches[0].pageX - windowHalfX;
          mouseY = event.touches[0].pageY - windowHalfY;
        }
      }

      function onDocumentTouchMove(event) {
        if (event.touches.length === 1) {
          event.preventDefault();
          mouseX = event.touches[0].pageX - windowHalfX;
          mouseY = event.touches[0].pageY - windowHalfY;
        }
      }

      function animate() {
        requestAnimationFrame(animate);
        render();
      }

      function render() {
        camera.position.x += (mouseX - camera.position.x) * 0.05;
        camera.position.y += (-mouseY - camera.position.y) * 0.05;
        camera.lookAt(scene.position);
        var i = 0;
        for (var ix = 0; ix < AMOUNTX; ix++) {
          for (var iy = 0; iy < AMOUNTY; iy++) {
            particle = particles[i++];
            particle.position.y =
              Math.sin((ix + count) * 0.3) * 50 +
              Math.sin((iy + count) * 0.5) * 50;
            particle.scale.x = particle.scale.y =
              (Math.sin((ix + count) * 0.3) + 1) * 2 +
              (Math.sin((iy + count) * 0.5) + 1) * 2;
          }
        }
        renderer.render(scene, camera);
        count += 0.1;
      }
      let renderAnimation = (container) => {
        init(container);
        animate();
      };
      let domContainer = document.getElementById("m-points");
      let a = renderAnimation(domContainer);
    </script>
  </body>
</html>
```
