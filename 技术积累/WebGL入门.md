## 什么是webgl

> WebGL通过引入OpenGL ES 2.0的API，该API可以在HTML5 <canvas>元素中使用。利用GPU做渲染加速，可以实现绘制兼容web浏览器的2D,3D的一个图形库。

因为要和GPU打交道，所以利用WebGL绘制图形时，需要做很多底层的操作，操作图形每一步的渲染细节。并不像Canvas或者SVG等，学开发会绘图指令，就可以通过命令式的api绘制图形。
当然也有基于WebGL封装的渲染引擎，比如：three.js等，可以将底层API封装暴露出相对声明式的指令API，供开发使用。

## 简易Demo比对
-  基于Canvas绘制一个矩型
```js
function drawCanvasRect() {
    ctx.fillStyle = "#000";
    ctx.fillRect(0, 0, 100, 100);
}
```
通过命令式的写法，告诉canvas需要绘制的颜色，和矩型尺寸大小，就可以绘制出来。
- 基于WebGL绘制一个矩形
```js
function drawWebGLReact() {
    // 创建上下文
    const canvas = document.querySelector('canvas');
    const gl = canvas.getContext('webgl');
    // 利用GLSL编程语言编写的顶点着色器
    const vertex = `
      attribute vec2 position;

      void main() {
        gl_PointSize = 1.0;
        gl_Position = vec4(position * 0.5, 1.0, 1.0);
      }
    `;

    // 利用GLSL编程语言编写的片元着色器
    const fragment = `
      precision mediump float;
      void main()
      {
        gl_FragColor = vec4(1, 0, 0.5, 1);
      }    
    `;
    
    // 创建顶点着色器
    const vertexShader = gl.createShader(gl.VERTEX_SHADER);
    gl.shaderSource(vertexShader, vertex);
    gl.compileShader(vertexShader);

    // 创建片元着色器
    const fragmentShader = gl.createShader(gl.FRAGMENT_SHADER);
    gl.shaderSource(fragmentShader, fragment);
    gl.compileShader(fragmentShader);

    
    // 创建WebGL程序关联着色器；
    const program = gl.createProgram();
    gl.attachShader(program, vertexShader);
    gl.attachShader(program, fragmentShader);
    gl.linkProgram(program);
    gl.useProgram(program);

    // 设置顶点数据, 坐标体系是以中心点为起始点
    const points = new Float32Array([
      -1, -1,
      -1, 1,
      1, 1,
      1, -1
    ]);

    // 创建缓冲区，并将points数据写入缓冲区
    const bufferId = gl.createBuffer();
    gl.bindBuffer(gl.ARRAY_BUFFER, bufferId);
    gl.bufferData(gl.ARRAY_BUFFER, points, gl.STATIC_DRAW);

    // 将缓冲区的数据读取到GPU
    // 获取到WebGL程序中的postion变量地址
    const vPosition = gl.getAttribLocation(program, 'position');
    // 告诉这个属性怎么依次从缓冲区中读取数据。
    gl.vertexAttribPointer(vPosition, 2, gl.FLOAT, false, 0, 0);
    // 激活变量
    gl.enableVertexAttribArray(vPosition); 

    // 清空画布
    gl.clearColor(0, 0, 0, 0);
    gl.clear(gl.COLOR_BUFFER_BIT);
    // WebGL 就会将对应的 buffer 数组传给顶点着色器，绘制图形
    gl.drawArrays(gl.LINE_LOOP, 0, points.length / 2);

}
```
需要操控底层代码，创建着色器，创建程序，创建缓冲区...一步步通过渲染管线将绘制过程串联，最后绘制出来。

## WebGL相关基础概念
- 渲染管线: 类似流水管道，WebGL将一步步的渲染流程环环相扣，将上一步的结果当作下一步的输入，连接起来，类似一个管线的概念。
- 纹理对象: 在WebGL中, 图片，<Canvas>、<video>等都可以通过createTexture()转换成纹理对象。纹理对象就是一个数据序列，类似于顶点数据一样。
- 图元： 在WebGL中所有的图形都是由点，线，三角形绘制而成的；图元： 点，线，三角形。所有的图形都是由图元为基本单位构成。
- 缓冲区： 利用js将数据写入到缓冲区，供GPU可以读取到数据来进行绘制;
- 着色器: 着色器是用GLSL编程语言来编写的代码片段，分为顶点着色器，片元着色器。
    - 顶点着色器：处理图形的顶点信息，确定顶点位置，图形形状。
    - 片元着色器： 处理图形的像素信息,着色
- 光栅化：利用gl.drawArray将图元组合后的基本图形, 用像素来表达，获取到每个像素位置信息。给到下一步片元着色器来着色。


## WebGL绘制流程
- 创建上下文
- 创建顶点着色器；片元着色器
- 然后编写webgl程序，关联这两个着色器；
- 创建缓冲区，将数据写入缓冲区（GPU，是从缓冲区域读取数据）;
- 获取到顶点着色器中的变量，读取缓冲区数据；
- 最后调用gl.drawArrays并行绘制顶点，确定图形位置，形状。
- 运行着色程序

