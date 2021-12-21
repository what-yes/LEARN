---
title: 背包问题
date: 2021-11-21 22:46:13.573
updated: 2021-11-23 14:51:21.905
url: https://what-yes.top/archives/package
categories: 算法
tags: 动态规划  | 背包问题
---

## 背包问题

### 0-1背包

```
最简单的背包问题就是0-1背包问题。属于动态规划中的一种，动态规划问题要考虑状态和选择。
先说状态：相同数量的物品但重量不同，相同重量的物品但数量（种类）不同，背包的容量（能承受的重量是一种状态），物品的数量是一种状态。
再说选择：对于遍历中每个物体，我们可以将它加入背包，也可以不加入，这就是选择。
```

#### 思路伪代码：

```java
for 状态1 in 状态1的所有取值：
    for 状态1 in 状态1的所有取值：
        dp[状态1][状态2] = 择优（选择1， 选择2）
```

#### dp数组定义

```java
dp[i][w]定义：对于前i个物品，当背包容量为w时，这种情况下能装下的最大价值为dp[i][w].
```

#### 选择代码：

```java
// 第i个物品不能装下
if (W - weights[i - 1] < 0) {
// dp[i-1][j] : 不装第i个物品的背包的价值。既然装不下（就是没有装），那么就和能装的个数减一且重量相同时价值一样
    dp[i][j] = dp[i-1][j]; 
} else {
// value[i-1]+dp[i-1][j-value[i-1]] : 第i个物品能装下，并且把它装进去后的背包的价值。
// value[i-1]：第i个物品的价值. dp[i-1][j-value[i-1]]：除去第i个物品的质量，剩下质量所能装的最大价值
// dp[i-1][j] : 不装第i个物品的背包的价值。既然没有装，那么就和能装的个数减一且重量相同时价值一样
	dp[i][j] = Math.max(value[i-1]+dp[i-1][j-value[i-1]],dp[i-1][j]);
}
```

#### 完整代码

```java
public class package01 {
    /**
     * 
     * @param W       背包能装的总重量
     * @param N       背包能装的总个数 也即提供物品的数量
     * @param weights 物品的重量信息
     * @param value   物品的价值信息
     * @return
     */
    public int solution(int W, int N, int[] weights, int[] value) {
        int m = weights.length, n = value.length;
        // dp[i][j]表示 对于前i个物品，当背包承受重量为j时的最大价值
        int[][] dp = new int[m + 1][n + 1];

        //先确定能装的个数，再依次增大能装的质量来进行遍历
        for (int i = 1; i <= N; i++) {
            for (int j = 1; j <= W; j++) {
                // 第i个物品不能装下
                if (W - weights[i - 1] < 0) {
                    dp[i][j] = dp[i-1][j]; 
                } else {
                    dp[i][j] = Math.max(value[i - 1] + dp[i - 1][j - value[i - 1]], dp[i - 1][j]);
                }
            }
        }
        return dp[N][W];
    }

}

```

## 其它背包问题

很多问题都是背包问题的引申。比如有一个目标（类似目标和），有一些达到目标的方法（ 类似一些加数），问是否能达到目标或者达到目标有多少种方法。这一类问题都可以看作是背包问题。

### [零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)

![image-20211121215346396](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211121215346396.png)

#### dp数组定义

`dp[i][j] 对于前i种硬币，凑出金额j，有dp[i][j]种方法。`

#### base case

```java
dp[0][...] = 0;
dp[...][0] = 1;一个金币都不拿，也是一种方法。
```

#### 状态转移

```java
// 可以装入背包的时候
// dp[i-1][j] 是不装入背包，那么就是数量为i-1时的方法数
//dp[i][j-coins[i-1]] 是装入背包，dp[i][j-coins[i-1]]是除去目前装入的这个硬币 剩余的容量的方法数
dp[i][j] = dp[i-1][j] + dp[i][j-coins[i-1]];

//不能装入背包的时候
dp[i][j] = dp[i-1][j];
```

