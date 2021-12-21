# 认识JVM

## JVM位置

![image-20211221132518859](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211221132518859.png)

![image-20211221132557063](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211221132557063.png)

## 前端编译器 vs 后端编译器

前端编译器：.java -> .class

后端编译器：字节码 -> 机器码 JIT compiler 反复执行的热点代码编译成机器码缓存起来（二次编译，第一次是前端编译）。

### 问题

一般是对字节码逐行解析执行，但有些代码需要反复执行因此需要JIT compiler

如果只用JIT不用翻译字节码行不行？

不行，因为JIT很慢，如果所有代码都使用JIT，那么程序刚运行时会很久都看不到结果。响应时间方面会很差。翻译字节码和JIT共同使用，使得**程序响应时间**和**最佳执行性能**得到平衡。

![image-20211221124916335](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211221124916335.png)



## 栈的指令架构

由于跨平台性的设计，java的指令都是根据栈来设计的。不同平台CPU架构不同，所以不能设计为基于寄存器的。**优点**是**跨平台，指令集小，编译容易实现**；**缺点**是**性能下降，实现同样的指令需要更多的指令。**

## JVM生命周期

### 虚拟机的启动

Java虚拟机的启动是通过引导类加载器(bootstrap class loader)创建一个初始类(initial class)来完成

的，这个类是由虚拟机的具体实现指定的。

### 虚拟机的执行

- 一个运行中的Java虚拟机有着一个清晰的任务:执行Java程序。
- 程序开始执行时他才运行，程序结束时他就停止。
- 执行一个所谓的Java程序的时候，真真正正在执行的是一个叫做**Java虚拟机**的进程。

### 虚拟机的退出

有如下的几种情况:

- 程序正常执行结束
- 程序在执行过程中遇到了异常或错误而异常终止·由于操作系统出现错误而导致`Java`虚拟机进程终止
- 某线程调用`Runtime`类或`system`类的`exit`方法，或 `Runtime`类的`halt`方法，并且`Java`安全管理器也允许这次`exit`或`halt`操作。
- 除此之外，`JNI` ( `Java Native Interface`)规范描述了用`JNI Invocation API`来加载或卸载Java虚拟机时，Java虚拟机的退出情况。

## JVM发展

Classic VM

Exact VM

JRockit VM（三大商用虚拟机之一）

HotSpot VM（三大商用虚拟机之一）

名称中的HotSpot指的就是它的**热点代码探测技术**。通过计数器找到最具编译价值代码，触发即时编译或栈上替换.

- 通过编译器与解释器协同工作，在最优化的程序响应时间与最佳执行性能中取得平衡方法，并且Java安全管理器也允许这次exit或halt操作。
- 除此之外，JNI ( Java Native Interface)规范描述了用JNIInvocation API来加载或卸载Java虚拟机时，Java虚拟机的退出情况。

J9 VM（三大商用虚拟机之一）

