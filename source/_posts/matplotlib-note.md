---
title: matplotplit_note
author: ywfan
top: true
cover: false
toc: true
mathjax: false
date: 2019-12-21 17:53:50
img: /medias/banner/matplotlib.jpg
coverImg:
password:
summary:
	这个笔记记录了matplotlib的一些常规用法。
tags:
	- 数据可视化
categories:
	- python第三方库

typora-root-url: ..
---

# Matplotlib学习笔记

## 一. 基础知识

### 散点图

通过散点图探究数据之间的相关问题（正相关，负相关，不相关）

```python
import numpy as np
import matplotlib.pyplot as plt
N = 1000
x = np.random.randn(N)
y = np.random.randn(N)
plt.scatter(x, y, c="r", marker="o", alpha=0.5)
plt.show()
```

![散点图](/images/matplotplit-notes/scatter.png)

scatter(x, y, s, c, market)函数详解

| 参数   | 解释                                |
| ------ | ----------------------------------- |
| x      | array_like                          |
| y      | array_like                          |
| s      | 点的面积，                          |
| c      | 点的颜色                            |
| marker | 点的形状（在官方文档搜索"markers"） |
| alpha  | 点的透明度（0-1，重叠的点颜色加深） |

### 折线图

通过折线图观察数据的变化趋势（主要是数据随时间的变化图）

```python
import numpy as np
import matplotlib.pyplot as plt
x = np.linspace(-10, 10, 10)
y = x**2
plt.plot(x, y, "go--", linewidth=2, markersize=3)
plt.plot(x, y*2, color="green", linestyle="--", marker="o")
plt.show()
```

```python
# coding=utf-8
"""
数据随时间的变化
"""
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.dates as mdate
import datetime
fig = plt.figure()
ax = plt.gca()  # 获取当前的轴
start = datetime.datetime(2019, 12, 1)  # 开始日期
close = datetime.datetime(2019, 12, 21)  # 结束日期
delta = datetime.timedelta(days=1)  # 时间间隔
dates = mdate.drange(start, close, delta)  # 生成日期序列
values = np.random.rand(len(dates))   # 模拟数据变化
date_format=mdate.DateFormatter('%Y/%m/%d')
ax.xaxis.set_major_formatter(date_format)  # 设置主定位器为日期格式
fig.autofmt_xdate()  # 自动调整日期显示位置，避免日期之间相互重叠
plt.plot_date(dates, values, "b-")  # 绘图
plt.show()

```

![折线图](/images/matplotplit-notes/carve.png)

![时间折线图](/images/matplotplit-notes/carve_time.png)

plot([x], y, [fmt], [x2], y2, [fmt2],linewidth, markersize, label )

plot(x, y, color, marker, linestyle, linewidth, matkersize, label)

| 参数       | 解释                                                         |
| ---------- | ------------------------------------------------------------ |
| x          | array-like,*x* values are optional and default to `range(len(y)) |
| y          | array-like                                                   |
| fmt        | 定义基本的格式，如颜色，标记，线型                           |
| color      | 颜色                                                         |
| linewidth  | 线型宽度                                                     |
| linestyle  | 线型                                                         |
| markersize | 标记大小                                                     |
| label      | 线条名称，用于图例                                           |

| 返回值 | 解释                                                         |
| ------ | ------------------------------------------------------------ |
| 列表   | 列表中的元素是a <span style='color:red'>line</span> of `.Line2D` objects representing the plotted data |

### 条形图

```python
# coding=utf-8
import numpy as np
import matplotlib.pyplot as plt
N = 5
y = [20, 10, 30, 25, 15]
index = np.arange(5)
plt.bar(index, y, width=0.3, bottom=0, align="edge", edgecolor= "red",tick_label=["fisrt month", "second month", "third month", "forth month", "fifth month"])
plt.barh(index, y, height=0.3, edgecolor="red", tick_label=["fisrt month", "second month", "third month", "forth month", "fifth month"])
plt.show()

