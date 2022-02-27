## 插件化组合
- render => tick/loader

## application
```js
    this.renderer = autoDetectRenderer(options);

    // install plugins here
    Application._plugins.forEach((plugin) =>
    {
        plugin.init.call(this, options);
    });
```
- addsystem;

## 整个流程
- 创建渲染器；
- 遍历整个stage，进行render渲染；

- 创建缓冲区，将资源转换成纹理对象，写入缓冲区;
- 创建几何图形，确定顶点着色器，片元着色器，从缓冲区里面读取数据，drawArray;

