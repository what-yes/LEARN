---
title: 纪录一次由于不熟悉docker端口产生的BUG
date: 2021-11-25 11:47:59.653
updated: 2021-11-25 11:51:24.457
url: https://what-yes.top/archives/纪录一次由于不熟悉docker端口产生的bug
categories: Bug | Docker
tags: Bug | docker | nginx
---

## 纪录一次由于不熟悉技术产生的BUG

### 背景

写了一个简陋的springboot项目，我想把它部署到服务器的docker上；手头上有一个域名，于是乎还想绑定一个域名，所以得用nginx反向代理。（域名解析之前已经做好了）

**重点是docker的端口配置 `32769：9090`！！！！**

安装完nginx然后改配置文件

我想啊，nginx反向代理，会把公网:80 也就是通过域名直接访问的东西转发给 内网(127.0.0.1)吧，那内网的端口应该就是9090吧。所以我这样配置：

```conf
server {
    listen       80;
    server_name  what-yes.live;
    ....
    location / {
    	proxy_pass http://127.0.0.1:9090/;
    }
```

死活访问不了，出现的是这个界面

![image-20211125111920854](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211125111920854.png)

### 解决bug

#### 解决bug第一步，去看日志

cd `/usr/local/nginx/logs/error.logs`

![image-20211125112038570](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211125112038570.png)

重点是红框部分

然后去网上搜

#### 第二步 看错误信息

然后搜到了这样一张图

![image-20211125112208447](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211125112208447.png)

后来稀里糊涂解决问题后我才知道我的问题 `connection refused (while connecting toupstream)` 对应的错误说明

> upstream 挂掉或者不通

是什么意思：

就是我启动的服务在 32769，而我代理的却是 9090（因为我认为应该代理9090）。

#### 解决

最后我尝试着把9090改成32769然后就可以访问了。

~~不过我还是好奇~~ 好奇的时候大佬回我消息了

![image-20211125113532683](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211125113532683.png)

原来我一直弄错了，我一直以为 -p中一个是

~~内网访问的端口，一个是外网访问的端口。~~

刚刚才知道原来

**一个是docker内应用的端口，一个是映射到本机的端口**。

所以无论内网还是外网都是访问 映射到本机的端口

### 这是官网的介绍：

![image-20211125114505503](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211125114505503.png)