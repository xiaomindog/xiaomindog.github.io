---
title: Transformer
date: 2021-01-20 11:08:46
tags: [NLP,deeplearning]
categories: [deeplearning]
mathjax: true
---

本文以QA的方式整理关于transformer的知识点。

<!--more-->

###  1.Transformer的结构是什么样的？

transformer本质上还是一个典型的encoder-decoder模型，encoder和decoder端各有6个相同的大模块构成。

- encoder（由2个子模块构成）：多头self-attention+前馈神经网络
- decoder（由3个子模块构成）：多头self-attention+多头Encoder-Dencoder attention交互模块+前馈神经网络

###  2. Transformer Encoder和Decoder 端的输入输出具体是什么？

Encoder端：

- 输入：Encoder 端每个大模块接收的输入是不一样的，第一个大模块(最底下的那个)接收的输入是输入序列的 embedding(embedding 可以通过 word2vec 预训练得来)，其余大模块接收的是其前一个大模块的输出。
- 输出：最后一个模块的输出作为整个 Encoder 端的输出。

Decoder端：

- 输入：encoder的输出 & 对应i-1位置decoder的输出。所以中间的attention不是self-attention，它的K，V来自encoder，Q来自上一位置decoder的输出
- 输出：对应i位置的输出词的概率分布
- 解码：**编码可以并行计算，一次性全部encoding出来，但解码不是一次把所有序列解出来的，而是像rnn一样一个一个解出来的**，因为要用上一个位置的输入当作attention的query。

###  3. Transformer 中的 self-attention 是什么？self-attention 的计算过程？

- self-attention是什么？

  self-attention是一种通过自身和自身相关联的 attention 机制（而attention本身就是一种特征加权求和的计算）。在 self-attention 中， 序列中的每个单词和该序列中其余单词进行 attention 计算。self-attention 的特点在于**无视token之间的距离直接计算依赖关系，从而能够学习到序列的内部结构**，实现起来也比较简单。引入 Self Attention 后会**更容易捕获句子中长距离的相互依赖的特征**，因为如果是 RNN 或者 LSTM，需要依次序序列计算，对于远距离的相互依赖的特征，要经过若干时间步步骤的信息累积才能将两者联系起来，而距离越远，有效捕获的可能性越小。 Self-Attention 在计算过程中会直接将句子中任意两个单词的联系通过一个计算步骤直接联系起来，所以远距离依赖特征之间的距离被极大缩短，有利于有效地利用这些特征。除此外，Self Attention 对于**增加计算的并行性**也有直接帮助作用。

- self-attention的计算过程
  $$
  Attention(Q,K,V)=softmax(\frac{QK^{T}}{\sqrt{d_{k}}})V
  $$
  
- **为什么要有一个根号d_k？** 假设两个 dk 维向量每个分量都是一个相互独立的服从标准正态分布的随机变量，那么他们的点乘结果会变得很大，并且服从均值为0，方差就是 dk，【很大的点乘会让softmax函数处在梯度很小的区域】，对每一个分量除以 sqrt(d_k) 可以让点乘的方差变成 1。 

###  4.如何理解self-attention中的Q,K,V?

​	**计算attention的过程，即使用一个Q(query)，计算它和每个K(key)的相似度作为权重，对所有的V(value)进行加权求和。**

​	QKV来源于input的embedding，通过神经网络学习到不同的权重。简单来理解：Q是当前词，K是其他词，V是内容。

###  5. Transformer 为什么需要进行 Multi-head Attention？这样做有什么好处？Multi-head Attention 的计算过程？

将模型分为多个头，形成多个子空间，可以让模型去关注不同方面的信息，最后再将各个方面的信息综合起来。类比 CNN 中同时使用多个卷积核的作用，多头的注意力有助于网络捕捉到更丰富的特征/信息。

###  6. Transformer 是如何训练的？测试阶段如何进行测试呢？

Transformer 训练过程与 seq2seq 类似，首先 Encoder 端得到输入的 encoding 表示，并将其输入到 Decoder 端做交互式 attention，之后在 Decoder 端接收其相应的输入，经过多头 self-attention 模块之后，结合 Encoder 端的输出，再经过 FFN，得到 Decoder 端的输出之后，最后经过一个线性全连接层，就可以通过 softmax 来预测下一个token，然后根据 softmax 多分类的损失函数，将 loss 反向传播即可，所以从整体上来说，Transformer 训练过程就相当于一个有监督的多分类问题。

而对于测试阶段，其与训练阶段唯一不同的是 Decoder 端最底层的输入，先生成第一个位置的输出，然后有了这个之后，第二次预测时，再将其加入输入序列，以此类推直至预测结束。

- 需要注意的是，Encoder 端可以并行计算，一次性将输入序列全部 encoding 出来，但 Decoder 端不是一次性把所有token预测出来的，而是像 seq2seq 一样一个接着一个预测出来的。

###  7. Transformer的并行化体现在哪里？

Transformer 的并行化主要体现在 self-attention 模块，在 Encoder 端 Transformer 可以并行处理整个序列，并得到整个输入序列经过 Encoder 端的输出，在 self-attention 模块，对于某个序列x1,x2,...,xn ，self-attention 模块可以直接计算xi*xj的点乘结果，而 RNN 系列的模型就必须按照顺序从x1计算到xn。

