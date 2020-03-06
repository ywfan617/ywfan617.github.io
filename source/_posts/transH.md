---
title: transH
author: 范耀文
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-28 10:40:08
img: /medias/banner/paper.jpg
coverImg: 
password:
summary:
	TransH论文笔记。
tags:
	- Trans系列
categories:
	- KG
typora-root-url: ..
---

## 背景

我们处理将由实体和关系组成的大规模知识图谱嵌入到连续向量空间中的问题。TransE是一种有效的嵌入方法。但是 ，TranE方法适用于一对一关系，在处理自反，一对多，多对一和多对多的关系上做的并不好；同时，一些复杂的模型能够很好的处理这些复杂关系，但是效率低下，为了平衡模型容量和效率，提出了TransH。

## 任务

+ 分析TransE在自反/一对多/多对一/多对多关系上的问题
+ 提出TransH解决方法
+ 如何构建负例以减少训练中的虚假负例

## 符号

$h$：head entity

$r$ ：relation

$t$ ：tail entity

$\textbf{h},\textbf{r},\textbf{t}$ ：连续空间内的向量

$\Delta$ :：组集合

$\Delta^{’}$ ：错误三元组集合

$E$ ：实体集合

$R$ ：关系集合

## TransE算法存在的问题

+ transE模型的两个结论
  + 如果$(h,r,t)∈Δ$ and $(t,r,h)\in\Delta$ ,即 关系$r$ 是一个自反的映射，那么可以知道$\textbf{r=0}$ 并且$\textbf{h}=\textbf{t}$ 
  + 如果$\forall_i\in\{0,1,2,\cdots,m\},(h_i,r,t)\in\Delta$ ,也就是说r是一个多对一的映射，那么$\textbf{h}_0=\textbf{h}_1=\textbf{h}_2=\cdots=\textbf{h}_m$ .同理，对于$\forall_i,(h,r,t_i)\in\Delta$是一个多对一的映射，那么可以知道$\textbf{t}_0=\textbf{t}_1=\textbf{t}_2=\cdots=\textbf{t}_n$ 
+ 从上述结果可以发现，transE算法在处理自反关系，以及多对一、一对多、多对多关系中，会使得一些不同的实体具有相同或者相似的向量。其根本原因在于，**出现在多个关系中的同一个实体的表示是相同的**。
+ 问题举例
  + (美国，总统，奥巴马)，（美国，总统，布什），在TransE中，奥巴马和布什表示为相似向量

## tranH

+ 核心思想

  每一个关系定义一个超平面${W}_r$ ,和一个关系向量$\textbf{d}_r$ 。$\textbf{h}_{\bot},\textbf{t}_{\bot}$是$h$，$t$在$W_r$ 上的投影向量，我们希望正确的三元组满足$\textbf{h}_{\bot}+\textbf{d}_r=\textbf{t}_{\bot}$ 。**这样能够使得同一实体在不同关系中的意义不同，同时不同实体，在同一关系中的意义，也可以相同。**
  <img src="/images/transH/1.png" alt="TransH" style="zoom:50%;" />

+ 得分函数
  $$
  d(\textbf{h}+\textbf{r},\textbf{t})=f_r(\textbf{h},\textbf{t})=\lVert \textbf{h}_{\bot}+\textbf{d}_r-\textbf{t}_{\bot}\rVert_2^2
  $$
  

  对于平面${W}_r$ 我们可以使用法向量来表示，我们不妨假设$\textbf{w}_r$ 为平面$W_r$ 的法向量，并加约束条件$\lVert \textbf{w}_r\rVert_2^2=1$,所以我们知道$\textbf{h}$在$\textbf{w}_r$ 上的投影为
  $$
  \textbf{h}_{\textbf{w}_r}=\textbf{w}^T\textbf{h}\textbf{w} (\textbf{w} \cdot \textbf{h}=\textbf{w}^T\textbf{h})
  $$
  这是因为$|w_r||h|cos\theta$ 表示h在w方向上投影的长度，再乘以$w_r$即为$h$在$w_r$上的投影向量，所以得出
  $$
  f_r(\textbf{h},\textbf{t})=\lVert \textbf{h}_{\bot}+\textbf{d}_r-\textbf{t}_{\bot}\rVert_2^2=\lVert \textbf{h}-\textbf{h}_{\textbf{w}_r}+\textbf{d}_r-(\textbf{t}-\textbf{t}_{\textbf{w}_r})\rVert_2^2=\lVert \textbf{h}-\textbf{w}^T\textbf{h}\textbf{w}+\textbf{d}_r-(\textbf{t}-\textbf{w}^T\textbf{t}\textbf{w})\rVert_2^2
  $$
  

