---
title: HashMap
date: 2021-12-05 13:42:04.356
updated: 2021-12-05 13:42:04.356
url: https://what-yes.top/archives/hashmap
categories: 
tags: 树 | java
---

# HashMap
## 写在前面
果然屠龙者终变恶龙，以前觉得某dn博客全是copy的，没有水平，现在自己也在copy了hhhhhhhhh

今天看了看HashMap，但让我意想不到的事情是：当我给舍友讲的时候，他突然说这不是数据结构课上学的东西吗？回忆一下，好像真的。我......

emmm...我真的是懒得画图来介绍这个（当然也因为我并不是太了解）

所以我放几篇文章的链接，但是直接看可能看不懂，前提得了解hash冲突，红黑树吧；然后就是你真的想学一下hashmap，有这几点应该就差不多了

## 简单介绍HashMap以及1.7和1.8中的区别：

[hashmap底层实现原理](https://blog.csdn.net/qq_43370771/article/details/111353046?ops_request_misc=%7B%22request%5Fid%22%3A%22163867936716780271926816%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163867936716780271926816&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-111353046.pc_search_result_cache&utm_term=hashmap底层实现原理&spm=1018.2226.3001.4187)

HashCode函数是对`key`来使用

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

equals函数是比较键值是否相等

```java
public final boolean equals(Object o) {
    if (o == this)
        return true;
    if (o instanceof Map.Entry) {
        Map.Entry<?,?> e = (Map.Entry<?,?>)o;
        if (Objects.equals(key, e.getKey()) &&
            Objects.equals(value, e.getValue()))
            return true;
    }
    return false;
}
}
```

![查看源图像](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/1597914836534882.png)

我认为，`hash`函数就是确定值是哪一行的；再通过`equals`确定值是哪一列的

## 详细介绍

[hashmap扩容](https://blog.csdn.net/lkforce/article/details/89521318?ops_request_misc=%7B%22request%5Fid%22%3A%22163868005816780265444627%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163868005816780265444627&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-89521318.pc_search_result_cache&utm_term=hashmap扩容机制&spm=1018.2226.3001.4187)

这篇博客对Hashmap进行了更详细的介绍，扩容机制也讲得很清楚。

### 一些相关的题目

[面渣逆袭：HashMap追魂二十三问 - 三分恶 - 博客园 (cnblogs.com)](https://www.cnblogs.com/three-fighter/p/15631937.html)

其中转红黑树时链表长度阈值为什么是8（table>64）？扩容因子为什么是0.75？扩容机制实现等问题回答得真的巨棒！