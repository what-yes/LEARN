---
title: 反转链表
date: 2021-12-01 20:26:25.655
updated: 2021-12-01 20:26:25.655
url: https://what-yes.top/archives/fan-zhuan-lian-biao
categories: 
tags: 算法 | 链表
---

# [反转链表](https://leetcode-cn.com/problems/UHnkqh/)

最近看面经什么的总是看到有人被考这道题，我还以为是多难一道题呢！打开一看发现原来自己写过。当然，不能大意。

我感觉写链表题目吧，就像写递归一样，对于每个节点的操作一样，只要想好对于中间某个节点的情况，再考虑首尾有没有特殊情况就可以了。

![image-20211201201521094](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211201201521094.png)

我的思路：假设现在有两个点已经反转了，有人可能会说，我都不会反转，那它俩怎么反转过来的？因为反转对于每个节点操作都一样，从中间开始考虑比较容易思考。下面是思路，并不是代码的顺序

1. 我们需要让当前没有反转的第一个节点`cur`的`next`指向已经反转链表的头节点`before`（因为`before`节点在原链表中本来就在`cur`的`before`位置，我给它命名成`before`不过分吧）。
2. 那当前没有反转的第一个节点的next也就是后面的节点都找不着了嘛，所以需要用一个`tmp`来记录。
3. 我们已经对于当前节点及以前的节点进行了反转，下一次我们还要进行相同操作。所以需要改变`cur`和`before`指向，`before`指向已经反转的头节点，`cur`指向没有反转的头节点

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode cur = head, before = null;
        while(cur!=null){
            ListNode tmp = cur.next;
            cur.next = before;
            before = cur;
            cur = tmp;
        }

        return before;
    }
}
```