<img src="/images/transH/2.png" alt="TransH" style="zoom:50%;" />

+ 代价函数

$$
L=\sum_{(h,r,t)\in\Delta}\sum_{(h^{'},r^{'},t^{'})\in \Delta}[f_r(\textbf{h},\textbf{t})+\gamma+f_{r^{'}}(\textbf{h}^{'},\textbf{t}^{'})]_+
$$

在公式中，$[x]_+\triangleq max(x,0)$ 

+ 训练的限制条件

  + $\forall e\in E, ||{\bf e}||_2 \leq 1$ , # 控制实体嵌入大小
  + $\forall r \in R, \frac{|{\bf w}_t^T {\bf d}_r|}{||{\bf d}_r||_2} \leq \epsilon$ , # 正交，控制转移向量落在超平面
  + $\forall r \in R. ||{\bf w}_r||_2=1$ , # 单位法向量

+ 无约束代价函数
  $$
  L=\sum_{(h,r,t)\in\Delta}\sum_{(h^{'},r^{'},t^{'})\in \Delta}[f_r(\textbf{h},\textbf{t})+\gamma+f_{r^{'}}(\textbf{h}^{'},\textbf{t}^{'})]_+ + \{\sum_{e\in E}[||{\bf e}||_2^2 -1]_+ + \sum_{r \in R}left[\frac{({\bf w}_t^T {\bf d}_r)^2}{||{\bf d}_r||_2^2} - \epsilon ^2 ]_+ \}
  $$
  

  + C是软约束的超参数权重
  + 注意到约束3不在loss公式里，在访问每一个mini-batch时将每一个${bf w}_r$投影到单位 [$L_2-\bf {ball}$](https://blog.csdn.net/zouxy09/article/details/24971995)。

## 负样本构造

在本篇论文中除了提出了TranH模型，另一贡献是提出了基于伯努利分布的采样方法。在原来的均匀采样中，容易将错误的负例引入到训练过程中来，例如，对于正例（美国，总统，奥巴马），随机替换奥巴马为罗斯福构成（美国，总统，罗斯福）作为负例，实际上由于罗斯福也是总统，这并不是一个负例。新的采样方法的动机是，对于一对多关系，我们以更大的概率来替换其头实体，对于多对一关系，我们以更大的概率来替换其尾实体。

相对于TransE模型的随机采样生成负样本，TransH模型的创新点：
**赋予头尾实体采样概率**

- 处理1-N关系时，赋予头部实体更高的采样概率；
- 处理N-1关系时，赋予尾部实体更高的采样概率；

在所有关系r的三元组中，首先获取两个数据：

- $tph$: 每一个头部实体对应的平均尾部实体;
- $hpt$: 每一个尾部实体对应的平均头部实体;

针对这两个数据进行映射的分类：
$$
\left[\begin{array}{rcl}
1-1 & \mbox{for} & tph_r<1.5,hpt_r <1.5\\
N-N & \mbox{for} & tph_r \geq 1.5,hpt_r \geq 1.5\\
1-N & \mbox{for} & tph_r \geq 1.5,hpt_r < 1.5\\
N-1 & \mbox{for} & tph_r < 1.5,hpt_r \geq 1.5\\
\end{array}
\right]
$$
定义一个伯努利分布进行采样：

- $\frac{tph}{tph+hpt}$: 采样头部实体的概率;
- $\frac{hpt}{tph+hpt}$: 采样尾部实体的概率;

## 

在三个任务上研究评估模型：从不同视角和应用层面，评估对陌生三元组的预测精度

- link prediction
- triplets classification
- relational fact extraction

## 模型评估

#### link prediction

+ **任务**

  三元组的头部或者尾部实体缺失，给定 $(h,r)$ 预测 $t$ 或者给定 $(r,t)$ 预测 $h$ , 返回预测的候选排名；

+ **评估协议**

 对于给定的三元组，用每个实体 $e$ 取代尾部实体 $t$ ，并计算损坏样本的差异性分数，升序排列获取原始三元组的排名；

+ **过滤**

生成的负例三元组可能原本就存在于知识图谱中，需要删去；

+ **报告指标**：
  + Mean:平均排名:
  + $Hits@10$: 排名不超过10的比例

#### triplets classification

**任务**: 对给定三元组进行二分类判断正负样本；

#### relational fact extraction

(待补充)