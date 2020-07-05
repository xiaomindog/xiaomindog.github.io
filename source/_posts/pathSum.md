---
title: pathSum
date: 2020-07-05 17:10:09
tags:[leetcode,递归，二叉树]
categories:'leetcode'
---

关于leetcode中路径总和的几道题目的整理。

<!--more-->

##  路径总和I

- [leetcode112]( https://leetcode-cn.com/problems/path-sum/ )

- 题目描述： 给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。 

- 解题思路：递归，sum值每次递归减去其根结点的值

- java代码：

  ```java
  class Solution {
      public boolean hasPathSum(TreeNode root, int sum) {
          if (root == null)
              return false;
          sum -= root.val; 
          if (root.left == null && root.right == null) {
              return sum == 0;
          }
          return hasPathSum(root.left, sum) || hasPathSum(root.right, sum);
      }
  }
  ```

  

##  路径总和II

- [leetcode113，剑指offer34]( https://leetcode-cn.com/problems/path-sum-ii/ )

- 题目描述： 输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径。 

- 解题思路：递归，利用一个数组保存遍历的路径，如果遇到根结点并且满足条件则输出。

- java代码：

  ```java
  class Solution {
      public List<List<Integer>> pathSum(TreeNode root, int sum) {
          List<List<Integer>> ans = new ArrayList<>();
          List<Integer> arry = new ArrayList<>();
          dfs(root, sum, arry, ans);
          return ans;
      }
  
      public void dfs(TreeNode root, int sum, List<Integer> arry, List<List<Integer>> ans) {
          if (root == null) return;
          arry.add(root.val);
          sum -= root.val;
          if (root.left == null && root.right == null && sum == 0) {
              //ans.add(arry); //整个递归过程用的是同一个arry，需要把当前的状态保存下来，否则最后的输出全为空[]
              ans.add(new ArrayList<>(arry));
          }
          dfs(root.left, sum, arry, ans);
          dfs(root.right, sum, arry, ans);
          arry.remove(arry.size() - 1);//回溯，去除当前的根结点，继续寻找下一个满足条件的路径
      }
  }
  ```

  

##  路径总和III

- [leetcode437]( https://leetcode-cn.com/problems/path-sum-iii/ )

- 题目描述：给定一个二叉树，它的每个结点都存放着一个整数值。找出路径和等于给定数值的路径总数。

  路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。二叉树不超过1000个节点，且节点数值范围是 [-1000000,1000000] 的整数。

- 解题思路：

  - 思路1：递归，从每一个节点出发，找到所有满足条件的路径，两层递归。
  - 思路2：考虑到每个节点到根节点的路径是唯一的。用一维数组保存当前节点到根节点的所有路径，用一个标记表示当前路径的终点。

- java代码：

  ```java
  class Solution {
      //思路1 双重递归
      //先序遍历每一个结点，在以每一个节点作为起始节点递归查找
      private int num = 0;
      public int pathSum(TreeNode root, int sum) {
          if (root == null) return 0;
          cal_sum(root, sum);
          pathSum(root.left, sum);
          pathSum(root.right, sum);
          return num;
      }
      //计算当前结点作为根结点的所有满足的路径
      public void cal_sum(TreeNode root, int sum) {
          if (root == null) return;
          sum -= root.val;
          if (sum == 0) {
              num++;
          }
          cal_sum(root.left, sum);
          cal_sum(root.right, sum);
      }
  }
  ```

  ```java
  class Solution {
     //思路2
      /**核心：每个节点到根节点的路径是唯一的
      * 1.用一维数组保存当前节点到根节点的所有路径
      * 2.用一个标记表示当前路径的终点
      * 3.先序遍历所有节点
      */
      public int pathSum(TreeNode root, int sum) {
          return cal_sum(root, sum, new int[1000], 0);
      }
      //arry数组用于保存遍历过的路径
      public int cal_sum(TreeNode root, int sum, int arry[], int p) {
          if (root == null) return 0;
          int n = (root.val == sum ? 1 : 0); //判断单独的节点是否满足条件
          int cur = root.val; 
          for (int i = p - 1; i >= 0; i--) { //根结点出发到当前结点的所有可能值
              cur += arry[i];
              if (cur == sum) {
                  n++;
              }
          }
          arry[p] = root.val; //访问过的结点加入数组
          int n1 = cal_sum_2(root.left, sum, arry, p + 1);
          int n2 = cal_sum_2(root.right, sum, arry, p + 1);
          return n + n1 + n2;
      }
  }
  ```

  

  