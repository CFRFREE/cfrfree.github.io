---
title: HHM学习笔记3——隐马尔可夫模型
mathjax: true
date: 2023-02-19 13:54:20
categories: 机器学习
description: 这一篇我们将介绍隐马尔可夫模型定义及三种类型的应用
---

## 隐马尔可夫模型

### 定义

首先，我们给出一些常用的标记如下：

- $Q=\set{q_1,q_2,\dots q_N}$代表所有可能的状态集合
- $V=\set{v_1,v_2,\dots v_M}$代表所有可能的观测集合
- $A=[a_{ij}],a_{ij}=P(i_{t+1}=q_j|i_{t}=q_i)$代表状态转移概率矩阵，即任意时刻$t$，当前状态为$q_i$时下个状态为$q_j$的概率，$i,j\in[1,N]$
- $B=[b_{ik}],b_{ik}=P(o_t=v_k|i_t=q_i)$代表观测概率矩阵，即任意时刻$t$，若当前状态为$q_i$则观测值$v_k$被观测到的概率，$i\in[1,N],k\in[1,M]$
- $\pi=(\pi_{i}),\pi_{i}=P(i_1=q_1)$代表初始状态概率向量

那么一个隐马尔可夫模型便可以用三元组$\lambda=(\pi,A,B)$来表示。

基于上文的描述，我们可以发现隐马尔可夫模型有如下特点：

- **齐次性**：即时刻$t$的状态只依赖于$t-1$时的状态，与其他时刻的状态与观测无关。
- **观测独立性**：任意时刻的观测只与该时刻马尔可夫链状态有关，与其他观测与状态无关

### 应用

一旦一个系统可以作为HMM被描述，就可以用来解决三个基本问题。

1. 评估（Evaluation）

    给定许多$\lambda=(\pi,A,B)$，寻找最有可能得到某特定观测序列的模型。

    换言之对某个$\lambda$，求产生特定观测序列的概率。

    这个问题我们通常使用前向算法（forward algorithm）来解决。

2. 解码（ Decoding）

    给定$\lambda=(\pi,A,B)$和观测序列，求状态序列。

    即寻找一个最能解释已知观测序列的状态序列。

    我们使用Viterbi 算法确定（搜索）已知观察序列及$\lambda$下最可能的隐藏状态序列。

3. 学习（Learning）

    给定一个观察序列，求隐马尔可夫模型。

    第三个问题是与HMM相关的问题中最难的，当矩阵$A$和$B$不能够直接被（估计）测量时，前向-后向算法（forward-backward algorithm）被用来进行学习或者说参数估计，这也是实际应用中常见的情况。
