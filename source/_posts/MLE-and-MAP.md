---
title: 最大似然估计与最大后验估计（贝叶斯估计）
date: 2021-04-01 22:19:34
tags: [ML]
categories: [ML]
---

 最大似然估计(Maximum Likelihood Estimation)与最大后验估计(Maximum A Posteriori)是机器学习中最常用的两种**参数估计方法**(统计领域问题： 已知数据，推模型和参数 )。 最大似然估计可以认为是**频率学派**的观点，最大后验概率估计可以认为是**贝叶斯学派**的观点。 



###  最大似然估计

- 最大似然估计提供了一种给定观察数据来评估模型参数的方法，即：“模型已定，参数未知”。
- 最大似然估计假设数据都是**独立同分布**的。
- 最大化P(x0|θ)

###  最大后验估计

- 最大化 *P*(θ∣x0)=P(x0∣θ)P(θ) / P(x0) 贝叶斯公式  P(θ|x0) 即后验概率

###  二者区别

- 最大似然估计，是通过给定观测值，需要获得参数，使得多组观测值出现的概率最大化。最大后验估计，在给定观测值的同时，给出参数的先验分布，通过先验和似然并且利用贝叶斯公式得最大后验。

- **MAP 考虑了模型的先验分布，而MLE 假设模型是均匀分布，即先验概率P(θ)=1。** 可以说，MLE是MAP的一种特例 





###  参考

[详解最大似然估计（MLE）、最大后验概率估计（MAP），以及贝叶斯公式的理解](https://blog.csdn.net/u011508640/article/details/72815981?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&dist_request_id=&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control)