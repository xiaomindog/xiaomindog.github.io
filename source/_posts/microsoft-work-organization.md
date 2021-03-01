---
title: 微软实习内容简单梳理
date: 2021-03-01 16:17:55
tags: ["deeplearning","实习"]
categories: ["工作"]
---

本文简单梳理一下微软工作中遗留的一些知识点。

<!--more-->

###  数据增强

- 简单的EDA数据增强（FGM对抗训练耗时，在embedding层添加扰动）

  - 词语替换
    - 基于词典的 （标准EDA）
    - 基于词向量的（利用word2vec训练近义词）
    - 基于TF-IDF的 （替换TF-IDF值较低的词）
    - 基于mask LM的替换
  - 随机噪音注入
    - 随机插入
    - 随机交换
    - 随机删除
  - 回译

- 半监督学习
  - UDA无监督数据增强（**个人理解的本质：在原有损失上添加针对未标注数据的正则项，使模型能够利用这些数据进行隐式迭代，最终增强泛化性能**）

    基于少量的有标签样本(3k)+大量的无标签样本(9w)

    增强手段：回译 tf-idf替换

    损失：

    ​	标记数据：交叉熵损失 （TSA）

    ​	未标记数据：**通过未标记样本和增强后的样本之间的KL散度来计算loss**（带温度的softmax）

    训练技巧：**TSA，训练信号退火，逐步去除带标签的数据避免过拟合。** 采用大量的未标记数据进行训练，所需的模型会偏大，而大模型又会轻松的在有限的有监督数据上过拟合，这时TSA就要逐步的释放有监督数据的训练信号了。作者对每个training step 都设了一个阈值ηt，且小于等于1，当一个标签例子的正确类别P的概率高于阈值ηt时，模型从损失函数中删除这个例子，只训练这个minibatch下其他标记的例子。 

    

  

###  知识蒸馏

知识蒸馏就是把大模型学习到的作为知识，蒸馏到小模型上，让小模型也具有接近大模型的精度并且提升速度。

bert作为Teacher模型，textcnn作为student模型，

损失计算部分考虑：计算textcnn与bert模型的输出的MSE-Loss，

计算textcnn与真实标签的CE-Loss

几个问题：

**为什么用mse-loss而不用softmax-T？**因为softmax-T需要2个超参数（T和损失权重，调参难度大，不易训练，并且在实验中，mse-loss的结果更优，至于为啥用mse-loss，我理解的是mse-loss等价于计算两者之间的一个余弦相似度。）

**做过哪些对比试验？**

大模型用Roberta，AIbert，效果并不理想。小模型考虑了TextRNN，但是推理速度太慢。

数据增强：基于随机[mask]替换和n-gram采样的替换

###  ALbert Roberta 

####  Albert的改进

- embedding因式分解，O(V\*H)-->O(V\*E+E*H)
- 每个transformer blocks参数共享（参数不会随着模型层数加深而增大）

- SOP代替NSP（预测2个句子的顺序关系）

- 去掉了dropout（作者认为bert一系列模型是欠拟合的）

####  Roberta的改进

- More data，Large batch size，Larger sequence length，Training longer
- No NSP（Full sentences，每次输入连续多个句子直到512，可以跨文章）
- Dynamic masking（ RoBERTa一开始把预训练的数据复制10份，每一份都随机选择15%的Tokens进行Masking，也就是说，同样的一句话有10种不同的mask方式。然后每份数据都训练N/10个epoch。这就相当于在这N个epoch的训练中，每个序列的被mask的tokens是会变化的。这就叫做动态Masking。 ）
- Byte level BPE

####  补充：XLnet与bert

自回归与自编码的区别