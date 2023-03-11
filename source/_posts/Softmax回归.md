---
title: Softmax回归与交叉熵损失函数
mathjax: true
date: 2023-03-11 11:34:47
categories: 机器学习
description: softmax回归是一种解决分类问题的常见回归模型
---

## 1、为什么要使用softmax回归

![softmax回归是一个单层神经网络](https://zh-v1.d2l.ai/_images/softmaxreg.svg)

如上图，如果对于一个分类问题我们把输出层的$O$向量直接作为置信度向量，并将值最大的输出所对应的类作为预测输出，即输出$\operatorname*{argmax}_i o_i$，将面临一方面由于输出层的输出值的范围不确定，我们难以直观上判断这些值的意义，另一方面由于真实标签是离散值，这些离散值与不确定范围的输出值之间的误差难以衡量的问题

## 2、什么是softmax回归

softmax回归的计算方式如下：

$$
\hat{y}\_i=\frac{\exp(o\_i)}{\sum_{i=1}^3 \exp(o\_i)}
$$

我们发现$\hat{y}_1 + \hat{y}_2 + \hat{y}_3 = 1$且$0 \leq \hat{y}_1, \hat{y}_2, \hat{y}_3 \leq 1$，因此$\hat{y}_1, \hat{y}_2, \hat{y}_3$是一个合法的概率分布，此外不难发现

$\operatorname*{argmax}_i o_i = \operatorname*{argmax}_i \hat y_i$

## 3、交叉熵函数

对于分类问题我们自然可以像线性回归那样使用平方损失函数，但实际上想要预测分类结果正确，我们其实并不需要预测概率完全等于标签概率。例如，在图像分类的例子里，如果$y^{(i)}=3$，那么我们只需要$\hat{y}^{(i)}_3$比其他两个预测值都大就行，至于这两个具体是多少就无所谓了，于是我们使用交叉熵函数（如下）来定义分类问题中的损失。

$$
H\left(\boldsymbol y^{(i)}, \boldsymbol {\hat y}^{(i)}\right ) = -\sum_{j=1}^q y_j^{(i)} \log \hat y_j^{(i)}
$$

又因为我们知道$\boldsymbol y^{(i)}$是一个独热向量，即只有第$y^{(i)}$个元素为$1$，其他元素均为$0$，所以

$$
H(\boldsymbol y^{(i)}, \boldsymbol {\hat y}^{(i)}) = -\log \hat y_{y^{(i)}}^{(i)}
$$

假设训练数据集的样本数为$n$，交叉熵损失函数定义如下：

$$
\ell(\boldsymbol{\Theta}) = \frac{1}{n} \sum_{i=1}^n H\left(\boldsymbol y^{(i)}, \boldsymbol {\hat y}^{(i)}\right )
$$
