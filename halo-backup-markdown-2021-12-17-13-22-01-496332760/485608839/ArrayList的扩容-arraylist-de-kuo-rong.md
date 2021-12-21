---
title: ArrayList的扩容
date: 2021-12-03 12:37:34.113
updated: 2021-12-03 12:37:34.113
url: https://what-yes.top/archives/arraylist-de-kuo-rong
categories: 
tags: ArrayList | 源码
---

# ArrayList的扩容

```java
public void ensureCapacity(int minCapacity) {
    if (minCapacity > elementData.length
        && !(elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
             && minCapacity <= DEFAULT_CAPACITY)) {
        modCount++;
        grow(minCapacity);
    }
}
```

如果：

1. 当前所需容器长度大于原来容器长度
2. 且不是空参构造的初始化情况

那么会调用grow函数，即扩大1.5倍。但是也不只是直接扩容这么简单。

```java
private Object[] grow(int minCapacity) {
    return elementData = Arrays.copyOf(elementData,
                                       newCapacity(minCapacity));
}

private Object[] grow() {
    return grow(size + 1);
}

private int newCapacity(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity <= 0) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return minCapacity;
    }
    return (newCapacity - MAX_ARRAY_SIZE <= 0)
        ? newCapacity
        : hugeCapacity(minCapacity);
}
```

> `MAX_ARRAY_SIZE = Interger.MAX_VALUE - 8`

```java
private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE)
        ? Integer.MAX_VALUE
        : MAX_ARRAY_SIZE;
}
```

## 扩容后的判断

应该是每次进行1.5倍扩容，如果扩容后：

1. `if (newCapacity - minCapacity <= 0)`  

   1. `if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)`，好像是初始化的情况，那么`(DEFAULT_CAPACITY, minCapacity)`取个最大值就行。

   2. 但 `if (minCapacity < 0)`说明`minCapacity`溢出了（或者说刚好溢出了，因为`minCapacity`是加一加一慢慢得到的），那么就会报`OutOfMemoryError()`错误。

      ```java
      // newCapacity
      if (newCapacity - minCapacity <= 0) {
          if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
              return Math.max(DEFAULT_CAPACITY, minCapacity);
          if (minCapacity < 0) // overflow
              throw new OutOfMemoryError();
          return minCapacity;
      }
      ```

      

2. 否则，将其与`MAX_ARRAY_SIZE`进行比较，

   1. 如果小于`MAX_ARRAY_SIZE`，那么返回`minCapacity`

   2. 如果大于MAX_ARRAY_SIZE，会进入`private static int hugeCapacity(int minCapacity)`再次进行判断

      ```java
      return (newCapacity - MAX_ARRAY_SIZE <= 0)
          ? newCapacity
          : hugeCapacity(minCapacity);
      ```

      1. 首先还是判断溢出

      2. 其次如果大于`MAX_ARRAY_SIZE`，则直接让minCapacity等于`Integer.MAX_VALUE`。如果小于`MAX_ARRAY_SIZE`，则`minCapacity=MAX_ARRAY_SIZE`

```java
private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE)
        ? Integer.MAX_VALUE
        : MAX_ARRAY_SIZE;
}
```

我发现一件很奇怪的事：有些判断会重复进行。

比如在`minCapacity > MAX_ARRAY_SIZE` 在 `newCapacity` 和 `hugeCapacity` 都出现了，而调用关系是`newCapacity`调用`hugeCapacity`。

## 后话：

目前对于读不懂源码的一个原因可能是：源码中所描述的出来的有些情况我并不能理解什么时候会发生，所以不是很理解有些源码的功能。

 