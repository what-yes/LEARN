---
title: 锁机制-2
date: 2021-12-09 22:01:45.63
updated: 2021-12-10 13:54:07.668
url: https://what-yes.top/archives/suo-ji-zhi--2
categories: 
tags: 源码 | 锁
---

# 锁机制-2

互斥锁的同步方式是悲观的

## 悲观锁

操作系统悲观地认为如果不严格同步线程调用，那么一定产生异常；所以互斥锁将会将资源锁定，只供一个线程使用，而阻塞其它线程。

但如果大部分操作是读资源或者同步代码块的耗时远远小于线程切换的耗时，那么没有必要每次都锁定资源。

因此产生了`CAS`  `compare and swap`

## 乐观锁

很多硬件底层都实现了CAS，因此也产生了通过CAS实现同步的工具

通过CAS实现同步的工具，由于不会修改资源，而且当需要修改资源时，总是**乐观**认为对象值没有被其它线程修改过，而是自己去compare。

每次线程操作变量时，线程自身会携带 `old value`值和`new value`值，然后将`old value`和变量的值进行比较，如果相同则可以对变量进行操作，将变量改为`new value`；如果不同，则会进入自旋。

![image-20211209213515691](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209213515691.png)

可能有人有疑问：如果A在可以操作变量但还没有修改时，B也进行compare发现相同也准备修改变量怎么办？底层将`CAS` 设计成了原子指令就解决了这个问题。

本质上来说，乐观锁并没有给变量上锁，大家都这么叫，那就这样吧。

## Java中运用CAS

```java
// 三个线程加一个数到1000，不允许重复
static AtomicInteger num = new AtomicInteger(0);

public static void main(String[] args) {
    for(int i=0;i<3;i++){
        Thread t = new Thread(new Runnable() {
            @Override
            public void run(){
                while(num.get() < 1000){
                    System.out.println("thread name " + Thread.currentThread().getName() + ":  " + num.incrementAndGet());
                }
            }
        });
        t.start();
    }
}
```

`AtomicInteger` 这个类底层使用 CAS实现的。

```java
// `AtomicInteger`类中变量之一
private static final jdk.internal.misc.Unsafe U = jdk.internal.misc.Unsafe.getUnsafe();

// 底层代码中的CAS操作 while循环就是自旋操作，如果compare不相同不会一直自旋，默认是10次
@HotSpotIntrinsicCandidate
public final int getAndAddInt(Object o, long offset, int delta) {
    int v;
    do {
        v = getIntVolatile(o, offset);
    } while (!weakCompareAndSetInt(o, offset, v, v + delta));
    return v;
}
```

`unsafe`这个类中关于`CAS`的实现，它的`compareAndSwapInt`方法是`native`方法，`native`方法说明方法实现已经跟`java`无关了（我也只知道这个：它是通过调用别的语言的代码来实现这个函数），在这个函数中，实现的指令会与CPU架构有关，因为CAS指令会根据不同CPU而不同。

![image-20211209215006989](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209215006989.png)



> 参考视频： [【Java并发】面试官问我CAS、乐观锁、悲观锁，我反手就是骑脸输出_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1ff4y1q7we?spm_id_from=333.999.0.0)

