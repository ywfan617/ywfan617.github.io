---
title: transR
author: 范耀文
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-29 10:30:38
img: /medias/banner/paper.jpg
coverImg: 
password:
summary:
	TransE论文笔记。
tags:
	- Trans系列
categories:
	- KG
typora-root-url: ..
---

## 背景

TransE还是TransH都是将实体和关系映射同一个空间，但是，一个实体可能具有多个层面的信息，不同的关系可能关注不同层面的实体。实体和关系表达的最佳维度并不一定是一致的，因此，**映射到同一个空间可能会限制模型效果。**

## 任务

+ 分析TransE，TransH存在的问题
+ TransR
+ CTransR



## TransH存在的问题

虽然TransH模型使得同一实体在不同关系下通过投影有了不同的表示，但投影之后仍然处于原来的空间 $R^𝑛$ 中，这里表示实体向量和关系向量均为$𝑛$维。换言之，TransH模型假设实体和关系处于相同的语义空间中，这在一定程度上限制了它的表示能力。

## TransR

+ 设计理念

  一个实体是多种属性的综合体，不同关系关注实体的不同属性。直觉上一些相似的实体在实体空间中应该彼此靠近，但是同样地，在一些特定的不同的方面在对应的关系空间中应该彼此远离。为了解决这个问题，==我们提出了一种新的方法，将实体和关系投影到不同的空间中，也就是实体空间和多元关系空间（也即是特定关系的实体空间），在对应的关系空间上实现翻译。因此命名为TransR。==

+ 核心思想

  + TransH模型是为每个关系假定一超平面，将实体投影到这个超平面上进行翻译；而TransR模型是为每个关系假定一语义空间 $R^m$ ，将实体映射到这个语义空间上进行翻译。这里$m$表示示关系向量的维度为$𝑚$。
  + 对于每个元组$(h,r,t)$，首先将实体空间中的实体 $\textbf{h},\textbf{t}(\textbf{h},\textbf{t} \in R^d)$ 通过矩阵 $\textbf{M}_r(\textbf{M}_r \in R^{k \times d})$ 向关系 $\textbf{r}(\textbf{r} \in R^k)$ 投影得到$\textbf{h}_r$和$\textbf{t}_r$，然后使$\textbf{h}_r+\textbf{r}≈\textbf{t}_r$。特定的关系投影（彩色的圆圈表示）能够使得头/尾实体在这个关系下真实的靠近彼此，使得不具有此关系（彩色的三角形表示）的实体彼此远离。

  ![1](/images/transR/1.png)

  + 投影向量
    $$
    \textbf{h}_r=\textbf{hM}_r,\textbf{t}_r=\textbf{tM}_r
    $$
  
+ 得分函数
    $$
    f_r(h,t)=\Vert \textbf{h}_r+\textbf{r}−\textbf{t}_r\Vert_2^2
    $$
  
+ 正则约束
    $$
    其中\textbf{h},\textbf{t}∈R^n,\textbf{r}∈R^m,\textbf{M}_r∈R^{n×m} , \textbf{h},\textbf{r},\textbf{t},\textbf{hM}_r,\textbf{tM}_r的L_2范数均小于等于1
    $$
    
  + 基于边际的代价函数
  
  $$
    L=\sum_{(h, r, t) \in S\left(k^{\prime}, r, t^{\prime}\right) \in S^{\prime}} \max \left(0, f_{r}(h, t)+\gamma-f_{r}\left(h^{\prime}, t^{\prime}\right)\right)
  $$
  

## CTransR

+ 提出原因

  在一个特定的关系下，头-尾实体对通常展示出不同的模型。仅仅通过单个的关系向量还不足以建立实现从头实体到尾实体的所有翻译。例如，具有关系 $(location,location,contains)$ 头-尾实体有很多模式，如$country-city$ ，$country-university$ ，$continent-country$ 等等。沿着分段线性回归（Ritzema and others 1994）的思想，通过对不同的头-尾实体对聚类分组和学习每组的关系向量，我们进一步提出了基于聚类的CTransR。

+ 核心思想

  CTransR的意思是Cluster-based TransR. CTransR首先对于每个关系$r$所对应的三元组$(h,r,t)$，先通过TransE学习 $\textbf{h,t}$，然后依据 $\textbf{h-t}$ 进行AP聚类（一种不需要指定类别数的聚类方法），从而将关系 $\textbf{r}$ 分解为更细粒度的子关系 $r_c$ ，CTransR对每个 $r_c$ 分别学习相应的向量表示 $\textbf{r}_c$ 。

+ 投影向量
  $$
  \textbf{h}_{r,c}=\textbf{hM}_r,\textbf{t}_{r,c}=\textbf{tM}_r
  $$
  
+ 得分函数
  $$
  fr(h,t)=\Vert \textbf{h}_{r,c}+\textbf{r}_c−\textbf{t}_{r,c}\Vert_2^2+α\Vert \textbf{r}_c−\textbf{r}\Vert_2^2\\
  $$
  

## 模型评估

  ### 链接预测

+ **任务**

  链接预测指的是对于给定关系和关系的一个实体，预测另外一个实体，例如给定 $(r,t)$  预测 $h$ ,或者是给定 $(h,r)$ 预测 $t$ 。

+ **评估标准**

  + 正确实体的平均排名
  + 正确实体排在前10位的几率（Hist@10）

+ **过滤**

  生成的负例三元组可能原本就存在于知识图谱中，需要删去；

### 三元组分类

+ **任务**

  判断给定的三元组 $(h,r,t)$ 是否正确，这是一个二分类问题。

+ **评估标准**

  对于给定的三元组 $(h,r,t)$ ，我们设置一个阈值 $\delta_r$ ，如果三元组的得分函数小于 $\delta_r$ ，则被归为正类，否则被归为负类。

### 关系提取

+ **任务**

  关系提取旨在从大规模纯文本中提取关系事实，这是丰富知识图谱的重要信息来源。

