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

- **DistillBert**

  - teacher model: bert

  - student model改动：

    - 每2层去掉1层（作者调研后结果是隐藏层维度的变化比层数的变化对计算性能的影响较小，所以只改变了层数，把计算层数减小到原来的一半）
    
    - 去掉了token type embedding和pooler
    
  - 每一层加了初始化，每一层的初始化为teacher model的参数。 
    
  - 在训练过程中使用了**动态掩码**（dynamic masking）然后没有使用next sentence objective。训练数据和原始的Bert训练使用的一样。 
    
  - loss设计：

    - Lce loss:  Teacher model的soft label的损失函数， Teacher model的logits ti/T(T 为温度),通过softmax计算输出得到teacher的概率分布，与student model logits si/T(T为温度)，通过softmax计算输出得到student的概率分布，最后计算两个概率分布的KL散度 
    - Lmlm loss: Bert的Mask language modeling loss( 单词级别分类任务 )

    - Lcos loss:  计算teacher hidden state和student hidden state的余弦相似度。官方代码用的是：nn.CosineEmbeddingLoss

      整体的loss：Loss = 5.0\*Lce + 2.0\*Lmlm + 1.0\*Lcos

  

- **Bert-PKD**

  普通的知识蒸馏模型用来对模型进行压缩的时候, 通常都会损失很多精度。原因是学生模型 (student model) 在学习的时候只是学到了教师模型 (teacher model) 最终预测的概率分布，而完全忽略了中间隐藏层的表示。  该方法提出了一种损失函数，使得学生模型的隐藏层表示接近教师模型的隐藏层表示，从而让学生模型的泛化能力更强。文章称这种模型为“耐心的知识蒸馏”模型 (Patient Knowledge Distillation， 或者PKD)。 

  - teacher model：bert

  - student model改动：

    - **蒸馏模型的中间层：** PKD-last(7,8,9,10,11)  PKD-skip(2,4,6,8,10)
    - **student初始化：**teacher的前几层

  - loss设计

    - 中间层损失L_PT (MSE)
    - 真实标签损失L_CE (CE)
    - student和teacher之间的损失L_DS (temperature softmax)

    LOSS = (1-a)\*L_CE + a\*L_DS + L_PT

- **TinyBert**

  一般的蒸馏只是蒸馏bert的最后一层的结果，而很少关注transformer中间层的信息

  - loss设计

    - **Embedding-layer **

      student和teacher的embedding之间计算MSE

    - **Transformer-layer ** **采用隔 k 层蒸馏的方式 **

      若设置student BERT的层数为4，则teacher的第3、6、9、12分别对应student的1、2、3、4层

      每一层的transformer的loss又包含2个部分：

      - **attention based distillation**

        对attention score矩阵(n*n)计算MSE

        （attention可以学到句子的语法相关信息，并且[CLS],[SEP]等token有很高的权重）

      - **hidden states based distillation**

        student和teacher的transformer的隐层输出计算MSE

    -  **Prediction-Layer **

      ​		student和teacher输出概率分布的交叉熵

  - 实验细节   **两阶段学习框架 **

    TinyBERT 先在 general domain 数据集上用未经微调的 BERT 充当教师蒸馏出一个 base 模型，在此基础上，具体任务通过数据增强，利用微调后的 BERT 再进行重新执行蒸馏。

    蒸馏 TinyBERT 的流程是:

    1. 制作任务相关数据集;
    2. fine-tune teacher BERT;
    3. 固定 teacher BERT 参数，蒸馏得到 TinyBERT.

###  Reference

[DistilBERT, a distilled version of BERT: smaller, faster, cheaper and lighter](https://arxiv.org/abs/1910.01108)

[Patient Knowledge Distillation for BERT Model Compression ](https://arxiv.org/abs/1908.09355)

[TinyBERT: Distilling BERT for Natural Language Understanding](https://arxiv.org/abs/1909.10351)

[比 Bert 体积更小速度更快的 TinyBERT](https://zhuanlan.zhihu.com/p/94359189)