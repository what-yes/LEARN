---
title: 给网站配置域名
date: 2021-11-29 13:14:37.965
updated: 2021-11-29 13:15:18.241
url: https://what-yes.top/archives/gei-wang-zhan-pei-zhi-yu-ming
categories: 博客
tags: nginx | 服务器 
---

# 给网站配置域名

这篇博客只是为了方便日后本人日后配置，所以比较简陋。

1. 有一个公网IP。
2. 有一个域名（在`name.com`诸如此类的网站购买）。
3. 去`cloudflare`配置域名解析。
4. `cloudflare`会提供它的域名解析服务器网址，我们得去购买域名的网站比如`name.com`修改域名解析服务器的网址为`cloudflare`所提供的。
5. 在服务器配置`nginx`反向代理。
6. （可以在`cloudflare`配置`cdn`服务，这样就不能通过域名查到ip了

具体步骤我都没有写，正如开始所说，这并不是一个教程，这只是为了防止我将来重新配置时忘了某个步骤。

> 本文链接 https://what-yes.top/archives/gei-wang-zhan-pei-zhi-yu-ming