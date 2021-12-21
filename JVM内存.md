# JVM内存

## 硬件内存模型

将JVM内存模型之前首先介绍一下硬件内存模型，因为JVM内存模型是根据硬件内存模型设计而来的（我是这么认为的）。

CPU-寄存器-缓存-主存机制，为了解决访问速度不匹配的问题。对于单核cpu而言，运行起来没有问题，但是对于多核cpu而言，可能会出现数据不同步的问题。

比如两个CPU都对D进行修改，是以D1为准还是以D2为准。

![image-20211209194419840](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209194419840.png)

因此，提出了缓存一致性协议。这里不展开讲。

那么如何解决上面说的那个问题？

1. CPU1操作数据D的时候，CPU2也准备操作D，但发现D正在被别的CPU进行操作，于是等待别的CPU操作完，将数据写回主存后，再对D进行操作。看似可行，但这样会对CPU2的性能造成很大的损失。于是又有下面的一种方法，将同步改为异步。
2. CPU1操作数据D的时候，CPU2也准备操作D，但发现D正在被别的CPU进行操作，于是CPU2注册一个读取D的消息，然后CPU2去做别的事情，其它CPU写回数据D后，响应了这个注册信息；CPU2处理完手头工作发现 消息被相应后，再去读取D。但是CPU2是运行完后面的指令再去运行前面的指令，这样就会有指令的重排序。

硬件内存模型是为了让汇编语言能够运行在具有一致性的内存视图上。

但是随着高级语言的产生，需要让高级语言也能运行在具有一致性的内存视图上，因此在硬件内存模型上，还存在着为编程语言设计的内存模型。比如Java内存模型。

## Java内存模型

![image-20211209200453284](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209200453284.png)

本地内存中的存储是**私有变量**和**共享变量的副本**。

比如主内存的堆中有一个new出来的对象（new 出来的对象都是放在堆里的），线程A中有这个对象的引用，线程B中也有这个对象的引用。（对于变量存放位置还不太清楚，今晚可能看看书了解一下）

![image-20211209200754452](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209200754452.png)

### 理想的通信

![image-20211209201509796](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209201509796.png)

### 可能存在的问题

![image-20211209201544424](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209201544424.png)

左图：

线程A读取x并对其进行修改为2，写入主存。但线程B运行时，为了速度会直接从本地内存中读取x，因为它看不见x在主存中的值已经改变了。

右图：

线程A和线程B都读取了x=1，并对其进行加1操作，理论结果应该是3，但最后是2.

## 并发三要素

### 可见性

当一个线程修改共享变量的值，起他线程需要能立刻的值这个修改。

#### 两种解读：

1. ##### 第一种解读

   线程A修改了数据D，线程B需要读到修改后最新的D。（由刷新主存的时机引起的）

```java
// 左图代码  运行结果是是会一直卡着出不去 但我看弹幕说 jdk16好像可以出去。
static int a = 1;
public static void main(String[] args) throws InterruptedException {
    Thread t1 = new Thread(new Runnable() {
        @Override
        public void run(){
            while(a!=2){
                // do nothing
            }
        }
    });
    Thread t2 = new Thread(new Runnable() {
        @Override
        public void run(){
            a = 2;
        }
    });
    t1.start();
    Thread.sleep(1000);
    t2.start();
}
```

​	线程2对变量的修改并没有被线程1读取到。

​	解决办法：

1. volataile

   被volataile修饰的变量总是会主动写入主存，若要读取volataile修饰的变量，那么总是从主存中读取。这样相当于操作volataile变量都是直接去读写主存。

2. synchronized

   在同步代码块中monitor的基础上，读写变量时，将会隐式地执行内存lock指令，并清空工作内存中改变量地值，需要使用该变量时，必须从主存中读取。同理，也会隐式地执行内存中unlock指令，将修改过的变量刷新回主存。

   ```java
   Thread t1 = new Thread(new Runnable() {
       @Override
       public void run(){
           while(a!=2){
               synchronized(this){
                   int b = a + 2;
               }
           }
       }
   });
   ```

2. ##### 第二种解读

   线程B需要读到被修改的变量D，线程A应该修改，但是因为重排序导致线程A没有及时修改变量D。(由指令重排引起的)

![image-20211209204203841](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209204203841.png)

JVM内存模型中也可能出现指令重排的情况，导致尽管执行到4，但并没有执行过1.

也可以通过volataile和synchronized来解决。

1. volataile

   volataile关键字禁止当前变量与之前的代码语句进行重新排序。

   当程序执行到vollatile变量的读写时(还未执行)，之前的代码语句的执行结果是满足可见性的。

   当执行volatille变量的读写时上文讲过变量将会与主存进行同步。

   只需给flag加上volataile，因为代码2中的变量是被vollatile修饰的，根据上一段讲的就能够保证代码的可见性

2. synchronized

   我们可以知道产生错误的原因是：线程2读取到了线程1的中间状态。我们只需将代码1和代码2编程一个不可分割的代码块，那么无论其内部如何重排，线程2都只能读到其最终结果，因此都不会影响到线程2的执行。

   ```java
   synchronized(this){
       a = 1;
       flag = true;
   }
   ```

#### Happens-Before原则

内存模型中定义了 Happens-Before原则，所以平常写代码没有遇见那么多可见性问题。

![image-20211209205601453](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211209205601453.png)

原子性

有序性