###  8. Transformer的位置编码？

为什么要引入位置编码？ 如果没有位置编码，就失去了词序的信息。

 给定一个长度为n的输入序列，让t表示词在序列中的位置，p_t表示t位置对应的向量，d是向量的维度。f是生成位置向量p_t的函数，定义如下： 
$$
\vec{p_t}^{(i)} = f(t)^{(i)} := \begin{cases}
      \sin({\omega_k} . t),  & \text{if}\  i = 2k \\
      \cos({\omega_k} . t),  & \text{if}\  i = 2k + 1
  \end{cases}
$$

$$
where\quad \omega_k = \frac{1}{10000^{2k / d}}
$$

- 它能为每个时间步输出一个独一无二的编码；
- 不同长度的句子之间，任何两个时间步之间的距离应该保持一致；
- 模型应该能毫不费力地泛化到更长的句子。它的值应该是有界的；
- 它必须是确定性的。
-  正弦曲线函数的位置编码的另一个特点是，它能让模型毫不费力地关注相对位置信息 ， 因为对于任何固定的偏移量![[公式]](https://www.zhihu.com/equation?tex=k)，![[公式]](https://www.zhihu.com/equation?tex=%5Ctext%7BPE%7D_%7B%5Ctext%7Bpos%7D%2Bk%7D)可以表示成![[公式]](https://www.zhihu.com/equation?tex=%5Ctext%7BPE%7D_%7B%5Ctext%7Bpos%7D%7D)的线性函数。 

###  9. Transformer的时间复杂度是多少？对比RNN，CNN呢？

*一个形状为N\*M的矩阵，与另一个形状为M\*P的矩阵相乘，其运算复杂度来源于乘法操作的次数，时间复杂度为 O(N*M*P)。*

- self-attention

  - Q,K,V：n*d
  - 相似度计算：Q*K^T，复杂度为O(n^2d)
  - softmax计算：对每行做softmax，复杂度为 O(n)，则n行的复杂度为 O(n^2)
  - 加权和：n\*n与n\*d运算，得到n\*d矩阵，复杂度为O(n^2d)

  所以self-attention的时间复杂度为O(n^2d)

- Multi-Head Attention
  $$
  MultiHead(Q,K,V) = Concat(head_1,...,head_h)W^O
  $$

  $$
  Where \quad head_i = Attention(QW_{i}^Q,KW_{i}^k,VW_i^V)
  $$

  假设有h个head（h为常数），对于每个head，对于每个head，首先需要把三个矩阵分别映射到 d_q,d_k,d_v 维度。d_q=d_k=d_v=d/h。

  - 输入线性映射的复杂度：n\*d与d\*d/h运算，复杂度为O(nd^2)
  - Attention的计算：O(n^2d)
  - 输出线性映射的复杂度：concat操作拼起来形成 n\*d 的矩阵，然后经过输出线性映射，保证输入输出相同，所以是n\*d 与 d\*d 计算，复杂度为O(nd^2)

  所以Multi-Head Attention的时间复杂度为O(nd^2+n^2d)

  注：多头的计算并不是通过循环完成的，而是通过 transposes and reshapes，用矩阵乘法来完成的。假设有h个head，则新的representation dimension：m=d/h。因为，我们将n\*d矩阵拆为n\*h\*m张量，再利用转置操作转为h\*n\*m 的张量。故 QK^T的计算为： h\*n\*m与 h\*m\*n做计算，得到h\*n\*n的张量，复杂度为O(h^2n^2m)，即O(n^2dh) 。注意，此处h实际是一个常数，故QK^T复杂度为O(n^2d) 。

###  10. Transformer为什么要引入LN？

- LN则是针对每一句话做归一化处理。例如：我是中国人我爱中国——归一化处理后，一句话内每个字之间的联系并没有破坏。 

- layer normalization 有助于得到一个球体空间中符合**0均值1方差**高斯分布的 embedding， batch normalization不具备这个功能。 
- layer normalization可以对transformer学习过程中由于多词条embedding累加可能带来的“尺度”问题施加约束，相当于对表达每个词**一词多义的空间施加了约束**，有效降低模型方差。batch normalization也不具备这个功能。

###  Renference

[Attention Is All You Need](https://arxiv.org/abs/1706.03762)

[关于Transformer，面试官们都怎么问？](https://blog.csdn.net/fengdu78/article/details/104629336)

[The Illustrated Transformer](http://jalammar.github.io/illustrated-transformer/)

[Transformer/CNN/RNN的对比（时间复杂度，序列操作数，最大路径长度）](https://zhuanlan.zhihu.com/p/264749298)

[BERT大火却不懂Transformer？读这一篇就够了](https://zhuanlan.zhihu.com/p/54356280)

[Transformer Architecture: The Positional Encoding](https://kazemnejad.com/blog/transformer_architecture_positional_encoding/)

[Transformer 为什么使用 layer normalization，而不是其他的归一化](https://www.zhihu.com/question/395811291)

[Transformer中的位置编码](https://zhuanlan.zhihu.com/p/106644634)