---
title: 奇淫巧计
date: 2021-11-22 20:52:58.948
updated: 2021-11-22 20:52:58.948
url: https://what-yes.top/archives/java-skills
categories: 
tags: java
---

## 奇淫巧计

### 求数组最小/大值

```java
public static void main(String[] args) {
    int[] nums = new int[]{4,2,1,3,5};
    int min = Arrays.stream(nums).min().getAsInt();
    System.out.println(min);
}
```

### 构造List

如果List有初始值，以前只知道用`put`的方法。最近发现可以用`Arrays.asList`

```java
List<Integer> list_1 = new ArrayList<>(Arrays.asList(1,2,3));

List<Integer> list_2 = new ArrayList<>(Arrays.asList("a","b","c"));

String[] str = new String[]{"a","b","c"};
List<String> list_3 = new ArrayList<>(Arrays.asList(str));
```

### 输出List

```java
public class test_2 {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(Arrays.asList(1,2,3));
        list.forEach(l->System.out.println(l));
        //或者 list.forEach(System.out::println);
    }
}
```

其中`l->System.out.println(l)`是`lamada`表达式。甚至于可以

```java
list.forEach((e) -> {
            e = e * 10;
            System.out.print(e + " ");
        });
```

### 合并两个List

```
addAll()
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
/**
 * 合并两个ArrayList
 * 同时也是比较简洁的输出List
 */
public class mergeList {
    public static void main(String[] args) {
        List<Integer> l1 = new ArrayList<>(Arrays.asList(1,2,3));
        List<Integer> l2 = new ArrayList<>(Arrays.asList(4,5,6));
        l1.addAll(l2);
        l1.forEach(System.out::println);
    }
}
```

### 抑或  ^

当抑或两边的判断结果不相同时整个判断成立。

比如用来得出两个结点是否存在某一个为空，如果存在则进入if。

```java
if(a==null ^ b==null)
    System.out.println("一个为空一个不为空");
```

