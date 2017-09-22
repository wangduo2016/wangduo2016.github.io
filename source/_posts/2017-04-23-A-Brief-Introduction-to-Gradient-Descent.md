---
title: 梯度下降(gradient descent)算法简介
date: 2017-04-23 21:36:42
author: wangduo
cover: .jpg
tags: [gradient descent, Deep learning]
categories: Deep learning
---

![](http://ovu6dymqj.bkt.clouddn.com/20170423-post/1.png)

梯度下降法是一个最优化算法，通常也称为最速下降法。最速下降法是求解无约束优化问题最简单和最古老的方法之一，虽然现在已经不具有实用性，但是许多有效算法都是以它为基础进行改进和修正而得到的。最速下降法是用负梯度方向为搜索方向的，最速下降法越接近目标值，步长越小，前进越慢。

中文名 梯度下降
外文名 steepest descent (gradient descent)
用于 求解非线性方程组
类型 最优化算法

### 目录
1 简介
2 求解过程
3 例子
4 缺点

### 简介

梯度下降法(gradient descent)是一个最优化算法，通常也称为最速下降法。[1]

常用于机器学习和人工智能当中用来递归性地逼近最小偏差模型。

### 求解过程

顾名思义，梯度下降法的计算过程就是沿梯度下降的方向求解极小值（也可以沿梯度上升方向求解极大值）。

其迭代公式为 ![][1]  ,其中  ![][2] 代表梯度负方向，  ![][3] 表示梯度方向上的搜索步长。梯度方向我们可以通过对函数求导得到，步长的确定比较麻烦，太大了的话可能会发散，太小收敛速度又太慢。一般确定步长的方法是由线性搜索算法来确定，即把下一个点的坐标看做是a<sub>k+1</sub>的函数，然后求满足f(a<sub>k+1</sub>)的最小值的 即可。

因为一般情况下，梯度向量为0的话说明是到了一个极值点，此时梯度的幅值也为0.而采用梯度下降算法进行最优化求解时，算法迭代的终止条件是梯度向量的幅值接近0即可，可以设置个非常小的常数阈值。

[1]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/2.png
[2]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/3.png/
[3]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/4.png

### 例子
举一个非常简单的例子，如求函数 ![][1] 的最小值。

利用梯度下降的方法解题步骤如下：

1、求梯度， ![][2]

2、向梯度相反的方向移动 ![][3] ，如下

 ![][4] ，其中， ![][5] 为步长。如果步长足够小，则可以保证每一次迭代都在减小，但可能导致收敛太慢，如果步长太大，则不能保证每一次迭代都减少，也不能保证收敛。

3、循环迭代步骤2，直到 ![][6] 的值变化到使得 ![][7] 在两次迭代之间的差值足够小，比如0.00000001，也就是说，直到两次迭代计算出来的 ![][7] 基本没有变化，则说明此时 ![][7] 已经达到局部最小值了。

4、此时，输出 x ，这个 x 就是使得函数 f(x) 最小时的 x 的取值 。

[1]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/5.png
[2]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/6.png
[3]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/7.png
[4]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/8.png
[5]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/9.png
[6]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/10.png
[7]:http://ovu6dymqj.bkt.clouddn.com/20170423-post/11.png

MATLAB如下：

```
%% 最速下降法图示
% 设置步长为0.1，f_change为改变前后的y值变化，仅设置了一个退出条件。
syms x;f=x^2;
step=0.1;x=2;k=0;         %设置步长,初始值,迭代记录数
f_change=x^2;             %初始化差值
f_current=x^2;            %计算当前函数值
ezplot(@(x,f)f-x.^2)       %画出函数图像
axis([-2,2,-0.2,3])       %固定坐标轴
hold on
while f_change>0.000000001                %设置条件，两次计算的值之差小于某个数，跳出循环
    x=x-step*2*x;                         %-2*x为梯度反方向，step为步长，！最速下降法！
    f_change = f_current - x^2;           %计算两次函数值之差
    f_current = x^2 ;                     %重新计算当前的函数值
    plot(x,f_current,'ro','markersize',7) %标记当前的位置
    drawnow;pause(0.2);
    k=k+1;
end
hold off
fprintf('在迭代%d次后找到函数最小值为%e，对应的x值为%e\n',k,x^2,x)
```

梯度下降法处理一些复杂的非线性函数会出现问题，例如Rosenbrock函数：

其最小值在(x,y)=(1,1) 处，函数值为 f(x,y)=0。但是此函数具有狭窄弯曲的山谷，最小点 (x,y)=(1,1)就在这些山谷之中，并且谷底很平。优化过程是之字形的向极小值点靠近，速度非常缓慢。

![](http://ovu6dymqj.bkt.clouddn.com/20170423-post/14.jpg)

### 缺点
- 靠近极小值时收敛速度减慢。
- 直线搜索时可能会产生一些问题。
- 可能会“之字形”地下降。

### 参考资料
1. [维基百科]() ．维基百科[引用日期2013-05-23]
2. [百度百科](http://baike.baidu.com/item/梯度下降) ． http://baike.baidu.com/item/梯度下降
