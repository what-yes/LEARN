---
title: forward 和 redirect
date: 2021-11-19 20:00:14.034
updated: 2021-11-19 20:00:14.034
url: https://what-yes.top/archives/forwardandredirect
categories: servlet
tags: servlet
---

`forward`是转发。由**服务器**完成，因此用户的浏览器（客户端）中url无变化。
`redirect`是重定向。由**客户端**完成，因此用户的浏览器中url有变化。本质是服务器告诉客户端应该访问哪个链接由客户端自己去访问。
转发的效率更高。
![image.png](https://oss.what-yes.top/halo-blog/image_1637322793846.png )
