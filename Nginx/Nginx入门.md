# Nginx入门

## 安装

下载（window下载，传到服务器上）

解压

cd nginx

./configure

make

make install

## 常用命令

![image-20211124174449752](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211124174449752.png)

## 配置文件

### 全局块

其中是一些能影响nginx整体运行的指令。

比如 worker_processes 1 ; 支持并发数量为1.

### events块

主要影响Nginx服务器与用户的网络连接。

比如 worker_connnections 1024 ; 支持的最大连接数。

### http块

代理、缓存和日志定义等绝大部分功能和第三方模块的配置都在这里。

http块可以包括**http全局块**、server块。

#### http全局块

http全局块配置的指令包括文件引入、MIME-TYPE定义，日志自定义、连接超时时间、单链接请求数上限等。

#### server块

这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。

每个http块可以包括多个server块，而每个server块就相当于一个虚拟主机。

而每个server块也分为全局server块，以及可以同时包含多个location块。

##### 全局server块

最常见的配置是本虚拟主机的监听配置和本虚拟主机的名称或IP配置。

##### location块

一个 `server`块可以有多个 `location`。

这块的主要作用是基于 `Nginx`服务器接收到的请求字符串（例如server_name/uri-string）,对虚拟主机名称（也可以是IP别名）之外的字符串（例如前面的 /uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有第三方模块也在这里进行。

## 反向代理

### 目标-1

输入 `www.123.com` 跳到 tomcat服务器页面

开放端口

```shell
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload

# 查看开放的端口号
firewall-cmd --list-all
```

逻辑

![image-20211125095912030](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211125095912030.png)

nginx监听端口是80，直接域名访问域名默认是访问对应IP的80端口，因此可以起作用。

修改nginx配置文件

```shell
server{
	listen	80;
	server_name 192.168.17.129;
	
	location /{
		proxy_pass http://127.0.0.1:8080;
	}

}
```

目标-2

1. 使用nginx反向代理，根据访问的路径跳转到不同端口的服务中。nginx监听端口为9001.

2. 访问 http://192.168.17.129:9001/edu/ 直接跳转到 127.0.0.1:8080

   访问 http://192.168.17.129:9001/vod/ 直接跳转到 127.0.0.1:8081

(192.168.17.129是这次实验的内网ip)

```shell
server{
	listen	9001;
	server_name 192.168.17.129;
	
	location ~/edu/{
		proxy_pass http://127.0.0.1:8080;
	}
	
	location ~/vod/{
		proxy_pass http://127.0.0.1:8081;
	}

}
```

## 负载均衡

单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们说的负载均衡。

#### 目标

监听`80`端口的请求，分别发送到 8080或`8081`端口。

`192.168.17.129:8080` `192.168.17.129:8081`是相同的内容只不过部署在不同的地方。

监听80端口的访问，根据情况分发到不同的服务器上。

```conf
http{
	upstream myserver{
		server 192.168.17.129:8080;
		server 192.168.17.129:8081;
	}
	server{
		listen 80;
		server_name 192.168.17.129;
		
		location /{
			proxy_pass http://myserver
		}
	
	}
}
```

#### 负载均衡策略

##### 轮询（默认）

一个服务器一次。

##### 分配权重

在 `upstream`中可以分配权重。`weight`越大则越容易被分配。

```shell
upstream myserver{
		server 192.168.17.129:8080 weight=10;
		server 192.168.17.129:8081 weight=5;
	}
```

##### ip_hash

每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session问题。

```shell
upstream myserver{
		ip_hash;
		server 192.168.17.129:8080;
		server 192.168.17.129:8081;
	}
```

##### fair

根据相应时间分配，哪个短就选择哪个。

```shell
upstream myserver{
		server 192.168.17.129:8080;
		server 192.168.17.129:8081;
		fair;
	}
```

### 动静分离

#### 两种方式

1. 将动态请求和静态请求分开。

   动态请求：sql数据库查询之类的。

   静态请求：请求页面。

2. 将动态文件和静态文件分开。

`expire` 设置缓存过期时间。给资源设置一个过期时间，**过期时间内**访问这个页面时，会发送一个请求对比页面是否发生变化：如果没变，则返回状态码304；如果变了，则会从服务器重新下载，返回状态码200.

目标

将静态页面放在 `www/data/`下， 通过 `www/资源名称`来访问。

将动态资源放在 `image/data/`下， 通过 `image/资源名称`来访问。

配置

```shell
server {
	listen 80;
	sever_name 192.168.17.129;
	
	location /www/ {
		root /data/; #root 就是指定当我访问www时是到哪个路径下去找文件
		index index.html index.htm;
	}
	
	location /image/ {
		root /data/;
		autoindex on; #开启后，当访问 ip/image/ 时，页面会显示这个目录下所有文件
	}
}
```

`autoindex on`的效果

![image-20211125145905122](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211125145905122.png)

### 后话

对于 `server`块中的 `server_name`一直很好奇：

我有个域名A，我的公网IP是B。在 `server_name`那个地方我填 `A`或者B或者 `localhost`都可以，都可以实现访问。感觉有点多余。但是，刚刚搜到 [这篇文章](https://blog.csdn.net/Cheng_Kohui/article/details/82930464)。大概意思就是，

>  **server name 为虚拟服务器的识别路径。因此不同的域名会通过请求头中的HOST字段，匹配到特定的server块，转发到对应的应用服务器中去。**

可以设置多个 `server_name`，虽然监听的都是80端口，但不同域名对应的请求头中的`HOST`字段不同，因此会匹配到不同服务器中。

![image-20211125154920105](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211125154920105.png)





### 参考文章：[ nginx配置:server_name的作用_Kevin的博客-CSDN博客_nginx server_name使用](https://blog.csdn.net/Cheng_Kohui/article/details/82930464)















