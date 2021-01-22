---
title: knowledge distillation
date: 2021-01-20 15:24:04
tags: [NLP,distillation]
categories: [Distillation]
---

训练和部署两个阶段对模型的要求是不同的。在训练阶段，我们希望模型可以从大量的、高度冗余的信息中学到数据的特征，此阶段对延迟和计算资源没有太严格的要求。但是如果模型要被部署到大量用户那里，对延迟和计算资源的限制就很高。因此，我们可以先训练一个大模型。这个大模型可以是很多独立模型的集成，也可以是单个的使用了Dropout等正则化方法的复杂模型。在大模型训练好之后，我们就可以使用一个称为**“蒸馏”**的训练过程，将大模型中的知识迁移到便于部署的小模型中。

<!--more-->

 知识蒸馏，可以将一个网络的知识转移到另一个网络，两个网络可以是同构或者异构。做法是先训练一个teacher网络，然后使用这个teacher网络的输出和数据的真实标签去训练student网络。知识蒸馏，可以用来将网络从大网络转化成一个小网络，并保留接近于大网络的性能；也可以将多个网络的学到的知识转移到一个网络中，使得单个网络的性能接近emsemble的结果。  知识蒸馏的本质，个人理解，**其实知识蒸馏实际相当于引入先验概率（prior knowledge)**， soft label即是网络输入的先验概率，soft label与真实世界的事物类似，呈各种概率分布。 

  我们通过小的模型去学习大的模型的直接输出，往往能够得到更好的结果，这是因为大的模型的信息熵比onehot的结果往往更多，小的模型能获得的信息量更大。 **使用知识蒸馏得到的小模型，其泛化能力会比使用一般方法训练得到的小模型要好。** 



###  基于bert的知识蒸馏

- DistillBert

  - teacher model: bert

  - student model改动：

    1. 每2层去掉1层（作者调研后结果是隐藏层维度的变化比层数的变化对计算性能的影响较小，所以只改变了层数，把计算层数减小到原来的一半）
    2.  去掉了token type embedding和pooler
    3.  每一层加了初始化，每一层的初始化为teacher model的参数。 
    4.  在训练过程中使用了动态掩码（dynamic masking）然后没有使用next sentence objective。训练数据和原始的Bert训练使用的一样。 

    损失函数设计：

    1. Lce loss:  Teacher model的soft label的损失函数， Teacher model的logits ti/T(T 为温度),通过softmax计算输出得到teacher的概率分布，与student model logits si/T(T为温度)，通过softmax计算输出得到student的概率分布，最后计算两个概率分布的KL散度 
    2. Lmlm loss: Bert的Mask language modeling loss( 单词级别分类任务 )
    3. Lcos loss:  计算teacher hidden state和student hidden state的余弦相似度。官方代码用的是：nn.CosineEmbeddingLoss

       整体的loss：Loss=5.0\*Lce+2.0\*Lmlm+1.0\*Lcos

- Bert-PKD

  普通的知识蒸馏模型用来对模型进行压缩的时候, 通常都会损失很多精度。原因是学生模型 (student model) 在学习的时候只是学到了教师模型 (teacher model) 最终预测的概率分布，而完全忽略了中间隐藏层的表示。  该方法提出了一种损失函数，使得学生模型的隐藏层表示接近教师模型的隐藏层表示，从而让学生模型的泛化能力更强。文章称这种模型为“耐心的知识蒸馏”模型 (Patient Knowledge Distillation， 或者PKD)。 



###  Reference

[DistilBERT, a distilled version of BERT: smaller, faster, cheaper and lighter](https://arxiv.org/abs/1910.01108)

