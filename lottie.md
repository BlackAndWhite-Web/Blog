




## lottie

### lottie是什么

lottie是Airbnb开源一种动画的解决方案，能够将高质量动画添加到任何原生应用，比如IOS， Android , React Native，web。lottie的出现使得各端渲染动画更加简单和统一；

![图片来自官网](http://upload-images.jianshu.io/upload_images/2825714-bf5ca464398b1755.gif)

### lottie的优点

1、lottie格式的动图，占用空间更小,加载性能更好；

2、灵活支持各种尺寸，不需要考虑适配问题；

3、跨平台，兼容各客户端;

4、可以对动画的暂停/播放，做相关的控制，操控动画等；

### lottie的缺点

1、lottie支持的动画效果有限，有些动画效果不支持，一部分是性能问题，一部分是还在不断的迭代中；

2、导出的json文件，如果设计师创建了很多图层，json文件大小会比较大，

3、生产lottie动图对设计师要求比较高，需要遵循制图的一些标准，否则导出来的json会渲染有误；

### lottie的生成

#### lottie动画制作过程

```js

json

AE(Bodymovin) ---------> lottie player

图片资源

```

#### 生成的json文件分析

![](https://user-gold-cdn.xitu.io/2018/9/3/1659ede1389454a3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

#### 什么情况会生成图片资源

背景：AE 制作动图，可能存在对一些位图编码失败，导出images位图相关图片资源，json中的assets的u + p中描述了对images引用关系；

什么情况会编码失败：依赖bodymovin这个插件对编码的支持情况，目前设计师反馈的还不存在该问题，还在观察中。

### lottie渲染

官方提供各平台渲染库

- [lottie-web](https://github.com/airbnb/lottie-web)

- [lottie-android](https://github.com/airbnb/lottie-android)

- [lottie-ios](https://github.com/airbnb/lottie-ios)

### vue中渲染lottie

vue-lottie基于lottie-web做了二次封装[vue-lottie](https://github.com/chenqingspring/vue-lottie)

#### 第一步: 安装依赖

```js

npm install --save vue-lottie

```

#### 引入lottie组件

```js

// vue-lottie 中src然后引入该lottle组件

<template>

<div ref="lavContainer" :style="style" />

</template>

<script>

import lottie from 'lottie-web';

export default {

props: {

options: {

type: Object,

required: true

},

height: Number,

width: Number

},

data() {

return {

style: {

width: this.width ? `${this.width}px` : '100%',

height: this.height ? `${this.height}px` : '100%',

overflow: 'hidden',

margin: '0 auto'

}

};

},

mounted() {

this.anim = lottie.loadAnimation({

container: this.$refs.lavContainer,

renderer: 'svg',

loop: this.options.loop !== false,

autoplay: this.options.autoplay !== false,

path: this.options.path,

// animationData: this.options.animationData,

rendererSettings: this.options.rendererSettings

}

);

this.$emit('animCreated', this.anim);

}

};

</script>

```

#### 业务中渲染lottie动画

```js

<lottie :options="defaultOptions" :height="400" :width="400" v-on:animCreated="handleAnimation"/>

```

#### 支持引入json的URL，和json源文件渲染

支持引入json的URL

```js

defaultOptions: {

path: 'https://aicompanion-image-bucket.obs.cn-east-2.myhuaweicloud.com/6279e2cb-4209-4c78-a292-6d53b80b1544.json'

}

```

json源文件渲染

```js

// 预先引入资源

import * as animationData from '@/assets/033005.json';

// 配置defaultOptions参数

defaultOptions: {animationData: animationData},

```

注意：path和animationData互斥；

### 官网web上传zip包渲染分析

[渲染地址](https://lottiefiles.com/preview)



