---
title: pytorch数据处理
author: 范耀文
top: false
cover: false
toc: true
mathjax: false
date: 2020-02-27 14:43:43
img: /medias/banner/data.jpg
coverImg: 
password:
summary:
	介绍了利用pytorch对数据进行处理的操作。
tags:
	- pytorch
categories:
	- 机器学习框架
---

### 相关概念

#### **一个数据集需要具备哪些功能？**

+ 获取每一个数据及其label
+ 获取数据集的大小（size）

#### 数据集的组织形式

+ 数据集是单独文件夹和对应的标签集单独一个文件夹
+ 数据集是单独一个文件夹，label是每一个数据的文件名
+ 数据集是单独一个文件夹，文件夹的名字是该分类的label

#### DataSet类

`Dataset`是一个抽象类，如果想要创建自己的数据集的话，或者说，想要用`Pytorch`的库来进入数据加载的话，是需要继承这个`Dataset`抽象类的。同时需要重写这个类的函数的魔法函数`__item__()`和`__len__()`。

### 设计代码

```python
# coding=utf-8
"""
Author: ywfan
Email: 1640788287@qq.com
date: 2020/2/25 18:45
"""
from torch.utils.data import Dataset
import os
import matplotlib.pyplot as plt


class MyData(Dataset):

    def __init__(self, datas_dir, labels_dir):
        """

        :param datas_dir: 数据集的地址
        :param labels_dir: 数据集对应标签的地址
        """
        self.datas_dir = datas_dir
        self.labels_dir = labels_dir
        # 获取数据集和对应标签的文件名，各自存储在列表中
        self.images = os.listdir(self.datas_dir)
        self.labels = os.listdir(self.labels_dir)

    def __getitem__(self, idx):
        img_name = self.images[idx]
        label_name = self.labels[idx]
        img_path = os.path.join(self.datas_dir, img_name)
        label_path = os.path.join(self.labels_dir, label_name)
        img = plt.imread(img_path)
        with open(label_path) as fp:
            label = fp.read()
        return img, label

    def __len__(self):
        return len(self.images)
if __name__ == '__main__':
    # ------数据集1的地址------
    datas1_dir = "data/train/ants_image"
    labels1_dir = "data/train/ants_label"
    my_data1 = MyData(datas1_dir,labels1_dir)
    # ------数据集2的地址------
    datas2_dir = "data/train/bees_image"
    labels2_dir = "data/train/bees_label"
    my_data2 = MyData(datas2_dir, labels2_dir)
    # 整合数据，形成一个数据集
    my_data = my_data1+my_data2
    img, label = my_data[123]
    plt.imshow(img)
    plt.show()
    print(label)
    print(len(my_data))


```