```

![纵向条形图](/images/matplotplit-notes/bar2.png)

![横向条形图](/images/matplotplit-notes/bar3.png)

bar(self, x, height, width=0.8, bottom=None, *, align="center", **kwargs)------纵向条形图

barh(y, width, height=0.8, left=None, *, align="center",  **kwargs)------横向条形图

| 参数       | 解释                                |
| ---------- | ----------------------------------- |
| x          | sequence of scalars                 |
| height     | scalar or sequence of scalars       |
| width      | 条形的宽度                          |
| bottom     | y坐标的基值                         |
| align      | {"center", "edge"},x坐标的位置      |
| color      | 柱状图表面颜色                      |
| edgecolor  | 柱状图边缘颜色                      |
| linewidth  | 线宽                                |
| tick_label | x坐标轴的记号，string or array-like |

```python
# coding=utf-8
import numpy as np
import matplotlib.pyplot as plt
N = 5
y1 = [20, 10, 30, 25, 15]
y2 = [15, 20, 10, 15, 25]
index = np.arange(5)
plt.bar(index, y1, width=0.3)
plt.bar(index+0.3, y2, width=0.3)
plt.show()
```

![横向双条形图](/images/matplotplit-notes/bar1.png)

```python
# coding=utf-8
import numpy as np
import matplotlib.pyplot as plt
N = 5
y1 = [20, 10, 30, 25, 15]
y2 = [15, 20, 10, 15, 25]
index = np.arange(5)
plt.bar(index, y1, width=0.3)
plt.bar(index, y2, width=0.3, bottom=y1)
plt.show()

```

![层叠条形图](/images/matplotplit-notes/bar4.png)

### 直方图

一系列高度不同的连续纵向条形组成

```python
# coding=utf-8
import numpy as np
import matplotlib.pyplot as plt
mu = 100
sigma = 20
x = mu + sigma*np.random.randn(2000)  # 生成正态分布（2000个数据）
n, bins, patch = plt.hist(x, bins=100, color="#DDA0DD", edgecolor="black", density=True)
y = ((1 / (np.sqrt(2 * np.pi) * sigma)) *np.exp(-0.5 * (1 / sigma * (bins - mu))**2))
print(y)
plt.plot(bins, y, "--")
plt.show()

```

![hist1](/images/matplotplit-notes/hist1.png)

hist(self, x, bins=None, range=None, density=None, weights=None, cumulative=False, bottom=None, histtype='bar', align='mid', orientation='vertical', rwidth=None, log=False, color=None, label=None, stacked=False, normed=None, **kwargs)

| 参数      | 解释                                                       |
| --------- | ---------------------------------------------------------- |
| x         | array or sequence of (n,) arrays,是一个数组或者数组序列    |
| bins      | 整数（条形的个数），或者是序列（指定每一条的范围）         |
| range     | 条形的上界和下界，范围之外的被舍弃                         |
| density   | true(纵轴显示频率/组距)，false(纵轴显示频数)，即归一化处理 |
| alpha     | 透明度                                                     |
| bottom    | 每一个柱子的基值                                           |
| histtype  | 柱子类型，默认是bar                                        |
| rwidth    | 柱子之间的距离，默认为零                                   |
| edgecolor | 柱子边界的颜色                                             |

| 返回值  | 解释                              |
| ------- | --------------------------------- |
| n       | 落入每一个盒子的样本数            |
| bins    | 每一个盒子边缘的值（一共n+1个值） |
| patched | 每一个柱子                        |

```python
# coding=utf-8
import numpy as np
import matplotlib.pyplot as plt
x1 = np.random.normal(0, 0.8, 1000)  # 均值为0，标准差为0.8的正太分布
x2 = np.random.normal(-2, 1, 1000)
x3 =  np.random.normal(3, 2, 1000)
kwargs = dict(histtype="stepfilled", alpha=0.5, density=True, bins=40)
plt.hist(x1, **kwargs)
plt.hist(x2, **kwargs)
plt.hist(x3, **kwargs)
plt.show()
```

![hist2](/images/matplotplit-notes/hist2.png)

### 饼图

```python
# coding=utf-8
import numpy as np
import matplotlib.pyplot as plt
def printf(val):
    return "{:.2f}%".format(val)


if __name__ == '__main__':
    labels = ["SH", "BI", "SZ", "GD"]
    fracs = [20, 10, 15, 5]
    patches, tests, auto_tests = plt.pie(fracs, explode=[0, 0, 0.1, 0], labels=labels, autopct=printf)
    plt.show()

