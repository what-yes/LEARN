---
title: 两两交换链表中的节点
date: 2021-12-01 21:58:59.574
updated: 2021-12-02 10:30:18.798
url: https://what-yes.top/archives/liang-liang-jiao-huan-lian-biao-zhong-de-jie-dian
categories: 
tags: 算法 | 链表
---

# [两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

![image-20211201204551508](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211201204551508.png)

趁热打铁再写一题。

还是老思想，对于两个要交换的节点（记两个为一组）`cur` `cur.next`

1. 首先让`cur`指向下一组的第一个

2. 那么`cur.next`就找不着了，所以需要用一个`tmp`记录

   ```java
   ListNode tmp = cur.next;
   cur.next = tmp.next;
   tmp.next = cur;
   ```

3. 然后让`cur`变成指向下一组的第一个节点

   ```java
   pre = cur;
   cur = cur.next;
   ```

## 错误代码

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode cur = head;
        while(cur!=null && cur.next!=null){
            ListNode tmp = cur.next;
            cur.next = tmp.next;
            tmp.next = cur;
            cur = cur.next;
        }
        return head;
    }
}
```

乍一看这样就好了

## 错误原因

但是，这是上面运行的结果（假设现在处理的一组是`3 4`）

![image-20211201212951478](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211201212951478.png)

## 解决办法

所以，在处理每一组时，需要让上一组留下来的`pre`指向这一组改完后的第一个；

```java
pre.next = tmp;
```

还需要把处理完后的第二个节点记作`pre`，供下一组使用。

```java
pre = cur;
```

## 完整演示

![image-20211201214901837](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211201214901837.png)

## 特殊节点

这题就还需要考虑**头节点**的特殊情况：

```java
// 当是第一组时
if(pre == null){
    head = tmp;
}
//否则
else{
    pre.next = tmp;
}
```

## 完整代码：

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode cur = head, pre = null;
        while(cur!=null && cur.next!=null){
            ListNode tmp = cur.next;
            cur.next = tmp.next;
            tmp.next = cur;

            if(pre == null){
                head = tmp;
            }
            else{
                pre.next = tmp;
            }

            pre = cur;
            cur = cur.next;
        }
        return head;
    }
}
```

