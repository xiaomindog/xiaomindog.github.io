---
title: 背包问题整理
date: 2020-09-18 11:25:32
tags: ["DP","背包问题"]
categories: 'leetcode'
---



整理了一下几种背包问题。

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
                    dp[i][j] = Math.max(dp[i - 1][j], 
                                        dp[i - 1][j - A[i - 1]] + A[i - 1]);
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
                    dp[i][j] = Math.max(dp[i - 1][j], 
                                        dp[i - 1][j - A[i - 1]] + V[i - 1]);
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

**描述：**给定`n`种大小为`A[i]`，值为`V[i]`的物品(每个物品都有无限多的可用物品)和一个大小为`m`的背包，你能放入背包的最大值是多少?

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



##  背包问题VI（组合问题）

**描述：** 给定一个由正整数组成且不存在重复数字的数组，找出和为给定目标正整数的组合的个数。 

`顺序不同的序列被视作不同的组合。`

**注意与问题IV的区别：**组合考虑元素之间的顺序

[组合总和IV](https://leetcode-cn.com/problems/combination-sum-iv/)

```java
public class combinationSum4_377 {
    //把该问题看成完全背包问题 并且考虑顺序 所以target循环放在外部
    //转移方程：dp[i] = dp[i]+dp[i-num]  i为目标数
    /**
     * 状态转移方程：dp[i]= dp[i - nums[0]] + dp[i - nums[1]] + dp[i - nums[2]] + ... （当 [] 		里面的数 >= 0）
     * 特别注意：dp[0] = 1，表示，如果那个硬币的面值刚刚好等于需要凑出的价值，这个就成为 1 种组合方案
     * 再举一个具体的例子：nums=[1, 3, 4], target=7;
     * dp[7] = dp[6] + dp[4] + dp[3]
     * 即：7 的组合数可以由三部分组成，1 和 dp[6]，3 和 dp[4], 4 和dp[3];
     */
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int num : nums) {
                if (i >= num) {
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
    }
}
```



##  多重背包问题

**描述：**给定`n`种大小为 `A[i]`，值为 `V[i] `的物品，每个物品有 `amount[i]` 个可用，一个大小为 `m` 的背包，你能放入背包的最大值是多少?

```java
public class backpack_7 {
    public static int backpackVII_1(int[] A, int[] V, int[] amount, int m) {
        int[][] dp = new int[A.length + 1][m + 1];
        for (int i = 1; i <= A.length; i++) {
            for (int j = 1; j <= m; j++) {
                //dp[i][j] = dp[i - 1][j];
                for (int k = 0; k <= amount[i - 1]; k++) {
                    //k=0 表示当前元素不选
                    if (j >= k * A[i - 1]) {
                        dp[i][j] = Math.max(dp[i][j], 
                                            dp[i - 1][j - k * A[i - 1]] + k * V[i - 1]);
                    }
                }
            }
        }
        return dp[A.length][m];
    }

    public static int backpackVII_2(int[] A, int[] V, int[] amount, int m) {
        int[] dp = new int[m + 1];
        for (int i = 1; i <= A.length; i++) {
            for (int j = m; j >= A[i - 1]; j--) {
                for (int k = 1; k <= amount[i - 1]; k++) {
                    if (j >= k * A[i - 1]) {
                        dp[j] = Math.max(dp[j], dp[j - k * A[i - 1]] + k * V[i - 1]);
                    }
                }
            }
        }

        return dp[m];
    }
}
```

##  多重背包问题变种

**描述：**给出一些不同价值和数量的硬币。 找出这些硬币可以组合在1〜n范围内的多少个值

```java
public class backpack_8 {
    //这种解法会超时
    public static int backpackVIII_1(int n, int[] V, int[] amount) {
        boolean[] dp = new boolean[n + 1];
        //状态转移：dp[j] = dp[j]||dp[i-V[i]]
        dp[0] = true;
        for (int i = 1; i <= V.length; i++) {
            for (int k = 1; k <= amount[i - 1]; k++) {
                for (int j = n; j >= V[i - 1]; j--) {
                    //dp[j] = dp[j] || dp[j - V[i - 1]];
                    if (!dp[j] && dp[j - V[i - 1]]) { //剪枝
                        dp[j] = true;
                    }
                }
            }
        }
        int ans = 0;
        for (int i = 1; i <= dp.length - 1; i++) {
            System.out.println(dp[i]);
            if (dp[i]) ans++;
        }
        return ans;
    }

    //优化 比较难想到
    public static int backpackVIII_2(int n, int[] V, int[] amount) {
        boolean[] dp = new boolean[n + 1];
        dp[0] = true;
        for (int i = 1; i <= V.length; i++) {
            int[] count = new int[n + 1];
            //count数组的含义  相同价值硬币的使用次数
            //count[x]即当前i的时候，多少个硬币i已经用了来和其他硬币凑出x的价值。
            for (int j = V[i - 1]; j <= n; j++) {
                //为什么正序遍历？
                /* 3种剪枝情况
                1. dp[j]已经为true 无须继续更新
                2. 加入当前[value]为true，需要保证[j-value]也为true
                3. 硬币使用数量受限
                * */
                if (!dp[j] && dp[j - V[i - 1]] && count[j - V[i - 1]] < amount[i - 1]) {
                    dp[j] = true;
                    count[j] = count[j - V[i - 1]] + 1;
                }
            }
        }
        int ans = 0;
        for (int i = 1; i <= dp.length - 1; i++) {
            System.out.println(dp[i]);
            if (dp[i]) ans++;
        }
        return ans;
    }
}
```

## 背包问题IX（01背包问题）

**描述：**你总共有`n` 万元，希望申请国外的大学，要申请的话需要交一定的申请费用，给出每个大学的申请费用以及你得到这个大学offer的成功概率，大学的数量是 `m`。如果经济条件允许，你可以申请多所大学。找到获得至少一份工作的最高可能性。`0<=n<=10000,0<=m<=10000`

**样例：**

```txt
样例 1:
	输入:  
		n = 10
		prices = [4,4,5]
		probability = [0.1,0.2,0.3]
	输出:  0.440
	解释：
	选择第2和第3个学校。

样例 2:
	输入: 
		n = 10
		prices = [4,5,6]
		probability = [0.1,0.2,0.3]
	输出:  0.370
	解释:
	选择第1和第3个学校。
```

```java
public class backpack_9 {
    /**
     * 代码思路
     * 1. 定义一个二维数组dp[i]来表示花费i万元申请学校一所都没录取的概率
     * 2. 初始化dp数组，先假设录取概率都为0
     * 3. 操作probability数组，变成未录取的概率 probability[i] = 1.0 - probability[i]
     * 4. 第一层循环枚举i表示申请前i个学校
     * 5. 第二层循环枚举j表示花费j万元
     * 6. 根据算法思路中的状态转移方程来实现 dp[j] = min(dp[j],dp[j-prices[i]] * probability[i])
     * 7. dp[n]表示花费 n 万元一所学校都未录取的最小概率
     */
    //转换成01背包来想： 有n个学校，每个学校都不录取的值为Vi，求一所学校都没录取的最小值
    public static double backpackIX_1(int n, int[] prices, double[] probability) {
        double[][] dp = new double[prices.length + 1][n + 1];
        for (int i = 0; i <= prices.length; i++) {
            for (int j = 0; j <= n; j++) {
                dp[i][j] = 1.0;  //初始化
            }
        }
        for (int i = 0; i < probability.length; i++) {
            probability[i] = 1.0 - probability[i];
        }
        for (int i = 1; i <= prices.length; i++) {
            for (int j = 1; j <= n; j++) {
                if (j >= prices[i - 1]) { //选择当前学校
                    dp[i][j] = Math.min(dp[i - 1][j],
                            dp[i - 1][j - prices[i - 1]] * probability[i - 1]);
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return 1 - dp[prices.length][n];
    }

    public static double backpackIX_2(int n, int[] prices, double[] probability) {
        double[] dp = new double[n + 1];
        for (int i = 0; i <= n; i++) {
            dp[i] = 1.0;
        }
        for (int i = 0; i < probability.length; i++) {
            probability[i] = 1.0 - probability[i];
        }
        for (int i = 1; i <= prices.length; i++) {
            for (int j = n; j >= prices[i - 1]; j--) {
                dp[j] = Math.min(dp[j], dp[j - prices[i - 1]] * probability[i - 1]);
            }
        }
        return 1 - dp[n];
    }


    public static void main(String[] args) {
        int n = 6595;
        int[] prices = new int[]{178, 4933, 9772, 4890, 1732, 1690, 3793};
        double[] weight = new double[]{0.1, 0.8, 0.8, 0.1, 0.2, 0.2, 0.8};
        System.out.println(backpackIX_1(n, prices, weight));
    }
}
```

