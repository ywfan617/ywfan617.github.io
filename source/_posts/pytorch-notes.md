---
title: pytorch-notes
author: 范耀文
top: false
cover: false
toc: true
mathjax: false
date: 2020-02-26 15:31:51
img: /medias/banner/pytorch.jpg
coverImg:
password:
summary:
	- 这是pytorch学习笔记。
tags:
	- pytorch
categories:
	- 机器学习框架
---

## 张量简介

## Tensor与Variable

### Variable

variable是torch.autograd中的数据类型，主要用于封装Tensor，进行自动求导

+ data：被包装的Tensor
+ grad：data的梯度
+ grad_fn：创建Tensor的Function，是自动求导的关键
+ requires_grad：指示是否需要梯度
+ is_leaf：指示是否是叶子结点

### Tensor

+ dtype：张量的shujleixing
+ shape：张量的形状
+ device：张量所在的设备

+ data：被包装的Tensor
+ grad：data的梯度
+ grad_fn：创建Tensor的Function，是自动求导的关键
+ requires_grad：指示是否需要梯度
+ is_leaf：指示是否是叶子结点

### 张量的创建

#### 直接创建

`torch.tensor(data,dtype=None,device=None,requires_grad=Flase,pin_memory=False)`

```python
# ===============================  exmaple 1 ===============================
# 通过torch.tensor创建张量
#
# flag = True
flag = True
if flag:
    arr = np.ones((3, 3))
    print("ndarray的数据类型：", arr.dtype)
    t = torch.tensor(arr, device='cuda')
    # t = torch.tensor(arr)
    print(t)
```

```python
ndarray的数据类型： float64
tensor([[1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.]], device='cuda:0', dtype=torch.float64)

```

| 参数          | 解释                       |
| ------------- | -------------------------- |
| data          | 数据，可以是list，numpy    |
| dtype         | 数据类型，默认与data的一致 |
| device        | 所在设备，cuda/cpu         |
| requires_grad | 是否需要梯度               |
| pin_memory    | 是否存在锁页内存           |

#### 张量的创建

```python
# ===============================  exmaple 2.数据增强方法 ===============================
# 通过torch.from_numpy创建张量--两者共享内存
# flag = True
flag = True
if flag:
    arr = np.array([[1, 2, 3], [4, 5, 6]])
    t = torch.from_numpy(arr)
    print("numpy array: ", arr)
    print("tensor : ", t)

    print("\n修改arr")
    arr[0, 0] = 0
    print("numpy array: ", arr)
    print("tensor : ", t)

    print("\n修改tensor")
    t[0, 0] = -1
    print("numpy array: ", arr)
    print("tensor : ", t)
```

```python
numpy array:  [[1 2 3]
 [4 5 6]]
tensor :  tensor([[1, 2, 3],
        [4, 5, 6]], dtype=torch.int32)

修改arr
numpy array:  [[0 2 3]
 [4 5 6]]
tensor :  tensor([[0, 2, 3],
        [4, 5, 6]], dtype=torch.int32)

修改tensor
numpy array:  [[-1  2  3]
 [ 4  5  6]]
tensor :  tensor([[-1,  2,  3],
        [ 4,  5,  6]], dtype=torch.int32)
```

