---
title: 删除排序链表中的重复元素
date: 2021-12-04 10:25:19.753
updated: 2021-12-04 10:25:19.753
url: https://what-yes.top/archives/shan-chu-pai-xu-lian-biao-zhong-de-zhong-fu-yuan-su
categories: 
tags: 算法 | 链表
---

# [删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

其实前几天我想写的是这道题，但当时第一次搜出来的是数组那个题，所以就写数组了。

这道题我第之前一次写真的是没有头绪，写得很复杂还写错了。前几天突然就想写链表了，然后又想写写这道题。

![image-20211204101041142](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204101041142.png)

## 思路

这道题还是和以前说的一样，直接考虑中间节点这种普通情况。对于一个节点，它的前一个节点（不要问为什么是前面而不是后面，问就是后面节点的情况还没有考虑）只有两种情况，要么值相同，要么值不同。

1. 值相同，则让前一个节点的`next`指向当前的节点的`next`。
2. 值不同，则直接判断下一个就行了，即`cur=cur.next`。而且下一次判断中，前一个节点就是当前节点，因此前一个节点的值也需要改变。

上面的思路提到了前一个节点，因此我们需要记录下前一个节点为`pre`，并在每次判断时改变改变`pre`或`pre.next`。

## 特殊情况：

注意链表的头节点可能为空

## 完整代码：

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head==null || head.next == null)
            return head;
        ListNode pre = head;
        ListNode cur = head.next;
        while(cur!=null){
            if(pre.val != cur.val){
                pre.next = cur.next;
            }else{
                pre = cur;
            }
            cur = cur.next;
        }

        return head;
    }
}
```