```

![pie](/images/matplotplit-notes/pie.png)

pie(self, x, explode=None, labels=None, colors=None,autopct=None, pctdistance=0.6, shadow=False, labeldistance=1.1,startangle=None, radius=None, counterclock=True, wedgeprops=None, textprops=None, center=(0, 0),frame=False, rotatelabels=False)

| 参数    | 解释                                           |
| ------- | ---------------------------------------------- |
| x       | array-like,The wedge sizes                     |
| explode | array-like，数组中每一个值是偏移中心位置的半径 |
| label   | 每一个扇形的名称                               |
| colors  | array-like, 指定每一个扇形的颜色               |
| autopct | string, or function，标签化值                  |
| shadow  | 阴影                                           |

| 返回值    | 解释                                                         |
| --------- | ------------------------------------------------------------ |
| patches   | A sequence of :class:`matplotlib.patches.Wedge` instances    |
| texts     | A list of the label :class:`matplotlib.text.Text` instances. |
| autotexts | A list of :class:`~matplotlib.text.Text` instances for the numericlabels. This will only be returned if the parameter *autopct* isnot *None*. |
### 网格

```python
import numpy as np
import matplotlib.pyplot as plt
x=np.arange(0,10,1)
y=np.random.randn(len(x))
fig=plt.figure()
ax=fig.add_subplot(111)
l1,=plt.plot(x,x,label="y=x")
# l1.set_label("y=x")
l2,=plt.plot(x,x*x,label="y=x^2")
ax.legend(handles=[l1,l2],labels=["y=x","y=x^2"],loc=0)
ax.grid(b=True,which='major',color='g', linestyle="--",linewidth=1)
plt.show()
```

![grid](/images/matplotplit-notes/grid.png)

grid(self, b=None, which='major', axis='both', **kwargs)

| 参数      | 解释                             |
| --------- | -------------------------------- |
| b         | 布尔值或者None，决定是否使用网格 |
| which     | 更改的网格线                     |
| axis      | 应用网格线的轴                   |
| linestyle | 线型                             |
| color     | 线的颜色                         |
| linewidth | 线宽                             |

legend()
legend(labels)
legend(handles, labels)

| 参数    | 解释                 |
| ------- | -------------------- |
| handles | 线型序列             |
| labels  | 字符串序列           |
| loc     | 图例的位置（见官网） |
| ncol    | 图例按列排列的列数   |

## 二. matplotlib使用的三种方式

### pyplot

经典高层封装

+ 优点

  简单易用，**交互使用方便**，可以根据命令实时作图

+ 缺点

  底层定制能力不足

+ 设计代码

+ ```python
  # coding=utf-8
  import numpy as np
  import matplotlib.pyplot as plt
  x = np.arange(0,10,1)
  y = np.random.randn(len(x))
  line= plt.plot(x, y, label="A")
  plt.title("pyplot")
  plt.show()
  ```

  

### pylab

不推荐使用

### 面向对象的方式

+ 优点

  接近matplotlib基础和底层的方式，难度较大，定制能力强

+ 设计代码

  ```python
  from matplotlib import pyplot as plt
  import numpy as np
  x = np.arange(0, 10, 1)
  y = np.random.randn(len(x))
  fig = plt.figure()  # 生成一张图
  ax1 = fig.add_subplot(421)
  l, = plt.plot(x, y)
  ax1.set_title("first figure")
  ax2 = fig.add_subplot(422)
  h, = plt.plot(x, y)
  ax2.set_title("first figure")
  plt.show()
  ```



## 三. 绘制多图

+ matplotlib对象简介
  + FigureCanvas：画图
  + Figure：图
  + Axes：坐标轴
+ 一张图生成多张子图

```python
import matplotlib.pyplot as plt
import numpy as np

# 子图
x=np.arange(1,100)
fig = plt.figure()  # 生成一张图

ax1 = fig.add_subplot(221)  #总行数，总列数，子图所在位置
ax1.plot(x,x)

ax2 = fig.add_subplot(222)
ax2.plot(x,-x)

ax3 = fig.add_subplot(223)
ax3.plot(x,x*x)

ax4 = fig.add_subplot(224)
ax4.plot(x,np.log(x))

