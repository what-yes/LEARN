---
title: 修改halo写博客时代码块背景颜色不易区分的问题
date: 2021-11-08 14:19:00.697
updated: 2021-11-09 12:56:47.73
url: https://what-yes.top/archives/change-halo-code-bgc
categories: 博客
tags: 博客
---

#### 最初颜色
这是最初的颜色，代码块背景和整个背景真的是融为一体了
![image.png](/upload/2021/11/image-b8bec5f52e41469c864cbabe66d0d158.png)
#### 修改后样式
![image.png](/upload/2021/11/image-aae7bc5cb53d4a24ace8fac4b6c9bb41.png)
#### 修改方法
`F12`在旁边找到
![image.png](/upload/2021/11/image-9917d73370cd4cf0b8eb2a979c558737.png)
在红色框框部分选取合适的背景颜色即可，但只是对于这次编辑博文有效，目前还没有找到合适的可以永久修改的办法。
因为我发现这个`css`好像是通过直接导入`github`中的。
就是[这个链接](https://cdn.jsdelivr.net/gh/cdnjs/cdnjs@master/ajax/libs/github-markdown-css/3.0.1/github-markdown.css)
或者说如果是本地文件，但我是通过`docker`弄的，我还不是很清楚如何找到我`halo`的所有文件。
2021-11-08