---
title: cuda_knowledge
author: 范耀文
top: false
cover: false
toc: true
mathjax: false
date: 2020-01-20 09:43:36
img: /medias/banner/gpu.jpg
coverImg:	
password:
summary:
	这个笔记记录了一些关于显卡的相关知识。
tags:
	- cuda 
categories:	
	- GPU
---

## cuda的相关操作

1. 查看驱动（drive cuda）版本

   ```bash
   nvidia-smi
   ```

   这个命令还可以实时显示GPU的使用情况

   ```bash
   watch -n 1 nvidia-smi  #一秒刷新一次
   ```

2. 查看cuda toolkit的版本

   ```python
   nvcc --version
   ```

   

## 不错的参考资料

[Pytorch 使用不同版本的cuda](https://www.cnblogs.com/yhjoker/p/10972795.html)

[显卡，显卡驱动，nvcc,cuda概念的介绍](https://www.cnblogs.com/marsggbo/p/11838823.html)

[安装详解](https://www.jianshu.com/p/9f89633bad57)

