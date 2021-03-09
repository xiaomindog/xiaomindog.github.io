---
title: 机器学习基础知识整理
date: 2021-03-08 20:47:19
tags: ['ML']
categories: ['ML','实习']
---

本文整理面试中可能出现的一些机器学习相关问题，以备记忆。

<!--more-->

###  BN

**1. BN使得网络中每层输入数据的分布相对稳定，加速模型学习速度**

​	BN通过规范化与线性变换使得每一层网络的输入数据的均值与方差都在一定范围内，使得后一层网络不必不断去适应底层网络中输入的变化，从而实现了网络中层与层之间的解耦，允许每一层进行独立学习，有利于提高整个神经网络的学习速度。

**2. BN使得模型对网络中的参数不那么敏感，简化调参过程，使得网络学习更加稳定**

**3. BN允许网络使用饱和性激活函数（例如sigmoid，tanh等），缓解梯度消失问题**

**4. BN具有一定的正则化效果**

​	在Batch Normalization中，由于我们使用mini-batch的均值与方差作为对整体训练样本均值与方差的估计，尽管每一个batch中的数据都是从总体样本中抽样得到，但不同mini-batch的均值与方差会有所不同，这就为网络的学习过程中增加了随机噪音，与Dropout通过关闭神经元给网络训练带来噪音类似，在一定程度上对模型起到了正则化的效果。

BN 比较适用的场景是：每个 mini-batch 比较大，数据分布比较接近。在进行训练之前，要做好充分的 shuffle. 否则效果会差很多。

**不适用于 动态的网络结构 和 RNN 网络。**

[Batch Normalization原理与实战](https://zhuanlan.zhihu.com/p/34879333)

[Batch Normalization（BN，批量归一化）](https://zhuanlan.zhihu.com/p/55852062)

###  LN

针对 BN 的上述不足而提出的。它综合考虑一层所有维度的输入，计算该层的平均输入值和输入方差，然后用同一个规范化操作来转换各个维度的输入。

LN 针对单个训练样本进行，不依赖于其他数据，因此可以避免 BN 中受 mini-batch 数据分布影响的问题，可以用于 小mini-batch场景、动态网络场景和 RNN，特别是自然语言处理领域。此外，LN 不需要保存 mini-batch 的均值和方差，节省了额外的存储空间。

[详解深度学习中的Normalization，BN/LN/WN](https://zhuanlan.zhihu.com/p/33173246)

### L1/L2正则化

- L1正则化可以产生稀疏权值矩阵，即产生一个稀疏模型，可以用于特征选择
- L2正则化可以防止模型过拟合（overfitting）；一定程度上，L1也可以防止过拟合

- L1正则化是指权值向量w w*w*中各个元素的**绝对值之和**，通常表示为||W1||
- L2正则化是指权值向量w中各个元素的**平方和然后再求平方根**

L1和L2的目的是通过减少W的权重**(权重衰减)**从而减少模型的复杂度，从而提高模型的泛华能力。

正则化系数：通常越大的λ可以让代价函数在参数为0时取到最小值。

[机器学习中正则化项L1和L2的直观理解](https://blog.csdn.net/jinping_shi/article/details/52433975)

###  偏差 方差

- 欠拟合 偏差大方差小
- 过拟合 偏差小 方差大

如何解决？ 选择正确的模型，合适的模型复杂度，慎重选择数据集的大小

###  如何理解反向传播 解释action机制

###  激活函数的意义

引入激活函数是为了**增加神经网络模型的非线性**。没有激活函数的每层都相当于矩阵相乘。即使叠加了若干层之后，无非还是个矩阵相乘。

性质：

非线性，可微性，单调性，f(x)约等于x，输出值的范围

- 非饱和性：饱和指的是在某些区间梯度接近于零（即梯度消失），使得参数无法继续更新的问题

RELU: 计算快(f(x)=max(0,x))

sigmoid,tanh

结合3者图像理解：

relu不容易梯度消失，relu的梯度大多数情况下是常数，有助于解决深层网络的收敛问题。

在LSTM中使用Relu作为激活函数：

​	RNN中共享参数W，很容易出现很大的输出值。

​	Relu不能解决梯度长距离的传递问题。

###   梯度消失和梯度爆炸

梯度消失：导数小于1，随着网络成熟变多，梯度更新信息指数衰减

梯度爆炸：导数大于1，随着网络成熟变多，梯度更新信息指数增加

解决方案：

1. 梯度裁剪
2. relu激活函数
3. BN
4. 残差结构等

###  Dropout的原理

神经网络在训练时，每层的迭代过程中， 随机选择中的一些神经元并将其临时隐藏(丢弃)，然后再进行本次训练和优化。 下一次迭代重复操作，以至训练结束。**由于是随机丢弃，每一个mini-batch都在训练不同的网络。**

 在训练时，每个神经单元以概率p被保留(Dropout丢弃率为1−p)；在测试阶段，每个神经单元都是存在的，权重参数w要乘以p，输出是：pw。 





###  神经网络相关的

#### LSTM的参数如何计算

4个参数计算 具体见公式

sum = 4*((dim+hidden)+hidden)

GRU应该是LSTM参数的3/4

####  有哪些优化器

- SGD 最基础的 
- 动量法
- 自适应学习率法 （减少梯度下降中的摆动）
- Adam = 动量法+自适应学习率
- AdamW 约等于 Adam+L2正则化 



###  机器学习

####  LR

简述：假设变量服从伯努利分布，以p概率取1，1-p概率取0，这样的模型就成为逻辑斯蒂回归模型

就可以用极大似然估计来学习模型参数

最大化对数似然

####  SVM 

- 函数间隔和几何间隔

  函数间隔受参数w影响，几何间隔除以L2范数，是确定的

- 对偶问题

  对偶问题更容易求解，把目标函数和约束融为拉格朗日函数，通过这个函数来寻找最优解。

  可以自然的引出核函数，进而推广到非线性分类问题

- 引入核函数

  原本的样本空间线性不可分，通过核函数把样本映射到一个线性可分的空间去，这样以后，求解对偶问题只需知道核函数，求解难度下降。

- 核函数之间的区别

  线性核：适用于线性可分，参数少，训练快

  高斯核：适用于线性不可分，参数多，分类结果依赖于参数的好坏