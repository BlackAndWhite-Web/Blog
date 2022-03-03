## docker
docker是一个开源的容器引擎，基于go编写。可以让开发者打包应用以及依赖到一个轻量级的容器中，并可以发布到任何linux机器中，容器之间使用沙箱机制，相互隔离的。

### docker镜像基本命令
```js
// 查看镜像
docker image
// 构建一个镜像
docker build -t [镜像名称] .
// 删除一个镜像
docker rmi nginx
// 查找镜像
docker search nginx
// 下载镜像
docker pull nginx
```

### docker容器基本命令
```js
// 查看容器
docker ps
// 删除容器
docker rm [容器id]
// 运行镜像
docker run -d -p 宿主机端口:容器端口 nginx
```
### Dockerfile配置文件
使用dockerfile构建自己的docker镜像，是一个文本文件，里面描述了构建镜像的细节

```js
// dockerfile
FROM nginx
ADD dist/ /usr/share/nginx/html    
ADD default.conf /etc/nginx/conf.d/default.conf
```

## nginx
nginx是一个高性能&轻量级的http服务器，也可以用来做反向代理和负载均衡

### 如何构建一个nginx镜像部署vue项目
- 将打包后的文件dist内部的文件全部写入到 nginx默认目录'usr/share/nginx/html'
- 自定义default.conf文件，可自定义一个配置，将其和/etc/nginx/conf.d/default.conf合并

```js
// default.conf
    server {
        listen       8080;
        location / {
            root   html;
            add_header Access-Control-Allow-Origin *;
            index  index.html index.htm index.htm;
        }
    }
```

## 参考
[十分钟学会用docker部署微服务](https://zhuanlan.zhihu.com/p/78295209)
[连前端都看得懂的《Nginx 入门指南》](https://juejin.cn/post/6844904129987526663)