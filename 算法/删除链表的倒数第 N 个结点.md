# [删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

## 题目：

![image-20211204223237319](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211204223237319.png)

## 思路：

这种链表中**倒数第k个值**的题目，我认为**双指针**的方法是比较容易理解和实现的。

1. 首先还是定义一个`fast`，一个`slow`。
2. 让`fast`先走`k`步，
3. 再让`slow`和`fast`一起走，
4. 当`fast`所指为`null`的时候，`slow`所指的值就是倒数第k个了

这题只不过是要删掉倒数第k个，这种删除的题目一般都是要记录下前一个指针(记作`pre`)，所以当`slow`开始走的时候，总是得记录下它的前一个，当`slow`找到位置后，`pre.next = pre.next.next` 就行了

当然，指针题目还得考虑特殊的地方：

1. 第一个节点
2. 最后一个节点
3. 只有一个节点（其实这只是上面两种情况中的特殊情况）
4. 空节点（整个为空）

这一题先排除第四种，因为题目中 `1<=sz<=30`

那么需要考虑前三种：

如果删除的是第一个（也就是倒数第`n`个），那么`pre`肯定是为`null`的，我们只需要对`pre`是否为空进行一个判断就行了。如果为空，说明是第一个节点，那返回 `head.next就行了`

```java
// 删第一个 那pre就是null
if(pre == null)
    return head.next;
```

如果删除的是最后一个，那么`slow.next`（也就是`pre.next.next`）为空，我们只需要对`pre.next.next`是否为空进行一个判断就行了，如果为空....（等会，写到这我发现好像不用这一步的判断，`pre.next.next`此时为空，当删除最后一个值时，我本来就是要进行 `pre.next=null`,那这和`pre.next` = `pre.next.next`不就是一回事 ）。

## 完善：

原来代码：

```java
// 删第一个 那pre就是null
if(pre == null)
    return head.next;

//删最后一个 那del就是null  就没有pre.next.next (pre.next是del)
if(pre.next.next == null){
    pre.next = null;
    return head;
}

pre.next = pre.next.next;
return head;
```

发现问题后改的：

```java
// 删第一个 那pre就是null
if(pre == null)
    return head.next;

pre.next = pre.next.next;
return head;
```

## 完整代码：

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        int step = 0;
        ListNode cur = head, del = head, pre = null;;
        while(step!=n){
            cur = cur.next;
            step++;
        }
        while(cur!=null){
            pre = del;
            del = del.next;
            cur = cur.next;
        }
        // 删第一个 那pre就是null
        if(pre == null)
            return head.next;
        
        pre.next = pre.next.next;
        return head;
    }
}
```

