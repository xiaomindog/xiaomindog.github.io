---
title: 背包问题整理
date: 2020-09-18 11:25:32
tags: ["DP","背包问题"]
categories: 'leetcode'
---



<!--more-->



##  背包问题I

**描述：**在`n`个物品中挑选若干物品装入背包，最多能装多满？假设背包的大小为`m`，每个物品的大小为`A[i]`

```java
public class backpack_1 {
    public int backPack_1(int m, int[] A) {
        if (A.length == 0 || m == 0) return 0;
        int[][] dp = new int[A.length + 1][m + 1]; //表示 当前选择第i个物品背包容量为j的情况
        for (int i = 1; i <= A.length; i++) { //遍历物品
            for (int j = 1; j <= m; j++) {
                if (j >= A[i - 1]) { //2种情况： 当前物品选和不选
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - A[i - 1]] + A[i - 1]);
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[A.length][m];
    }

    //二维变一维
    public int backPack_2(int m, int[] A) {
        if (A.length == 0 || m == 0) return 0;
        int[] dp = new int[m + 1];

        for (int i = 1; i <= A.length; i++) {
            for (int j = m; j >= 1; j--) { //这边需要倒序 因为dp[i-1][j-A[i-1]]不会被提前更新
                if (j >= A[i - 1]) {
                    dp[j] = Math.max(dp[j], dp[j - A[i - 1]] + A[i - 1]);
                }
            }
        }
        return dp[m];
    }
}
```



##  背包问题II（01背包）

**描述：**有 `n` 个物品和一个大小为 `m` 的背包. 给定数组 `A` 表示每个物品的大小和数组 `V` 表示每个物品的价值.

问最多能装入背包的总价值是多大?（每个物品只能取一次）  

思路和上题一样，只需要修改一处即可。

```java
public class backpack_2 {
    public int backPackII_1(int m, int[] A, int[] V) {
        if (m == 0 || A.length == 0 || V.length == 0) return 0;
        int[][] dp = new int[A.length + 1][m + 1];
        for (int i = 1; i <= A.length; i++) {
            for (int j = 1; j <= m; j++) {
                if (j >= A[i - 1]) {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - A[i - 1]] + V[i - 1]);
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[A.length][m];
    }

    public int backPackII_2(int m, int[] A, int[] V) {
        if (m == 0 || A.length == 0 || V.length == 0) return 0;
        int[] dp = new int[m + 1];
        for (int i = 1; i <= A.length; i++) {
            for (int j = m; j >= 1; j--) {
                if (j >= A[i - 1]) {
                    dp[j] = Math.max(dp[j], dp[j - A[i - 1]] + V[i - 1]);
                }
            }
        }
        return dp[m];
    }
}
```
## 背包问题III（完全背包 求值）

**描述：**给定`n`种大小为`Ai`，值为`Vi`的物品(每个物品都有无限多的可用物品)和一个大小为m的背包，你能放入背包的最大值是多少?

