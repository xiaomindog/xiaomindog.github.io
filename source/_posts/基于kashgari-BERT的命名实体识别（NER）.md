---
title: 基于kashgari+BERT的命名实体识别（NER）
date: 2019-12-06 15:03:07
tags: [NLP,NER,bert,kashgari]
categories: 'NLP'
---

最近帮师兄做NER的BIO标注进行触发词识别的对比实验，用到了一个很好的框架[kashgari](https://github.com/BrikerMan/Kashgari)，基于 tf.keras编写，使用起来很方便，几分钟即可搭建一个文本分类/命名实体识别的baseline。对于命名实体识别任务，该框架封装了BiLSTM，BiGRU，BiLSTM+CRF，BiGRU+CRF，CNN_LSTM。可加载wordembedding和bert词向量。

<!--more-->

###  环境安装

```
pip install tensorflow==1.14
pip install kashgari-tf
python3.6
```

###  数据准备

NER标签采用BIO标注的形式。

train_x和 train_y，test_x和test_y都是list类型。

```python
train_x: [[char1],[char1],[char1],..... ]
train_y: [[label1],[label2],[label3],..... ]
#eg:bert基于字级别的BIO数据
train_x = [['立','法','院','成','立','刺','激','团','体'],...]
train_y = [['B-ORG','I-ORG','I-ORG','O','O','O','O','O','O'],...]
```

###  导入模型，加载预训练embedding

[BERT-Base, Chinese](https://github.com/google-research/bert)

[Chinese-Word-Vectors](https://github.com/Embedding/Chinese-Word-Vectors)

```python
import kashgari
from kashgari.tasks.labeling import BiLSTM_CRF_Model, BiGRU_CRF_Model
from kashgari.embeddings import BERTEmbedding, WordEmbedding

BERT_PATH = './data/chinese_L-12_H-768_A-12'
bert_embedding=BERTEmbedding(BERT_PATH,task=kashgari.LABELING,sequence_length=100)
#如果加载wordembedding的词向量
#word_embedding = WordEmbedding(embed_path, task=kashgari.LABELING, 								sequence_length=100)

```

###  模型训练

```python
model = BiLSTM_CRF_Model(embedding)

model.fit(train_x, train_y,x_validate=val_x, y_validate=val_y,
          epochs=40,batch_size=128)
#如果没有开发集
#model.fit(train_x, train_y,epochs=40,batch_size=128)

model.evaluate(test_x, test_y)

```

### 实验结果及代码

实验证明BERT+BiLSTM+CRF取得的效果是最好的。完整代码待上传github。

###  

