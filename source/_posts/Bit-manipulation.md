---
title: leetcode中位运算的整理
date: 2020-10-15 12:22:16
tags: [leetcode,位运算]
categories: [leetcode]
---

位运算中常常有一些很骚的思路，本文整理了算法题中常见的位运算的细节。

<!--more-->



##  1.不用中间变量交换2个数的值

a = a^b

b = a^b

a = a^b

解释：**b = a^a^b, a = a^b^b**

同样原理适用于题目：找出数组中不重复的元素，其他元素均出现2次



##  2.计算数字的二进制中有多少个1

法1：常规解法：循环右移。，每次判断最后1位是不是1   **(n&1)==1 **

法2：**n&(n-1)** 每次去掉二进制从前往后的最后1个1

##  3.每次改变一个数中二进制的一位

**n^(1<<i)** ：表示改变n的第i位

##  4. 找出数组中消失的2个数字