#### 完整代码

```java
    public int coinChange_(int amount, int[] coins){
        int n = coins.length;
        int[][] dp = new int[n+1][amount+1];
        for(int i=0;i<=n;i++)
            dp[i][0] = 1;

        for(int i=1;i<=n;i++){
            for(int j=1;j<=amount;j++){
                if(j - coins[i] >= 0)
                    dp[i][j] = dp[i-1][j] + dp[i-1][j-coins[i]];
                else
                    dp[i][j] = dp[i-1][j];
            }
        }
        return dp[n][amount];
    }
```

#### 状态压缩

从上面的代码可以看出来，`dp[i][j]都是通过上一行dp[i-1][...]转换过来的`

```java
public int coinChange(int amount, int[] coins) {
    int n = coins.length;
    int[] dp = new int[amount + 1];
    dp[0] = 1;

    for (int i = 0; i < n; i++) {
        for (int j = 1; j <= amount ; j++) {
            if (j >= coins[i])
                dp[j] = dp[j] + dp[j - coins[i]];
        }
    }
    return dp[amount];
}
```

### 平分数组

#### 二维dp

```java
public class canPartition {
    public static boolean Solution(int[] nums){
        int sum = 0;
        for(int n : nums)
            sum += n;
        if(sum % 2 != 0)
            return false;
        int W = sum/2, N = nums.length;


        boolean[][] dp = new boolean[N+1][W+1];
        // 无论多少个元素，只要都是0就可以被平均划分
        for(int i=0;i<=N;i++){
            dp[i][0] = true;
        }
        // 将背包能装质量从小到大遍历， 看重量能否恰好装满（装满是数组和的一半）
        for(int i=1;i<=N;i++){
            for(int j=1;j<=W;j++){
                if(j - nums[i] < 0){
                    dp[i][j] = dp[i-1][j];
                }else{
                    dp[i][j] = dp[i-1][j] || dp[i-1][j-nums[i-1]];
                }
            }
        }
        // 每一行都只与前一行有关，可以采用一维数组进行状态压缩
        return dp[N][W];
    }    


}
```

#### 一维dp

比如 `i=0`, 会把`dp[nums[0]]`的值值为true
`i=1`时，会把`dp[nums[1]], dp[nums[0]+nums[1]]`的值置为true.

##### 为什么反向遍历？

dp数组后面的值是根据前面的值得出的（所以j应该是反向遍历） 或者说从二维`dp`数组解法中可以知道，
每一次`dp[i][j]`是根据`dp[i-1][xx](xx <= j)`求出来的，所以要保证用一维数组求解时，使用的数组
还是上一次遍历所得到的，如果正向遍历，那么可能用到的就是这一次遍历的.

而且对于这个问题而言，物品（数组的数）只能使用一次,

| 1    | 2    | 3     | 4     | 5     | 6     | 7     | 8     |
| ---- | ---- | ----- | ----- | ----- | ----- | ----- | ----- |
| true | true | false | false | false | false | false | false |
| true | true | false | true  | false | true  | false | false |

所以不会出现在一次外层循环nums[i]= 2中，使得dp[4],dp[6]因为2都变为true这种事情发生
所以说，对于数组中的数只能**使用一次**时，内层循环需要倒叙遍历
否则顺序遍历就行了

```java
public class canPartition2 {

    /*
     * 数组能否被平分
     */

    public boolean Solution(int[] nums) {
        int sum = 0;
        for (int n : nums)
            sum += n;
        if (sum % 2 != 0)
            return false;
        int W = sum / 2, N = nums.length;
        boolean[] dp = new boolean[W + 1];
        dp[0] = true;

        for (int i = 0; i < N; i++) {
            for (int j = W; j >= 0; j--)
                if (j - nums[i] >= 0)
                    dp[j] = dp[j] || dp[j - nums[i]];
        }

        return dp[W];
    }

}
```









