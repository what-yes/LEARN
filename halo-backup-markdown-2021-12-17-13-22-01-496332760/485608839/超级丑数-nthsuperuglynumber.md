---
title: 超级丑数
date: 2021-11-22 19:23:59.319
updated: 2021-11-22 20:41:18.084
url: https://what-yes.top/archives/nthsuperuglynumber
categories: 算法
tags: 动态规划 
---

## [超级丑数](https://leetcode-cn.com/problems/super-ugly-number/)

![image-20211122184436797](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211122184436797.png)

### 思路

`dp[i]`表示第`i+1`个丑数。dp[i]肯定是由dp[j] (其中j<i)乘上primes中某个值得到的，一个很暴力的思路就是

```java
for(int j=0;i<i;j++){
    for(int k=0;k<primes.length;k++)
        // 求 dp[j]*primes[k] 最小值
}
```

当然这肯定是过不了的。

所以需要换个思路：

dp中每个数肯定只会乘primes中每个数一次，假如primes中某个数乘过dp中某个数，那么primes中那个数下次应该乘dp中后一个数。

所以每次我们只需比较 primes乘上dp中对应的数。比如示例1中，我们已知dp数组前三个数[1,2,4]，4是乘2得来的，那么下一个乘2的数一定是dp[2]也就是4，乘7，13，19的当然还是dp[0]也就是1，下一次比较只需要比较 2\*dp[2], 7\*dp[0], 13\*dp[0], 19\*dp[0]。

因此需要一个pointer数组存放primes数组对应乘的数的位置，每当找到使用primes中哪个数进行相乘得到最小结果时，需要把对应pointer++；比如例子中应是乘7 （1*7），那么就把pointer[1]++(7的下标是1)

```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] dp = new int[n];
        int l = primes.length;
        int[] pointers = new int[l];

        Arrays.fill(pointers, 0);
        Arrays.fill(nums, 1);
        dp[0] = 1;
        for(int i=1;i<n;i++){
            int min = Integer.MAX_VALUE;
            int index = -1;
            for(int j=0;j<l;j++){
                int tmp = dp[pointers[j]]*primes[j];
                if(tmp < min && tmp > dp[i-1]){
                    min = tmp;
                    index = j;
                }
            }
            dp[i] = min;
            pointers[index]++;
        }
        for(int i=0;i<n;i++)
            System.out.println(dp[i]);
        return dp[n-1];
    }
}
```

但是还忽略了一个问题，如果出现相等的情况，那么只会将某个pointer进行++，没有进行++的pointer在以后的判断中就不会再满足if条件了，就会被忽略掉。

比如这个primes数组 ：[3,5,7,11,19,23,29,41,43,47]

一定会出现3\*5 和 5\*3的情况，先3\*5后，那么5对应的pointer就不会++，因此后续也不会有5\*5=25，5\*7=35...,就会有问题。

![image-20211122191941250](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211122191941250.png)

所以应该是先循环求出最小值，然后再进行同样的循环，将等于最小值时的pointer都++。

```java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] dp = new int[n];
        int l = primes.length;
        int[] pointers = new int[l];

        Arrays.fill(pointers, 0);
        dp[0] = 1;
        for(int i=1;i<n;i++){
            int min = Integer.MAX_VALUE;
            // 求出最小值
            for(int j=0;j<l;j++){
                int tmp = dp[pointers[j]]*primes[j];
                if(tmp < min && tmp > dp[i-1]){
                    min = tmp;
                }
            }
            // 将等于最小值的pointer都++
            for(int j=0;j<l;j++){
                if(dp[pointers[j]]*primes[j] == min)
                    pointers[j]++;
            }
            dp[i] = min;
        }
        return dp[n-1];
    }
}
```

