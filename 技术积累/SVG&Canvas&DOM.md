# Svg Canvas DOM

## web常见的渲染元素形式和特点

## **html + css** :

1. 可以绘制一些规则简单图形，但是图形数据上的语义化不是很友好；

```css

div {
		width: 0;
    height: 0;
    border: 100px solid #000;
    border-top-color: red;
    border-bottom-color: yellow;
    border-left-color: blue;
    border-right-color: green;
}
```

1. 坐标系： 左上角为原点

## SVG:

1. 用xml语法来描述图形的一种格式。因为基于数学来绘制图像，然后填充颜色，最后呈现的图形是一种矢量图；
2. svg 是 css + html绘制一些不规则图形的补充；
3. svg的图形有相关的元素组成，可以对其进行一些js操作，如事件绑定等；
4. svg有一些自带的属性(properties：width, height,路径,阴影等)，svg的一些属性可以用css来设置（attributes：fill, stroke等);

```html
<svg  height="210" width="500">
  <polygon points="10,10 50,190 10,210"
  style="fill:lime;stroke:purple;stroke-width:1"/>
</svg>
```

1. 坐标系：无viewBox属性，同html,有的话，viewBox = (x, y, width, height)

## **canvas：**

1. canvas提供两种绘图能力，一般2D就指canvas 2D, 3D就指webGL；
2. 调用canvas提供的绘图api，调用绘图指令绘制，是一种指令式的绘图系统；
3. 因为canvas是操作画布上下文的，所以在渲染中不存在重排和重绘，绘制庞大复杂图形比较友好。
4. canvas所有绘制的图形都在一个画布上面，想单一控制某个元素不友好。
5. 坐标系: 左上角为原点坐标，右下角由画布width, height

## 使用场景

**svg**:

1. 渲染矢量图标；
2. 元素少，简单的图形；

**canvas**:

1. 复杂庞大图像的渲染；
2. 3d，需要大量计算的选择webGL;

整体来说：SVG和canvas可以结合使用，可以将SVG在img标签中渲染, drawImage绘制在canvas上。

1. 坐标系转换：对于已知图形原点和坐标系，可以对整个画布进行平移动和缩放等，实现对坐标系的转换。如下：默认左下坐标就可以变成原点在x, y节点，坐标轴，y轴正方向向上，x轴正方向向右
    
    ```jsx
    const ctx = canvas.getContext('2d');
    ctx.translate(x, y);
    ctx.scale(1, -1);
    
    ```
    

## 业务场景的选型思考

1. 使用canvas来绘制模式预览，放弃使用html + css;

composer mode业务中，模式的对图片，视频，文字的预览选择html + css; 在频繁的变化中，内容的变化，体验上会有闪的一下效果，因为元素的变化导致页面的重排和重绘，做不到无缝切换；后续采用canvas来绘制，可以做到内容的无缝切换。

1. 在360全景中插件化氛围灯渲染，采用svg， 放弃使用html + css

利用phone-shpere-view做全景图，插件化支持框选区域做氛围灯效果，因为会涉及图片的转动，放大缩小，采用svg做氛围灯的一个渲染，矢量图，图片变动中，svg矢量图完美贴合；html + css会有转动过程中，采用transform偏移后使得氛围灯和原图不是很贴合。