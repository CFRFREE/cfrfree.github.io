---
title: SVM学习笔记1
mathjax: true
date: 2023-03-26 16:49:05
categories: 机器学习
description: SVM（支持向量机）和感知机一样也是一种二分类模型，其中引入的间隔以及核技巧使之成为一个十分经典的算法。
---

## 定义

先让我们回顾一下同为二分类模型的感知机，其采用所有误分类点到超平面的距离和作为损失函数，而SVM则是希望所有误分类点到超平面的最小距离最大，要想达成这一步首先要了解函数间隔和几何间隔这两个概念：

### 1.函数间隔

二分类样本点$(x_{i},y_{i})$关于超平面$(w,b)$的函数间隔定义为$\hat{\gamma_{i}}=y_{i}(w\cdot x_{i}+b)$，在此基础上我们还定义超平面$(w,b)$关于数据集$T$的函数间隔为所有样本点函数间隔的最小值，即
$$
\hat{\gamma}=\min{\hat{\gamma_{i}}}
$$
在选择超平面时我们发现如果成比例地改变$w$和$b$，超平面本身没有改变但其函数间隔却会跟着发生变化，因此我们可以对超平面的法向量$w$进行规范化，比如令$||w||=1$。

### 2.几何间隔

二分类样本点$(x_{i},y_{i})$关于超平面$(w,b)$的几何间隔定义为$\gamma_{i}=y_{i}(\cfrac{w}{||w||}\cdot x_{i}+\cfrac{b}{||w||})$，至于超平面$(w,b)$关于数据集$T$的几何间隔还是所有样本点几何间隔的最小值，对比函数间隔$\hat{\gamma}$和几何间隔$\gamma$的表达式我们还可以发现
$$
\gamma=\cfrac{\hat{\gamma}}{||w||}
$$


## 算法

SVM的基本思想是找到能正确划分数据集并且几何间隔最大的超平面，也就是求解下面这个约束最优化问题：
$$
\mathop{\max}\limits_{w,b} \gamma\\
s.t.\quad y_{i}(\cfrac{w}{||w||}\cdot x_{i}+\cfrac{b}{||w||})\ge\gamma\\
$$
考虑到上文我们发现的数间隔$\hat{\gamma}$和几何间隔$\gamma$的关系，上述问题可以写为：
$$
\mathop{\max}\limits_{w,b} \cfrac{\hat{\gamma}}{||w||}\\
s.t.\quad y_{i}(\cfrac{w}{||w||}\cdot x_{i}+\cfrac{b}{||w||})\ge\hat{\gamma}
$$
对于这个问题，我们其实不关心$\cfrac{\hat{\gamma}}{||w||}$究竟能取多大而是关心取最大值时$w$和$b$的选择，又联系到我们证明过$\hat{\gamma}$可以随着$w$和$b$的等比例变化而变化，因此可以把这个最优化问题改写为如下形式：
$$
\mathop{\min}\limits_{w,b} \frac{1}{2}||w||^{2}\\\\
s.t.\quad y_{i}(w\cdot x_{i}+b)-1\ge0
$$
这是一个凸二次规划问题，当我们解决这个问题之后要找的超平面也就得到了，不妨记为$(w^{\*},b^{\*})$，那么此时分类决策函数便为$f(x)=\text{sign}(w^{\*}\cdot x+b^{\*})$

## 通过求解对偶问题解决上述模型

通过求解对偶问题可以更方便地得到原始问题的最优解，同时还可以很自然引入核函数，进而使SVM推导致非线性分类问题。

对于上述问题，我们先引入拉格朗日乘子$a_{i}\ge0$，然后定义拉格朗日函数：
$$
L(w,b,a)=\frac{1}{2}||w||^{2}-\sum_{i=1}^{N}a_{i}y_{i}(w\cdot x_{i}+b)+\sum_{i=1}^{N}a_{i}
$$
根据拉格朗日对偶性，原始问题的对偶问题是极大极小问题：
$$
\mathop{\max}\limits_{a}\mathop{\min}\limits_{w,b}L(w,b,a)
$$
所以需要我们先求$L(w,b,a)$对$(w,b)$的极小，再求对$a$的极大。

### 1.求$\mathop{\min}\limits_{w,b}L(w,b,a)$

将拉格朗日函数$L(w,b,a)$分别对$w,b$求偏导并令其为$0$
$$
\nabla_{w}L(w,b,a)=w-\sum_{i=1}^{N}a_{i}y_{i}x_{i}=0\\
\nabla_{b}L(w,b,a)=-\sum_{i=1}^{N}a_{i}y_{i}=0
$$
所以我们得到
$$
\begin{cases}
w=\sum_{i=1}^{N}a_{i}y_{i}x_{i}\\\\
\sum_{i=1}^{N}a_{i}y_{i}=0
\end{cases}
$$
将该式带回$L(w,b,a)$就可以得到
$$
L(w,b,a)=\frac{1}{2}\sum_{i=1}^{N}\sum_{j=1}^{N}a_{i}a_{j}y_{i}y_{j}(x_{i}\cdot x_{j})-\sum_{i=1}^{N}a_{i}y_{i}((\sum_{j=1}^{N}a_{i}y_{i}x_{j})\cdot x_{i}+b)+\sum_{i=1}^{N}a_{i}\\
L(w,b,a)=-\frac{1}{2}\sum_{i=1}^{N}\sum_{j=1}^{N}a_{i}a_{j}y_{i}y_{j}(x_{i}\cdot x_{j})+\sum_{i=1}^{N}a_{i}
$$
即
$$
\mathop{\min}\limits_{w,b}L(w,b,a)=-\frac{1}{2}\sum_{i=1}^{N}\sum_{j=1}^{N}a_{i}a_{j}y_{i}y_{j}(x_{i}\cdot x_{j})+\sum_{i=1}^{N}a_{i}
$$

### 2.求$\mathop{\min}\limits_{w,b}L(w,b,a)$对$a$的极大

此时我们便需要解决下面这个最优问题：
$$
\mathop{\max}\limits_{a}-\frac{1}{2}\sum_{i=1}^{N}\sum_{j=1}^{N}a_{i}a_{j}y_{i}y_{j}(x_{i}\cdot x_{j})+\sum_{i=1}^{N}a_{i}\\
s.t.\quad \sum_{i=1}^{N}a_{i}y_{i}=0\\
a_{i}\ge0
$$
将目标函数的求最大便为求最小，就得到了与之对应的对偶最优问题：
$$
\mathop{\min}\limits_{a} \frac{1}{2}\sum_{i=1}^{N}\sum_{j=1}^{N}a_{i}a_{j}y_{i}y_{j}(x_{i}\cdot x_{j})-\sum_{i=1}^{N}a_{i}\\
s.t.\quad \sum_{i=1}^{N}a_{i}y_{i}=0\\
a_{i}\ge0
$$

请留意这里的$(x_{i}\cdot x_{j})$，之后介绍核技巧的时候会派上用场。在求解完这个问题得到$a^{\*}=\set{a_{1}^{\*},a_{2}^{\*}\cdots a_{n}^{\*}}$之后，我们可以计算出$w^{\*}=\sum a_{i}^{\*}x_{i}y_{i}$，并选择$a^{j}>0$计算$b^{\*}=y_{j}-\sum_{i=1}^{N}a_{i}^{\*}y_{i}(x_{i}\cdot x_{j})$

