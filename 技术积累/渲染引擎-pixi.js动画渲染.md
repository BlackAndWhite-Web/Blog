# 渲染引擎——PIXI.js 动画渲染

## Pixi.js简介

一个轻量级的2D渲染引擎，默认采用WebGL渲染器。我们可以不必深入关心WebGL API, 浏览器兼容性，创建渲丰富的渲染元素(图形，文字，图片，视频等)，交互丰富(支持拖拽，事件等)的应用和游戏。

## PIXI.js基本概念

pixi渲染原理 

基于pixi，创建渲染器和根节点——stage，所有需要展示的元素，add到stage展示；生成一颗节点树，默认支持嵌套元素，图层顺序等功能。

节点树如下：

![](https://s3.bmp.ovh/imgs/2022/01/a9935b78d01a58bc.png)

```jsx

const createScreen = (attr) => {
	 const app = new PIXI.Application({
	      width: attr.appWidth,
	      height: attr.appHeight,
	      backgroundColor: attr.bgColor,
	  });
	  app.stage.width = app.screen.width;
	  app.stage.height = app.screen.height;
	  document.body.appendChild(app.view);

		const text = new PIXI.Text(attr.content, attr.style);
    app.stage.addChild(text);
		const imageTexture = PIXI.Texture.from('examples/assets/flowerTop.png');
		const dude = new PIXI.Sprite(imageTexture);
		app.stage.addChild(dude);
}
```

渲染原理——如何将渲染器绘制到DOM

1. 创建renderer渲染器和渲染节点view, 获取view并将其插入到DOM中。

![](https://s3.bmp.ovh/imgs/2022/01/5f124434967f7272.png)

渲染原理——如何绘制元素，

1. 基于创建好renderer, 再创建根节点stage(类似DOM中的body节点)，然后在创建需要添加的图片Sprint/Text 元素，添加到stage根节点。最后调用render, 从根节点开始依次渲染和绘制。

![](https://s3.bmp.ovh/imgs/2022/01/0ec3e2db3fe3af08.png)

### Pixi.js 渲染器WebGL

Pixi内部会自动检测采用哪种渲染器，默认采用WebGL, 如浏览器不支持webGL渲染器，就降级采用Canvas渲染器。

默认选用webGL渲染器，是在渲染的时候可以利用GPU做渲染加速。texture(纹理)是GPU可以处理的图像数据，所以在渲染的时候，先将资源元素转成texture。

### 实现texture

- 资源转化成texture

PIXI提供将图像地址，img标签， canvas标签，video标签。将其图像转为texture。

```jsx
texture = new PIXI.Texture(new PIXI.BaseTexture.from(source: string | HTMLImageElement | HTMLCanvasElement | HTMLVideoElement | PIXI.BaseTexture, options))
```

- 创建纹理
    
    createTexture
    

### webgl 渲染图片流程图

- 图片资源转换成纹理对象，从纹理对象中获得坐标位置信息和颜色像素信息，传给着色器进行渲染

![绘制流程](https://s3.bmp.ovh/imgs/2022/01/4587d45a371ba889.jpeg)

### 固定帧动画图渲染

图片的动画的格式有gif, webp, apng, lotter等；PIXI默认不支持渲染图片动画。如何使得PIXI支持动效图？

动效图以gif为例，其实就是多张有时间限制的静态图压缩组合在一起，按照播放时长连续播放，形成连贯的动画。

第一步：解码gif图，提取图像信息，如width, height,图像基础信息。

采用omggif编解码库来解码gif;可以获取到gif图像的width, heigh, 序列帧数，每一帧图像信息

```jsx
const gif = new omggif(data);
gifWidth = gif.width,
gifHeight = gif.height,
gifFramesLen = gif.numFrames(),
```

第二步：将解码后的每一帧数据转成textures缓存；

```jsx
for(let i=0; i<gifFramesLen; i++){
      gifFrameInfo = gif.frameInfo(i);
      canvas = document.createElement('canvas');
      canvas.width = gifWidth;
      canvas.height = gifHeight;
      ctx = canvas.getContext('2d');
      imageData = ctx.createImageData(gifWidth, gifHeight);
      gif.decodeAndBlitFrameRGBA(i,imageData.data);
      ctx.putImageData(imageData, 0, 0);
      spriteSheet = new PIXI.BaseTexture.from(canvas);
      obj.textures.push(new PIXI.Texture(spriteSheet,new PIXI.Rectangle(0, 0, gifWidth, gifHeight)));
  };
```

第三步：利用定时器ticker逐帧渲染每一帧的 texture，形成连贯动画。

- 基于PIXI.Ticker，维护了一个计时器，里面运用requestAnimation来实现循环，
- ticker.add,将每一帧的texture渲染render推入到渲染队列，每次loop都触发render的执行。
- 在每一次render的时候，修改精灵纹理材质与当前的帧率相匹配

### 非固定帧动画——增量动画渲染

比如在一定时间内改变某一属性的位置信息，从而实现物品的平移效果

- 利用ticker定时器，在每一次requesetAnimationFrame时，将每次的rotation累加，从而实现动画效果。

```jsx
const app = new PIXI.Application();
document.body.appendChild(app.view);

// create a texture from an image path
const texture = PIXI.Texture.from('examples/assets/flowerTop.png');

// create a new Sprite using the texture
const dude = new PIXI.Sprite(texture);

// center the sprites anchor point
dude.anchor.set(0.5);

// move the sprite to the center of the screen
dude.x = app.screen.width / 2;
dude.y = app.screen.height / 2;

app.stage.addChild(dude);

app.ticker.add(() => {
    // just for fun, let's rotate mr rabbit a little
    dude.rotation += 0.1;
});
```