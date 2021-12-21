---
title: String StringBuffer StringBuilder的区别
date: 2021-11-29 22:16:42.741
updated: 2021-11-29 22:21:55.615
url: https://what-yes.top/archives/stringstringbufferstringbuilder-de-qu-bie
categories: 
tags: java | String 
---

# String StringBuffer StringBuilder的区别

## String

### 声明

```java
@Stable
private final byte[] value;
```

可以看出`String`存的对象都不能被改变，事实也确实是这样，调用`substring`, `concat`等会改变字符串的操作其实最后都会重新`new`一个对象出来，而不是对原来的`String`对象进行直接修改。
### substring concat源码
```java
public String substring(int beginIndex) {
    if (beginIndex < 0) {
        throw new StringIndexOutOfBoundsException(beginIndex);
    }
    int subLen = length() - beginIndex;
    if (subLen < 0) {
        throw new StringIndexOutOfBoundsException(subLen);
    }
    if (beginIndex == 0) {
        return this;
    }
    return isLatin1() ? StringLatin1.newString(value, beginIndex, subLen)
        : StringUTF16.newString(value, beginIndex, subLen);
}
```

```java
public String concat(String str) {
    if (str.isEmpty()) {
        return this;
    }
    if (coder() == str.coder()) {
        byte[] val = this.value;
        byte[] oval = str.value;
        int len = val.length + oval.length;
        byte[] buf = Arrays.copyOf(val, len);
        System.arraycopy(oval, 0, buf, val.length, oval.length);
        return new String(buf, coder);
    }
    int len = length();
    int olen = str.length();
    byte[] buf = StringUTF16.newBytesFor(len + olen);
    getBytes(buf, 0, UTF16);
    str.getBytes(buf, len, UTF16);
    return new String(buf, UTF16);
}
```

说明对`String`的修改是很麻烦的。如果是 `s+="a"`这条语句，通过反编译可以看到，每次调用时都会创建一个`StringBuffer`，然后调用其中的`append`方法：

（这个使用`javap -c`生成的，我的一点<a href="#puzzle">疑惑</a>在最后）

![image-20211129214644811](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211129214644811.png)

## StringBuffer

所以说，如果字符串有变化的需求，那么应该用`StringBuffer`。下面是一个关于时间的测试代码以及截图：

```java
public class teststring {
    public static void main(String[] args) {
        testString();

        testStringBuffer();
    }

    public static void testString(){
        Long start = System.currentTimeMillis();
        String s = "";
        for(int i=0;i<1000;i++){
            s+="a";
        }
        Long end = System.currentTimeMillis();
        System.out.print("String :");
        System.out.println(end-start);
    }

    public static void testStringBuffer(){
        Long start = System.currentTimeMillis();
        StringBuffer s = new StringBuffer();
        for(int i=0;i<1000;i++){
            s.append("a");
        }
        Long end = System.currentTimeMillis();
        System.out.print("StringBuffer :");
        System.out.println(end-start);
    }
}
```

![image-20211129215144233](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211129215144233.png)

那么有`StringBuffer`，为什么还有`StringBuilder`呢？

## StringBuilder

写到这我凌乱了，因为思绪不太对，介绍顺序我也弄不清楚了。简单来说，用`StringBuilder`是因为它的效率很高，特别高，那么有人好奇，刚刚不是说`StringBuffer`效率高嘛？确实，但`StringBuilder`效率**更**高。

现在问题变了，有`StringBuilder`，为什么还有`StringBuffer`呢？

因为`StringBuffer`是线程安全的

这是源码中关于`StringBuffer`的介绍：

> String buffers are safe for use by **multiple threads**.

相关源码如下：

```java
@Override
public synchronized int length() {
    return count;
}

@Override
public synchronized int capacity() {
    return value.length;
}


@Override
public synchronized void ensureCapacity(int minimumCapacity) {
    super.ensureCapacity(minimumCapacity);
}
//.....
```

`synchronized` 典型跟线程安全相关的标志嘛

## 总结

| String               | StringBuffer           | StringBuilder            |
| -------------------- | ---------------------- | ------------------------ |
| 字符串**不变时**使用 | **多线程多变化时**使用 | **单线程巨多变化**时使用 |

## <a name="puzzle"></a>疑惑

我开始反编译的时候，在`vscode`上操作的，发现我反编译的结果和看视频的`up`主用`IDEA`反编译的还不一样，是这样子：

![image-20211129220643520](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211129220643520.png)

（没有`StringBuilder`啥啥啥的）

后来我去`IDEA`发现还没有那个`java-p`的工具![image-20211129220749129](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211129220749129.png)

然后在[这个地方](https://blog.csdn.net/weixin_30409927/article/details/102951048)学到了怎么配置。

后来我想了一下，应该是两个地方的`jdk`不一样，因为`vscode`是`jdk11`，`IDEA`是`jdk8`。
> 本文链接： https://what-yes.top/archives/stringstringbufferstringbuilder-de-qu-bie#puzzle