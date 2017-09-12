---
title: PARAMETER和ARGUMENT的区别
date: 2017-09-11 19:15:20
author: wangduo
cover: /assets/images/.jpg
tags: [PARAMETER, ARGUMENT, C++, Ubuntu]
categories: C++
---

在对一个函数写一个注释时，我在考虑到底该用parameter还是用argument来描述其参数呢。

根据网上一些资料，对parameter和argument的区别，做如下的简单说明。

1. parameter是指函数定义中参数，而argument指的是函数调用时的实际参数。

2. 简略描述为：parameter=形参(formal parameter)， argument=实参(actual parameter)。

3. 在不很严格的情况下，现在二者可以混用，一般用argument，而parameter则比较少用。

While defining method, variables passed in the method are called parameters.
当定义方法时，传递到方法中的变量称为参数.
While using those methods, values passed to those variables are called arguments.
当调用方法时，传给变量的值称为引数.（有时argument被翻译为“引数“）

一个C++的例子来说明二者的区别：

```
void func(int n, char * pc); //n and pc are parameters
template <class T> A {}; //T is a a parameter

int main()
{
  char c;
  char *p = &c;
  func(5, p); //5 and p are arguments
  A<long> a; //'long' is an argument
  A<char> another_a; //'char' is an argument
  return 0;
}
```

如下叙述，来自wikipedia:
[http://en.wikipedia.org/wiki/Parameter_%28computer_programming%29](http://en.wikipedia.org/wiki/Parameter_%28computer_programming%29)

Just as in standard mathematical usage, the argument is thus the actual value passed to a function, procedure, or routine (such as x in log x), whereas the parameter is a reference to that value inside the implementation of the function (log in this case). See the Parameters and arguments section for more information.

Parameters and arguments
————————————————
These two terms are sometimes loosely used interchangeably; in particular, “argument” is sometimes used in place of “parameter”. Nevertheless, there is a difference. Properly, parameters appear in procedure definitions; arguments appear in procedure calls.

A parameter is an intrinsic property of the procedure, included in its definition. For example, in many languages, a minimal procedure to add two supplied integers together and calculate the sum total would need two parameters, one for each expected integer. In general, a procedure may be defined with any number of parameters, or no parameters at all. If a procedure has parameters, the part of its definition that specifies the parameters is called its parameter list.

By contrast, the arguments are the values actually supplied to the procedure when it is called. Unlike the parameters, which form an unchanging part of the procedure’s definition, the arguments can, and often do, vary from call to call. Each time a procedure is called, the part of the procedure call that specifies the arguments is called the argument list.

Although parameters are also commonly referred to as arguments, arguments are more properly thought of as the actual values or references assigned to the parameter variables when the subroutine is called at run-time. When discussing code that is calling into a subroutine, any values or references passed into the subroutine are the arguments, and the place in the code where these values or references are given is the parameter list. When discussing the code inside the subroutine definition, the variables in the subroutine’s parameter list are the parameters, while the values of the parameters at runtime are the arguments.

Many programmers use parameter and argument interchangeably, depending on context to distinguish the meaning. The term formal parameter refers to the variable as found in the function definition (parameter), while actual parameter refers to the actual value passed (argument).

From: http://smilejay.com/2011/11/parameter_argument/
