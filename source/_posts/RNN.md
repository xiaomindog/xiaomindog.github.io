---
title: RNN相关知识点
date: 2021-01-18 19:54:55
tags: [NLP,deeplearning]
mathjax: true
categories: [deeplearning]
---

<!--more-->

引入RNN：处理序列问题

最基本的单层网络结构为，输入x，经过Wx+b和激活函数得到输出y。RNN在这个基础上引入了隐藏层h，h可对序列数据提取特征，接着在转换为输出。

隐层计算为
$$
h_n = f(Ux_1+Wh_{n-1}+b)
$$
每个步骤**权值共享**，使用的参数U , W , b相同，激活函数为tanh。

RNN存在的问题：**梯度消失**。由于结构上的限制，很长的时刻以前的输入，对现在的网络影响非常小，后向传播时那些梯度，也很难影响很早以前的输入，即会出现梯度消失的问题。（**梯度被近距离梯度主导，导致模型难以学到远距离的依赖关系**）



###  LSTM

LSTM的结构：每个循环的模块内有4层结构: 3个sigmoid层，1个tanh层

遗忘门：决定丢弃信息
$$
f_t = \sigma(W_f\cdot[h_{t-1},x_t]+b_f)
$$
输入门：决定更新哪些值
$$
i_t=\sigma(W_i\cdot[h_{t-1},x_t]+b_i)
$$

$$
\widetilde{C}_t=tanh(W_C\cdot[h_{t-1},x_t]+b_C)
$$

$$
C_t=f_t*C_{t-1}+i_t*\widetilde{C}_t
$$

输出门：决定最终输出的值
$$
o_t = \sigma(W_o\cdot[h_{t-1},x_t]+b_o)
$$

$$
h_t = o_t*tanh(C_t)
$$



- LSTM如何解决梯度消失和爆炸问题

  [LSTM如何来避免梯度弥散和梯度爆炸？](https://www.zhihu.com/question/34878706/answer/665429718)

  梯度消失的本质：由于时间维度共享了参数矩阵，导致计算隐态h时会循环计算矩阵乘法，所以随时间反向传播算法求解梯度时出现了参数矩阵的累乘。

  LSTM解决问题的本质：引入gate机制，把矩阵乘法变成了逐位相乘，记忆状态的更新公式为ct。

###  GRU

更新门+输出门
$$
z_t=\sigma(W_z\cdot[h_{t-1},x_t])
$$

$$
r_t=\sigma(W_r\cdot[h_{t-1},x_t])
$$

$$
\widetilde{h}_t = tanh(S\cdot[r_t*h_{t-1},x_t])
$$

$$
h_t = (1-z_t)*h_{t-1}+z_t*\widetilde{h}_t
$$

###  Reference

[理解LSTM](https://www.jianshu.com/p/9dc9f41f0b29/)

[Understanding LSTM Networks](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)