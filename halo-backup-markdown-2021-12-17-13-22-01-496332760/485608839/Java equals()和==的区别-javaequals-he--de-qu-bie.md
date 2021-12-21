---
title: Java equals()和==的区别
date: 2021-12-05 12:06:55.772
updated: 2021-12-05 12:06:55.772
url: https://what-yes.top/archives/javaequals-he--de-qu-bie
categories: 
tags: java
---

# equals()和==的区别

java中的数据类型，可分为两类：

1. 基本数据类型，也称原始数据类型。`byte,short,char,int,long,float,double,boolean`
    他们之间的比较，应用双等号（`==`）,比较的是他们的值。

2. 复合数据类型(**类**)
    当他们用（`==`）进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个`new`出来的对象，他们的比较后的结果为`true`，否则比较后结果为`false`。

    JAVA当中所有的类都是继承于`Object`这个基类的，在Object中的基类中定义了一个`equals`的方法，这个方法的初始行为是比较对象的内存地 址，但在一些类库当中这个方法被`覆盖`掉了，如`String,Integer,Date`在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。

     对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的，因为**Object的equals方法也是用双等号（\==）**进行比较的，所以比较后的结果跟双等号（==）的结果相同。

> 内容取自：[Java中equals和==的区别 - BarneyZhang - 博客园 (cnblogs.com)](https://www.cnblogs.com/zhxhdean/archive/2011/03/25/1995431.html#:~:text=JAVA当中所有的类都是继承于Object这个基类的，在Object中的基类中定义了一个equals的方法，这个方法的初始行为是比较对象的内存地,址，但在一些类库当中这个方法被覆盖掉了，如String%2CInteger%2CDate在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。 对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的，因为Object的equals方法也是用双等号（%3D%3D）进行比较的，所以比较后的结果跟双等号（%3D%3D）的结果相同。)

