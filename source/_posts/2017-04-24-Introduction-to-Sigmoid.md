---
title: Sigmoid函数简介
date: 2017-04-24 21:23:21
author: wangduo
cover: .jpg
tags: [Sigmoid, Deep learning]
categories: Deep learning
---

Sigmoid函数是一个在生物学中常见的S型的函数，也称为S型生长曲线。[1]

![](http://ovu6dymqj.bkt.clouddn.com/20170424-post/1.jpg)

中文名 Sigmoid函数 
外文名 Sigmoid function 
别名 S型生长曲线

Sigmoid函数由下列公式定义：

![](http://ovu6dymqj.bkt.clouddn.com/20170424-post/2.png)

其对x的导数可以用自身表示：

![](http://ovu6dymqj.bkt.clouddn.com/20170424-post/3.png)

前16个Sigmoid函数的数值为：

![](http://ovu6dymqj.bkt.clouddn.com/20170424-post/4.jpg)

Sigmoid函数的图形如S曲线：

![](http://ovu6dymqj.bkt.clouddn.com/20170424-post/5.png)

Sigmoid函数的级数表示：

![](http://ovu6dymqj.bkt.clouddn.com/20170424-post/6.png)

在信息科学中，由于其单增以及反函数单增等性质，Sigmoid函数常被用作神经网络的阈值函数，将变量映射到0,1之间。

参考资料：

1. Han, Jun; Morag, Claudio ．The influence of the sigmoid function parameters on the speed of backpropagation learning：From Natural to Artificial Neural Computation.，1995： 195–201

2. http://baike.baidu.com/item/Sigmoid函数
