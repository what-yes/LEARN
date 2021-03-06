---
title: 双指针-----删除有序数组重复项
date: 2021-12-02 20:58:56.643
updated: 2021-12-04 15:17:59.343
url: https://what-yes.top/archives/shuang-zhi-zhen-------shan-chu-you-xu-shu-zu-zhong-fu-xiang
categories: 
tags: 算法 | 双指针
---

# 双指针-----删除有序数组重复项

[删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

[删除有序数组中的重复项 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array-ii/)

我开始做的是第二题，但做得挺暴力的

![image-20211202192915990](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211202192915990.png)

就去看了看题解，瞄了一眼看到个**双指针**，而且建议先去写第一题，所以我就去写了第一题，并用双指针的方法。

## [删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

![image-20211202193030352](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211202193030352.png)

思路：考虑到题目要求的特殊的返回值（int型），我们只需要让不重复的值把那些重复的值覆盖掉，然后记录下一共有多少个重复的值，最后**返回总长度-重复值个数**。回到双指针，双指针嘛，肯定是一快一慢。快慢指针指向的值有两种情况：

1. `nums[slow] == nums[fast]`

   直接让fast加一就好了，其余不用管。

   ![image-20211202194306850](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211202194306850.png)

2. `nums[slow] != nums[fast]`

   又分为两种情况：

   1. `i`和`j`相邻，就是说相邻两个数不相等，那直接让**快、慢指针都加一**就好了。

      ![image-20211202194329681](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211202194329681.png)

   2. `i`和`j`不相邻，这个时候我们需要用`nums[j]`覆盖掉第一个重复的`nums[i]`

      ![image-20211202194319343](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211202194319343.png)

完整代码

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int slow = 0, fast = 1, minus = 0;
        while(fast < nums.length){
            if(nums[slow] == nums[fast]){
                fast++;
                minus++;
            }else if(slow!=fast+1){
                nums[slow+1] = nums[fast];
                slow++;
                fast++;
            }else if(slow == fast+1){
                slow++;
                fast++;
            }
        }
        return nums.length - minus;
    }
}
```

其实代码可以简化一些，就是在`nums[slow] != nums[fast]`的时候，无论slow 和fast是否相邻，都执行

```java
nums[slow+1] = nums[fast];
slow++;
fast++;
```

因为相邻时：`fast=slow+1`,   `nums[slow+1] = nums[fast]` 其实就是自己给自己赋值。

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int slow = 0, fast = 1, minus = 0;
        while(fast < nums.length){
            if(nums[slow] == nums[fast]){
                fast++;
                minus++;
            }else{
                nums[slow+1] = nums[fast];
                slow++;
                fast++;
            }
        }
        return nums.length - minus;
    }
}
```

## [删除有序数组中的重复项 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array-ii/)

第二题的双指针可太离谱了，想了好久没想出来，看题解也不是太明白。

第二题的双指针中，slow所表示的下标指的是最后返回的个数，暗含着当`nums[fast]!=nums[slow-2]`时，nums[fast]就应该被保留下来，然后`slow++, fast++`。

当`nums[fast]==nums[slow-2]`时，则表明`nums[fast]`不该被保留，直接跳过即可: `fast++`

代码少得离谱：

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int slow = 2, fast = 2;
        while(fast < nums.length){
            if(nums[fast] != nums[slow - 2]){
                nums[slow] = nums[fast];
                ++slow;
            }
            ++fast;
        }
        return slow;
    }
}
```

菜得扣脚啊:cry: