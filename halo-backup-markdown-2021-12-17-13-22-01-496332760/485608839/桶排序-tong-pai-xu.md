---
title: 桶排序
date: 2021-12-16 19:54:17.102
updated: 2021-12-16 19:55:56.256
url: https://what-yes.top/archives/tong-pai-xu
categories: 
tags: 算法 | 排序
---

# 桶排序

![image-20211216114903826](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211216114903826.png)

之前看`HashMap`的时候隐约看到里面提了一下桶排序，之前也知道有这么一个算法但一直不知道具体是什么，所以今天看了看。

思想：

1. 遍历数组找到最大值和最小值
2. 将最大值和最小值的差值划分为几个桶
3. 遍历数组将数组放入桶中，对桶中数组进行排序

时间 空间复杂度

![image-20211216115333454](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211216115333454.png)

这样看来确实和`HashMap`的思想是一样的。



看这个视频时，他还讲桶排序的两种具体排序算法：计数排序和基数排序。我以为又是什么高大上没学过的东西，结果找到这两个视频一看，好家伙！[计数排序](https://www.bilibili.com/video/BV1Wb41157ed?spm_id_from=333.999.0.0)是大一在一本书上看到的，[基数排序](https://www.bilibili.com/video/BV184411L79P?spm_id_from=333.999.0.0)是在数据结构课上学的。就这？当然我的意思不是说算法简单，我是指有些知识可能都学过，只是不知道它与某些别的知识的联系。

不过提到了计数排序的适用场景：高考的一个分数是多少名？就是数据量大但是范围很小的时候（比如高考几十万人，但只有750分）。