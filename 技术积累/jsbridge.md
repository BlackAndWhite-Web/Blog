 ## jsbridge通信
 hybrid APP的混合开发，涉及到webview, native的相互通信，比如webview需要调用native的拍照，扫码等功能；或者native推送一些信息给webview, 就会涉及到相互通信。目前解决这类通信的方式是jsbridge， 基本原理是双向协定协议，双向通信。


### 通信的解决方案
####  native -> webview
因为js可以在js解释器中，直接运行，可以把webview就当作js解释器直接执行js;
```
webview.evaluateJavascript('jscode')
```

#### webview -> native
1. url schema: 因为webview中的所有请求，加载都可以被native拦截，拦截后就可以直接调用native的方法;
```js
location.href = 'jsbridge://showToast?text=hello'
```
2. 注入 API: webview提供context,供web调用native相关api; 前提是app中native将相关接口注入到js context中；

#### 双向通信，有来有回，callback的
请求的时候带上callbackid; 一方处理完以后，主动调用回来；

## 小程序内部通信
- 渲染线程， 逻辑线程，采用的是双线程；
- 基于双线程基于jsbridge进行通信；


## 参考链接
[深入浅出JSBridge：从原理到使用](https://juejin.cn/post/6936814903021797389#heading-4)




