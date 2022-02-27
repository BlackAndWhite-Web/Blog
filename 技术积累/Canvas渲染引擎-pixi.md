# Canvas渲染引擎—pixi.js

## canvas渲染引擎比对

- pixi
- Cocos2d-js
- three.js

## 游戏引擎的入门知识

- 预加载：需要加载一些静态图，模型，动画，贴图等；
- 图层和组合
- 动画系统

## pixi基本概念

- 一个轻量级的2D渲染引擎；渲染引擎有webGL, canvas；默认采用webGL渲染引擎，渲染的时候可以利用GPU，使得渲染加速；
- **sprint:**  如css中的图片，在游戏引擎中，sprite是承载图像，可以调整图像的位置，大小等；
- **texture:**  在pixi中，可以使用loader来做缓存，缓存的是texture(纹理: 可以被GPU处理的图像)，但是对视频和音频就只做了预加载。
- application
- stage (舞台)： 根节点，所有的元素都是添加到舞台中才能得以展示，类似于html的根节点<html>

## pixi渲染原理

- **渲染原理：** 创建一个renderer渲染器， 获取到app.view，并添加到DOM  tree中，在renderer.render()过程中，传入一个displayobject(stage)作为根组件进行渲染；深度遍历，对每个节点进行渲染。
- 渲染视频
    - webGL支持拉取当前桢通过texImage2D渲染纹理；只不过渲染图像的时候，传递的是image,渲染video的时候传递video标签；
- 渲染图片
    - texImage2D
- 渲染文字
- 渲染svg：也是将svg转为image对象进行绘制
- 渲染音频
    - 采用插件, pixi.sound进行音频的一个播放处理；
    - 采用AudioContext，进行音频节点的连接和播放；

### webGL基本入门

webGL中所有的图形都是由点，线，三角形绘制而成的；图元： 点，线，三角形。

webGL的一个绘制过程：将图形的数据存入数据缓冲区，在绘制过程中，webGL从缓冲区读取数据，执行着色器的程序； drawArray绘制;

- 渲染管线： 上一步的结果作为下一步的输入，如一个管道，指的是渲染的一个流程。
- 顶点着色器：负责处理图形顶点信息；
- 片元着色器：负责处理图形像素信息；
- 光栅化：顶点着色器和图元提取像素点，给片元着色器执行代码过程；
- texImage2D 渲染图片；

### 实现播放音频两种形式

浏览器对音视频的自动播放有限制，为了用户体验，屏蔽了自动播放功能，需要用户交互行为后才能自动播放；如果是audio标签的就会有这个现在，如果是audioContext就可以做到自动播放；

1. <audio>标签做音频播放
2. 采用audioContext对象播放音频

## pixi做预览优化

- loader预加载
- 音视频自动播放： 视频自动播放，视频的音频播放是基于video标签实现？
- 修改sprint的属性,如：透明度，不重复渲染？

**参考链接**

 1. [Animating textures in WebGL]([https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Tutorial/Animating_textures_in_WebGL](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Tutorial/Animating_textures_in_WebGL))

1. [audioContext & audio 音频播放]([https://blog.csdn.net/win7583362/article/details/118604081](https://blog.csdn.net/win7583362/article/details/118604081))
2. (Web Audio 简易教程)[[https://mp.weixin.qq.com/s/4hsQz0uMdWs6JOJz9q3faA](https://mp.weixin.qq.com/s/4hsQz0uMdWs6JOJz9q3faA)]
3. (**Pixi.js 桃红色的高温温泉和水蒸汽**)[[https://wow.techbrood.com/fiddle/13020](https://wow.techbrood.com/fiddle/13020)]