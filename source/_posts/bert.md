---
title: bert中容易忽略的细节问题
date: 2021-03-23 20:18:14
tags: [deeplearning]
categories: [bert]
---

面试过程中往往遇到过一些bert中容易忽略的细节，本文做一个整理。

<!--more-->

- bert中的transformer block由multi-head self-attention + FFN结构构成，但是中间还有一个**” intermediate layer “**，即中间层。中间层将Attention-layer的hidden size扩大了4倍，然后再做一次非线性变换(激活函数)，再将hidden size变回size。







