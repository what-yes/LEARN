---
title: volatile
date: 2021-12-14 12:43:00.2
updated: 2021-12-14 12:43:00.2
url: https://what-yes.top/archives/volatile
categories: 
tags: 锁 | 多线程  | volatile
---

# volatile

本来想研究内存模型的，但发现视频主要只讲了`volatile`，也行吧，也学到了一些。

## Java内存模型

准确来说是Java线程内存模型。

JMM数据原子操作：

- `read`(读取):从主内存读取数据
- `load`(载入)∶将主内存读取到的数据写入工作内存
- `use`(使用)∶从工作内存读取数据来计算
- `assign`(赋值)∶将计算好的值重新赋值到工作内存中
- `store`(存储):将工作内存数据写入主内存
- `write`(写入):将`store`过去的变量值赋值给主内存中的变量
- `lock`(锁定):将主内存变量加锁，标识为线程独占状态
- `unlock`（解锁)∶将主内存变量解锁,解锁后其他线程可以锁定该变量![20211214111312](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/20211214111312.png)



## Volatile缓存可见性实现原理

底层实现主要是通过汇编`lock`前缀指令，它会锁定这块内存区域的缓存(缓存行锁定)并回写到主内存。

IA-32架构软件开发者手册对`lock`指令的解释:

1. 会将当前处理器缓存行的数据立即写回到系统内存。一般情况是**不会立即**写回主存的。
2. 这个写回内存的操作会引起在其他`CPU`里缓存了该内存地址的数据无效(`MESI`协议)；通俗讲就是会让`cpu`工作内存中的值无效，那么`cpu`再`use`的时候，就会从主内存中重新读取。

> volatile的lock是加在store之前的，而不是read前。

![20211214113055](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/20211214113055.png)

在运行对`volatile`修饰变量的赋值语句时的汇编代码(?)：

![image-20211214123951699](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211214123951699.png)

### `volatile`不保证原子性：

1. 假设线程A和线程B都是执行1000次`i++`操作。`i`用`volatile`进行修饰
2. 假设一开始`i`为0，当两个线程中都执行了`i++`操作，此时两个线程的工作内存中`i`的值都为1
3. 当线程2先一步将`i`写回主存，那么会刷新掉线程1中的值，那么线程1的`++`操作就失效了。

如果能保证原子性，那么应该是：线程2`读取i后加1并写回主存`这是一次性完成的，那么线程1还没进行`++`操作时，就会刷新工作内存中的值，再++就是2了。

### `volatile`中`lock`的位置

![image-20211214123602485](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211214123602485.png)