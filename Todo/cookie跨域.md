- fetch请求，浏览器安全限制，不是同源的cookie不能携带在request头部；需要修改头部配置；
- fetch 不会发送跨域 cookies，除非你使用了 credentials 的初始化选项。（自2018 年 8 月以后，默认的 credentials 政策变更为 same-origin);
```
credentials: include
```