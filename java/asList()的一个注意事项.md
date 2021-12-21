# asList()的一个注意事项

我之前写过这样的代码用于使用数组快速初始化`List`

```java
String[] str = new String[]{"a","b","c"};
List<String> list_3 = new ArrayList<>(Arrays.asList(str));
```

今晚我又用这个，只不过把`String`数组改成`int`型了，结果就出错了 

```java
int[] nums = new int[]{1,2,3};
List<Integer> l1 = new LinkedList<>(Arrays.asList(1,2,3));
List<Integer> l2 = new LinkedList<>(Arrays.asList(nums));
```



![image-20211128211408911](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211128211408911.png)

查也没有查到什么。

我又仔细思考：`String`确实和`int`是有区别的，String是个类，而`int`不是，`Integer`才是一个类。

我又点进`asList`的源码

```java
* @param <T> the class of the objects in the array
    * @param a the array by which the list will be backed
    * @return a list view of the specified array
    */
    @SafeVarargs
    @SuppressWarnings("varargs")
    public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```

开始我只看到T，我认为T一般指类吧。后来我又看到上面的对参数的注释

> @param <T> the **class** of the objects in the array

注意是`class`，所以我把`int`改成了`Integer`，然后就成功运行了。

```java
Integer[] nums = new Integer[]{1,2,3};
List<Integer> l2 = new LinkedList<>(Arrays.asList(nums));
l2.forEach(System.out::println);
```

>