```java
public class backpack_3 {
    //思路：视为特殊的多重背包问题 每个物品的个数的最大为m/A[i]
    public int backPackIII_1(int m, int[] A, int[] V) {
        if (m == 0 || A.length == 0 || V.length == 0) return 0;
        int[][] dp = new int[A.length + 1][m + 1];

        for (int i = 1; i <= A.length; i++) {
            for (int j = A[i - 1]; j <= m; j++) {
                dp[i][j] = dp[i - 1][j]; //第i个不选
                for (int k = 1; k * A[i - 1] <= m; k++) {
                    if (j >= k * A[i - 1]) { //当前第i个情况不选(k=0) 或者选k个
                        dp[i][j] = Math.max(dp[i][j],
                                dp[i - 1][j - k * A[i - 1]] + k * V[i - 1]);
                    }
                }
            }
        }
        return dp[A.length][m];
    }

    //多重背包问题解完全背包问题的DP优化
    public int backPackIII_2(int m, int[] A, int[] V) {
        if (m == 0 || A.length == 0 || V.length == 0) return 0;
        int[] dp = new int[m + 1];
        for (int i = 1; i <= A.length; i++) {
            for (int k = 1; k * A[i - 1] <= m; k++) {  //遍历当前该元素的个数
                for (int j = m; j >= A[i - 1]; j--) {
                    //当前元素 不取 或者取1个
                    dp[j] = Math.max(dp[j], dp[j - A[i - 1]] + V[i - 1]);
                }
            }
        }
        return dp[m];
    }


    //另一种思路 同leetcode518 零钱兑换II
    public int backPackIII_3(int m, int[] A, int[] V) {
        if (m == 0 || A.length == 0 || V.length == 0) return 0;
        int[] dp = new int[m + 1];
        for (int i = 1; i <= A.length; i++) {
            for (int j = A[i - 1]; j <= m; j++) {
                //对于当前物品i，若j从小到大的话，很可能在j之前的j-A[i-1]时已经放过第i件物品了，
                // 在j时再放就是重复放入；若j从大到小，则j之前的所有情况都没有更新过，
                // 不可能放过第i件物品，所以不会重复放入。 //与第二题相反
                dp[j] = Math.max(dp[j], dp[j - A[i - 1]] + V[i - 1]);
            }
        }
        return dp[m];
    }
}
```

##  背包问题IV（完全背包 求方案）

**描述：** 给出 n 个物品, 以及一个数组, `nums[i]`代表第i个物品的大小, 保证大小均为正数并且没有重复, 正整数 `target` 表示背包的大小, 找到能填满背包的方案数。`每一个物品可以使用无数次` 

[零钱兑换II](https://leetcode-cn.com/problems/coin-change-2/) 

```java
public class backpack_4 {
    //爬楼梯 零钱兑换II
    public static int backPackIV_1(int[] nums, int target) {
        if (target < 0) return 0;
        int[][] dp = new int[nums.length + 1][target + 1];

        for (int i = 0; i <= nums.length; i++) {
            dp[i][0] = 1; //target=0时,对于任意硬币来说 都可以不选  所以都为1
        }

        for (int i = 1; i <= nums.length; i++) {
            for (int j = 1; j <= target; j++) {
                dp[i][j] = dp[i - 1][j]; //当前元素不选
                if (j - nums[i - 1] >= 0) { //当前元素选
                    dp[i][j] += dp[i][j - nums[i - 1]];
                }
            }
        }
        return dp[nums.length][target];
    }


    public int backPackIV_2(int[] nums, int target) {
        if (target < 0) return 0;
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i <= nums.length; i++) { //加入第i个元素
            for (int j = nums[i - 1]; j <= target; j++) {  //当前元素可能的组合个数
                dp[j] += dp[j - nums[i - 1]];
            }
        }
        return dp[target];
    }
}
```

##  背包问题V

**描述：** 给出 `n` 个物品, 以及一个数组, `nums[i]` 代表第i个物品的大小, 保证大小均为正数, 正整数 `target` 表示背包的大小, 找到能填满背包的方案数。`每一个物品只能使用一次` 

```java
public class backpack_5 {
    public int backPackV_1(int[] nums, int target) {
        int[][] dp = new int[nums.length + 1][target + 1];
        for (int i = 0; i <= nums.length; i++) {
            dp[i][0] = 1;
        }
        for (int i = 1; i <= nums.length; i++) {
            for (int j = 1; j <= target; j++) {
                dp[i][j] = dp[i - 1][j]; //不选当前元素
                if (j >= nums[i - 1]) { //选当前元素
                    // 这边+的时i-1 表示 如果选当前元素 之前没选过
                    dp[i][j] += dp[i - 1][j - nums[i - 1]];
                }
            }
        }
        return dp[nums.length][target];
    }

    public int backPackV_2(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1; // 目标为0  当前元素也不选
        for (int i = 1; i <= nums.length; i++) {
            for (int j = target; j >= nums[i - 1]; j--) {
                dp[j] += dp[j - nums[i - 1]];
            }
        }
        return dp[target];
    }
}
```

##  多重背包问题

- 待更新