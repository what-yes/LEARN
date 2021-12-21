---
title: 滑动窗口问题
date: 2021-12-05 16:06:31.23
updated: 2021-12-05 16:06:31.23
url: https://what-yes.top/archives/hua-dong-chuang-kou-wen-ti
categories: 
tags: java | 双指针
---

# 滑动窗口问题

这其实是个双指针问题。

## 相关题目：[最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

![image-20211205150824595](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211205150824595.png)

## 思路：

1. 在S字符串中使用双指针的左右指针，初始化`left = right = 0`，将`[left, right]`包含的值看作是一个窗口。
2. 不断增加`right`指针直到窗口中的字符串符合要求（包含t中所有字符串）
3. 此时，停止增加`right`，转而不断增加`left`指针缩小窗口，直到窗口不再符合要求（不包含t中所有字符串），每次增加`left`时，注意修改最后结果。
4. 重复2，3；直到`right`达到`S`的尽头。

本题中需要两个`Map`来记录需要**凑齐的字符**以及**窗口中已经有的字符**。

## 代码

```java
class Solution {
    public static String minWindow(String s, String t) {
        Map<Character, Integer> window = new HashMap<>();
        Map<Character, Integer> need = new HashMap<>();

        int left = 0, right = 0;
        int start = 0, end = Integer.MAX_VALUE;
        int valid = 0; // valid 不是按t的长度算的，而是按照t中不重复的字符数量算的

        // 初始化need
        for(char c : t.toCharArray()){
            if(need.containsKey(c))
                need.put(c, need.get(c) + 1);
            else
                need.put(c,1);
        }

        while(right < s.length()){
            char c = s.charAt(right);

            // 更新窗口里的值，没有则给1 有就加1
            window.put(c, window.containsKey(c) ? window.get(c) + 1 : 1);
            // 如果窗口中包含c的数量和所需的数量相同,有一个need的判断是因为如果need中没有的话，get就会报错
            if(need.containsKey(c) && window.get(c) == need.get(c)){
                valid++;
            }
            right++;
			
            // 如果窗口满足要求
            while(valid == need.size()){
                // 如果当前的长度 小于记录中的长度则更新
                if(right - left < end - start){
                    start = left;
                    end = right;
                }
                char d = s.charAt(left);
                //如果在包含d的时候，窗口中d刚好满足条件，那么删后就不满足了，valid就会减1（先减1再删除）
                if(need.containsKey(d) && need.get(d) == window.get(d))
                    valid --;
                
                window.put(d, window.get(d)-1);
                left++;
            }
        }

        return (end == Integer.MAX_VALUE) ? "" : s.substring(start, end);
        
    }
        
}
```

## emmm...

其实上面代码提交过不了

![image-20211205155929860](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211205155929860.png)

我也不知道是什么原因....这个输入反正就挺离谱

## 后话

感觉`map`的更新操作太不优雅了

`window.put(c, window.containsKey(c) ? window.get(c) + 1 : 1);`

有没有优雅一点的办法呢？