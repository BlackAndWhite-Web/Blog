## cookie和session
- 因为http协议是无状态的，所以无法记录用户的会话信息。cookie和session就是用来标记用户和存储一些个性化信息。

### cookie
服务端生成并伴随响应体存储在浏览器中

### session
session是一个在服务端的一个概念，用来存储用户凭证，标识用户，通常可以存储在内存中,或者外接存储中间件(redis等), 或者以cookie的形式存储在浏览器。

### Egg中的cookie, session实现
- Egg的context提供了一系列对cookie, session的读取操作api, 内部实现在Egg-session,Egg-cookie中，并以插件的形式集成。

### Egg-Session
- 以插件实现, 内部包装了koa-session,并将koa-session以中间件的形式继承到框架中；
```js
  app.config.coreMiddleware.push('session');
```

### koa-Session
- 实现了一个中间件，并将session相关操作扩展到context;
```js
Object.defineProperties(context, {
    session: {
      get() {
        return this[CONTEXT_SESSION].get();
      },
      set(val) {
        this[CONTEXT_SESSION].set(val);
      },
      configurable: true,
    },
})
```
### egg-cookies
- 封装了cookies相关的读写操作，配置合并相关的操作;
- 在egg中引入egg-cookie, 并在egg context中扩展，实例化egg-cookies后，将cookies挂在context上;
```js
// egg/app/extend/context.js
const proto = module.exports = {
  /**
   * Get the current visitor's cookies.
   */
  get cookies() {
    if (!this[COOKIES]) {
      this[COOKIES] = new this.app.ContextCookies(this, this.app.keys, this.app.config.cookies);
    }
    return this[COOKIES];
  },
}
```

### egg中通过配置session key值，最后将session写入cookie存储分析
- 在config中配置session相关配置信息；
```js
    config.session = {
        key: 'MA_SESS',
        httpOnly: true,
        secure: false,
        signed: true,
        domain: 'xxx'
    };
```
- koa-session中将传入的配置进行合并格式化处理;
- 在session中，如果外部为传入externalKey，会生成一个externalKey, 用来做寻找session的唯一用户标识符，并将其写入cookie；并将session存入store中；
```js
  async save(changed) {
    const opts = this.opts;
    const key = opts.key;
    const externalKey = this.externalKey;
    // set expire for check
    if (maxAge === 'session') {
      // do not set _expire in json if maxAge is set to 'session'
      // also delete maxAge from options
      opts.maxAge = undefined;
      json._session = true;
    } else {
      // set expire for check
      json._expire = maxAge + Date.now();
      json._maxAge = maxAge;
    }

    // save to external store
    if (externalKey) {
      debug('save %j to external key %s', json, externalKey);
      if (typeof maxAge === 'number') {
        // ensure store expired after cookie
        maxAge += 10000;
      }
      await this.store.set(externalKey, json, maxAge, {
        changed,
        ctx: this.ctx,
        rolling: opts.rolling,
      });
      if (opts.externalKey) {
        opts.externalKey.set(this.ctx, externalKey);
      } else {
        this.ctx.cookies.set(key, externalKey, opts);
      }
      return;
    }

```    

### 项目
- source设置的是在localhost域名；在localhost.xx.com去获取cookie没有获取到；
- 一级域名:baidu.com ;二级域名: a.baidu.com
- 在cookie中，一级域名的cookie，二级域名下可以获取到；反之不行；

### 参考文档
![从koa-session中间件源码学习cookie与session](https://segmentfault.com/a/1190000012412299)