整理word2vec和fasttext的原理。

<!--more-->

##  word2vec

word2vec主要包括Skip-Gram和CBOW两种模型。

Skip-Gram是利用一个词语作为输入，来预测它周围的上下文 ，CBOW是利用一个词语的上下文作为输入，来预测这个词语本身 。

###  简单情形

输入：one-hot encoder

隐层：对输入的词求和取平均+线性层

输出：隐层

**word2vec的本质：**是一种**降维**操作——把词语从 one-hot encoder 形式的表示降维到 Word2vec 形式的表示，是一个|V|分类问题。

**训练目标：** 极小化负对数似然 

   **Tip：** CBOW的窗口内部丢掉了词序信息，但是在窗口滑动过程中是按照通顺的自然语言顺序滑动的，或者样本就是按照一定的语序取出来的，所以最终得出的词向量中会包含一定的语序信息。

###  Word2vec 的训练trick

 Word2vec 本质上是一个语言模型，它的输出节点数是 V 个，对应了 V 个词语，本质上是一个多分类问题，但实际当中，词语的个数非常非常多，会给计算造成很大困难，所以需要用技巧来加速训练。 

- **Word pairs and “phase”**: 对常见的单词进行组合或者词组作为单个words来处理（比如` "New York" `,`"United Stated"`）

- **高频词采样：**对高频词进行抽样来减少训练样本的个数（比如`the`这个词在很多次的窗口中出现吗，他对其他词语义学习的帮助不大，并且，更多包含`the`的训练数据远远超出了学习`the`这个词向量所需要的样本数）

- **负采样：**用来提高训练速度并且改善所得到词向量的质量 。

  随机选取部分词作为负样本（比如当vocab_size为10000时，训练样本 `input word:"fox",output word:"quick"` ,在9999个负样本的权重中选择5-20个更新，参数大大减少）。

  - 如何选择negative words？

    根据词频进行负采样，出现概率高的单词容易被选为负样本。 每个单词被赋予一个权重。	
    $$
    P(w_i)=\frac{f(w_i)^{3/4}}{\sum^n_{j=0}(f(w_i)^{3/4})}
    $$
  
 - **层次softmax（Hierarchical Softmax）**

    在进行最优化的求解过程中：从隐藏层到输出的Softmax层的计算量很大，因为要计算所有词的Softmax概率，再去找概率最大的值。 

    word2vec采用了**霍夫曼树**来代替从隐藏层到输出softmax层的映射 。

   和之前的神经网络语言模型相比，霍夫曼树的所有内部节点就类似之前神经网络隐藏层的神经元,其中，根节点的词向量对应我们的投影后的词向量，而所有叶子节点就类似于之前神经网络softmax输出层的神经元，叶子节点的个数就是词汇表的大小。在霍夫曼树中，隐藏层到输出层的softmax映射不是一下子完成的，而是沿着霍夫曼树一步步完成的，因此这种softmax取名为 **Hierarchical Softmax** 。 
   
   在word2vec中，我们采用了二元逻辑回归的方法，即规定沿着左子树走，那么就是负类(霍夫曼树编码1)，沿    着右子树走，那么就是正类(霍夫曼树编码0)。判别正类和负类的方法是使用sigmoid函数， 采用随机梯度上升求解二分类，每计算一个样本更新一次误差函数 。
   
   **使用霍夫曼树有什么好处：**

​		首先，由于是二叉树，之前计算量为`V`,现在变成了`log2V`。

​		第二，由于使用霍夫曼树是**高频的词靠近树根**，这样高频词需要更少的时间会被找到，这符合贪心优化思想。 		

 

##  fasttext

fasttext是基于浅层神经网络训练的，其训练方式与word2vec中的CBOW方式如出一辙，fasttext是对整个句子的n-gram特征相加求平均，得到句向量，在根据句向量做分类。    	

fasttext的输入：embedding过的单词的词向量和n-gram向量		

内存考虑：**哈希映射**，将n-gram映射到固定K个的索引上，相同的索引共享相同的embedding。

## fasttext与word2vec对比

- fasttext用作分类是有监督的，word2vec是无监督的
- fasttext输入部分考虑了n-gram特征，word2vec的输入只有one-hot encoder
- fasttext可以表示oov的单词

word2vec的不足：

1. 多义词的问题。
2. Word2vec 是一种静态的方式，无法针对特定任务做动态优化

fasttext的不足：

fastText很难学出词序对句子语义的影响，对复杂任务还是需要用复杂网络学习任务的语义表达。



##  参考资料

[Word2Vec Tutorial - The Skip-Gram Model](http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/)

[秒懂词向量Word2vec的本质](https://zhuanlan.zhihu.com/p/26306795)