plt.show()
```

![add_subplot](/images/matplotplit-notes/add_subplot.png)

add_subplot(nrows, ncols, index, **kwargs)

| 参数  | 解释         |
| ----- | ------------ |
| nrows | 子图的总行数 |
| ncols | 子图的总列数 |
| index | 子图的位置   |
| label | 返回轴的标签 |

+ 生成多图

  ```python
  import matplotlib.pyplot as plt
  
  # 多图（同一时间生成多张图）
  fig1=plt.figure()
  ax1=fig1.add_subplot(111)
  ax1.plot([1,2,3],[3,2,1])
  
  fig2=plt.figure()
  ax2=fig2.add_subplot(111)
  ax2.plot([1,2,3],[1,2,3])
  
  plt.show()
  ```


## 4. 通用操作函数

### 调整坐标轴范围

```python
# 调整坐标轴范围
x = np.arange(-10,11,1)
plt.plot(x,x*x)
# axis参数：x轴最小坐标，x轴最大坐标，y轴最小坐标，y轴最大坐标
x_min, x_max, y_min, y_max = plt.axis()
# 调整坐标轴范围
# plt.axis([-5,5,20,60])
# 调整x轴：x轴最小坐标，x轴最大坐标
# .xlim([-5,5])
# 调整x轴的一端
# plt.xlim(xmin = -5)
# plt.xlim(xmax = 5)
# 同理y轴
# plt.ylim([0,60])
plt.show()
```

#### axis()

xmin, xmax, ymin, ymax = axis()

| 参数   | 解释                 |
| ------ | -------------------- |
| axis() | 获取轴的最大最小坐标 |

xmin, xmax, ymin, ymax = axis([xmin, xmax, ymin, ymax])

| 参数                | 解释                                               |
| ------------------- | -------------------------------------------------- |
| xmin,xmax,ymin,ymax | x轴最小坐标，x轴最大坐标，y轴最小坐标，y轴最大坐标 |

xmin, xmax, ymin, ymax = axis(option)

| 参数   | 解释                                                         |
| ------ | ------------------------------------------------------------ |
| option | 布尔值或者字符串，布尔值用于显示坐标轴与否，字符串用于设置坐标轴 |

#### plt方式的xlim()和ylim()

+ xlim()

  获取或设置当前轴的x轴

+ ylim()

  获取或设置当前轴的x轴

#### 面向对象方式的set_xlim()和set_ylim()

类似xlim()和ylim()

### 调整坐标轴刻度

```python
import numpy as np
import matplotlib.pyplot as plt
x=np.arange(0,10,1)
y=np.random.randn(len(x))
fig=plt.figure()
ax=fig.add_subplot(111)
l1,=plt.plot(x,x,label="y=x")
l2,=plt.plot(x,x*x,label="y=x^2")
# 调整坐标轴范围
ax.set_xlim((0,8))
ax.set_ylim(0,80)
# 将轴平均分份（紧密程度）
ax.locator_params(nbins=16)
ax.legend(handles=[l1,l2],labels=["y=x","y=x^2"],loc=0)
ax.grid(b=True,which='major',color='g', linestyle="--",linewidth=1)
plt.show()
```

![locator_params](/images/matplotplit-notes/locator_params.png)

locator_params(self, axis='both', tight=None, **kwargs)

| 参数  | 解释                       |
| ----- | -------------------------- |
| axis  | {'both', 'x', 'y'}操作的轴 |
| tight | bool or None, optional     |

### 添加坐标轴

```python
import numpy as np
import matplotlib.pyplot as plt

# 添加一个坐标轴，形成双坐标轴
x = np.arange(2,20,1)
y1 = x*x
y2 = np.log(x)
fig = plt.figure()
ax1 = fig.add_subplot(121)
ax1.plot(x, y1)
ax1.set_ylabel('Y1')
# 形成双坐标轴，x轴只有一个，y轴有两个
ax2 = ax1.twinx()
ax2.plot(x, y2 ,'r')
ax2.set_ylabel('Y2')
ax1.set_xlabel('Compare Y1 and Y2')
ax3 = fig.add_subplot(122)
ax3.plot(y1, x)
ax3.set_ylabel("Y")
ax3.set_xlabel("X1")
# 形成双坐标轴，y轴只有一个，y轴有两个
ax4 = ax3.twiny()
ax4.plot(y2, x, "r")
ax4.set_xlabel("X2")
plt.tight_layout()  # 调整子图间的布局
plt.show()
```

![twinxy](/images/matplotplit-notes/twinxy.png)

