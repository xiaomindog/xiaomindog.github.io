---
title: 论文笔记之HMEAE
date: 2019-12-12 14:17:50
tags: [NLP,paper]
categories: [paper]
---

论文地址：[ HMEAE: Hierarchical Modular Event Argument Extraction ]( https://www.aclweb.org/anthology/D19-1584.pdf )

### 摘要

现有的事件提取方法将每个论元角色独立分类，而忽略了不同论元角色之间的概念相关性。本文提出了事件论元抽取**HMEAE模型**，以从事件论元角色的概念层次结构提供有效的归纳偏差。具体来说，我们为概念层次结构的每个基本单元设计了一个神经模块网络，然后将具有逻辑操作的相关单元模块按层次结构组合到面向规则的模块化网络中，以对特定的论元角色进行分类。

<!--more-->

### 简介

![](/figure1.png)

以图1为例，该论文认为，Seller与Buyer概念上比Seller与Time-within更接近，因为他们在概念层次结构中共享相同的Superordinate Concept ”Person“和”Org“。直观上认为这会提供更多语义信息以提升论元抽取的性能。

如图1所示，将概念分成2部分：上层概念和下层的细粒度的论元角色。一个论元角色可能属于2个或以上的上层概念。

![](/model.png)

如图2所示，模型主要分成3个部分。1）对于每一个上层概念，实例化上层概念模块（SCM）以突出显示与该概念相关的文本信息；2）然后，对于每个论元角色，由其特定角色的逻辑联合模块组成与其上位概念相对应的SCM，以获得统一的高级模块；3）最后，根据高级模块的输出设置参数角色分类器，以预测实体是否属于给定的论元角色。

###  Instance Encoder 

将句子表示成n个词的序列，x={w1,...t,...,a,...,wn}，t表示触发词，a是候选论元。触发词在ED工作中已经识别出，与EAE任务相互独立。

#### Sentence Encoder

将单词序列编码为隐藏层表示，{h1,h2,...,hn} = E(w1,,...t,...,a,...,wn)，E(.)是对句子进行编码的神经网络。本文采用CNN和BERT作为编码器。

#### Feature Aggregator

特征聚合器将隐层向量聚合到一个实例向量中，延用DMCNN的方法。[DMCNN]( https://www.jianshu.com/p/84fd666b1900 )



###  Hierarchical Modular Attention 

上层概念模块会给每个隐藏的嵌入提供一个Attention分数，与其相关性建模。

####  Superordinate Concept Module 

上层概念模块，对于特定的上层概念c，我们用可训练的向量Uc表示其语义特征。 采用多层感知器来计算注意力得分。 首先计算隐藏状态，=========(3)。[参考论文]( https://www.aclweb.org/anthology/D15-1166.pdf ) [论文笔记]( https://www.jianshu.com/p/1c24eba3ba9c )



再通过softmax计算获得隐层的Attention分数，=====(4)其中Wa和Wb是在不同上层概念模块之间共享的可训练  矩阵。

####  Logic Union Module 

给定论元角色r∈R，我们将其k个上层概念表示为c1,c2,...ck。，ck和hi的相应注意力得分由(4)计算。由于应将所有上层概念的信息保留在面向角色的嵌入中，因此我们将注意力得分的平均值计算为面向角色的注意力得分(5)。

然后计算隐层embedding的加权总和作为面向论元角色的embedding。

###  Argument Role Classifier

将实例词嵌入x和面向角色的嵌入e^r串联起来作为论元角色分类器的输入特征，并估算实例x的r属于R的概率，其中r是论元角色r的embedding。

### Overall Evaluation Results

![](/result.png)

###  其他 

上层概念模块有8类：[Person,Place,Org,Time,Good,Behavior,Entity,NA]。

