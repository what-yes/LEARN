---
title: 对称树
date: 2021-11-08 14:01:58.311
updated: 2021-11-08 14:01:58.311
url: https://what-yes.top/archives/sysmmetric-tree
categories: 算法
tags: 算法 | 树
---

[leetcode链接](https://leetcode-cn.com/problems/symmetric-tree/submissions/)
![image.png](/upload/2021/11/image-9b22b42d9b814f7ab223123d02561192.png)
#### 思路1 递归
通过check函数，每次判断两个对称的结点是否同时为空或值是否相等。
```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return check(root.left, root.right);
    }
    public boolean check(TreeNode p, TreeNode q){
        if(p == null && q == null)
            return true;
        else  if(p == null || q == null)
            return false;
        else
            return p.val == q.val && check(p.left,q.right) && check(p.right, q.left);
    }
}
```
#### 思路2 
用一个队列将对称的结点存进去，即每次`offer`的时候，都是`offer`两个对称的点。
```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        Queue<TreeNode> tree = new LinkedList<>();
        if(root == null || (root.left==null && root.right==null))
            return true;
        if(root.left==null ^ root.right==null)
            return false;
        tree.offer(root.left);
        tree.offer(root.right);
        while(!tree.isEmpty()){
            TreeNode p = tree.poll();
            TreeNode q = tree.poll();
            if(p == null && q == null)
                continue;
            if((p == null ^ q == null) || p.val!=q.val)
                return false;
            tree.offer(p.left);
            tree.offer(q.right);

            tree.offer(p.right);
            tree.offer(q.left);
        }
        return true;
    }
}
```