![绘制流程](https://s3.bmp.ovh/imgs/2022/01/4587d45a371ba889.jpeg)

## WebGL渲染流程
![渲染流程](https://s3.bmp.ovh/imgs/2022/01/bd5fe948d9b737f9.png)


## WebGL 2D渲染实践
### 渲染文字
渲染文字的三种方法
- canvas渲染
    - 创建canvas节点
    - 在canvas上设置相关问题；
    - 将canvas转为纹理绑定到webgl纹理上面；
- DOM绝对定位覆盖canvas
- 文字图片贴图(本质还是贴图)

```js

```
### 渲染图片
基本实现就是创建几何图片，将图片贴上去；主要的就是图片纹理坐标和几何坐标的一个映射。

- 实例化Image，加载image
- 创建纹理; 加载image,转换为纹理图像；text
- 图片纹理映射到
设置纹理对象裁剪参数；
绑定纹理对象；
传入图片
```js
    const image = new Image();
    image.src = 'https://webglfundamentals.org/webgl/resources/leaves.jpg';
    image.onload = function(data) {
      render(image)
    }
    const render = (image) => {
      // 创建上下午
      const canvas = document.querySelector("canvas");
      const gl = canvas.getContext("webgl");
      
      // 编写顶点着色器
      // 通过a_texCoord接收到纹理坐标，传递给v_texCoord， 传给片元着色器
      // 主要做到的就是一个纹理坐标映射到几何图形，绘制的正方形;
      // 将图形贴上去;
      const vertex = `
        attribute vec4 a_position;
        attribute vec2 a_texCoord;
        varying vec2 v_texCoord;
        void main() {
          gl_Position = a_position;
          v_texCoord = a_texCoord;
        }
      `;

      // 利用GLSL编程语言编写的片元着色器
      // 向texture2D传递纹理单位编号和纹理坐标
      // 就可以获取到纹理上的像素颜色来填充；
      const fragment = `
        precision mediump float;
        uniform sampler2D u_image;
        varying vec2 v_texCoord;
        void main()
        {
          gl_FragColor = texture2D(u_image, v_texCoord);
        }    
      `;
    
    // 创建顶点着色器
    const vertexShader = gl.createShader(gl.VERTEX_SHADER);
    gl.shaderSource(vertexShader, vertex);
    gl.compileShader(vertexShader);

    // 创建片元着色器
    const fragmentShader = gl.createShader(gl.FRAGMENT_SHADER);
    gl.shaderSource(fragmentShader, fragment);
    gl.compileShader(fragmentShader);

    const pointPos = [
      -1, 1,
      -1, -1,
      1, -1,
      1, 1,
      -1, 1
    ]

    const texCoord = [
      0, 1,
      0, 0,
      1,0,
      1,1,
      0, 1
    ]
  // 创建WebGL程序关联着色器；
  const program = gl.createProgram();
  gl.attachShader(program, vertexShader);
  gl.attachShader(program, fragmentShader);
  gl.linkProgram(program);
  gl.useProgram(program);

  // 创建缓存区，将数据写入
  const buffer = gl.createBuffer();
  gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
  gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(pointPos), gl.STATIC_DRAW);

  const texCoordbuffer = gl.createBuffer();
  gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
  gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(texCoord), gl.STATIC_DRAW);

const a_texCoord = gl.getAttribLocation(program, "a_texCoord");
const a_position = gl.getAttribLocation(program, "a_position");
gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
// 设置变量读取缓存区数据
gl.vertexAttribPointer(
    a_position,
    2,
    gl.FLOAT,
    false,
    Float32Array.BYTES_PER_ELEMENT * 2,
    0
);
// 激活变量
gl.enableVertexAttribArray(a_position);

gl.vertexAttribPointer(
    a_texCoord,
    2,
    gl.FLOAT,
    false,
    Float32Array.BYTES_PER_ELEMENT * 2,
    0
);
gl.bindBuffer(gl.ARRAY_BUFFER, texCoordbuffer);
gl.enableVertexAttribArray(a_texCoord);
    
      const texture = gl.createTexture();
      // 获取片元着色器
      const u_Sampler = gl.getUniformLocation(program, 'u_image');
      gl.pixelStorei(gl.UNPACK_FLIP_Y_WEBGL, 1);
      gl.bindTexture(gl.TEXTURE_2D, texture);
      gl.activeTexture(gl.TEXTURE0); // 激活0号纹理单元

      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.NEAREST);
      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.NEAREST);

      gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, image);
      gl.uniform1i(u_Sampler, 0); // 将0号纹理传给着色器

      gl.clearColor(0, 0, 0, 0);
      gl.clear(gl.COLOR_BUFFER_BIT);
      gl.drawArrays(gl.TRIANGLE_STRIP, 0, 5);
    }
    

    //  会采用相应的模式，从起点，绘制多少个顶点出来
    // 然后片元着色器就会去
```

### 渲染视频
- autoplay mute为true;

### 渲染动图,类似gif, apng等；
## WebGL 3D渲染实践


