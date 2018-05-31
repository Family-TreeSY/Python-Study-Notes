---
title: 闭包
date: 2018-05-30 21:24:50
tags: Python
---
#### **Python闭包**

> 参考自廖雪峰的Python教程


在理解闭包之前，我们需要先理解 **变量是可以指向函数的，同时函数也是变量**。
这里我们使用绝对值函数作为示例，我们把abs函数赋值给变量f，f也就有了abs函数的功能


```python
In [40]: abs(-10)
Out[40]: 10

In [41]: abs
Out[41]: <function abs>

In [42]: f = abs

In [43]: f(-10)
Out[43]: 10

In [44]: f
Out[44]: <function abs>

```

再写一个示例

```python

In [45]: def example(x):
    ...:     return x * x
    ...:

In [46]: f = example

In [47]: f
Out[47]: <function __main__.example>

In [48]: f(10)
Out[48]: 100
```


**函数也可以作为结果值返回**

这里以一个求和函数为例子


```python
In [1]: def add_sum(*args):
   ...:     x = 0
   ...:     for n in args:
   ...:         x = x + n
   ...:     return x
   ...:

In [2]: add_sum(1, 2, 3, 4, 5, 6)
Out[2]: 21
```


这里写个函数它不马上返回求和结果，而是返回求和函数！外部函数add_sum有内嵌函数sum，内嵌函数sum使用了外部函数add_sum的参数args，外部函数的返回值是内嵌函数sum，当我们调用add_sum的时候返回的不是求和结果而是求和函数，调用函数f()时候才是求和结果！！！这种就是闭包（Closure）

创建闭包必须满足以下三点：
- 必须有一个内嵌函数
- 内嵌函数必须引用外部函数的参数和变量
- 外部函数的返回值必须是内嵌函数


```python
In [4]: def add_sum(*args):
   ...:     def sum():
   ...:         x = 0
   ...:         for n in args:
   ...:             x = x + n
   ...:         return x
   ...:     return sum
   ...:

In [5]: add_sum(1, 2, 3, 4, 5, 6)
Out[5]: <function __main__.add_sum.<locals>.sum>

In [6]: f = add_sum(1, 2, 3, 4, 5, 6)

In [7]: f
Out[7]: <function __main__.add_sum.<locals>.sum>

In [8]: f()
Out[8]: 21
```


当我们调用add_sum时每次返回的都是一个新的函数，f1和f2的调用结果互不影响


```python
In [15]: f1 = add_sum(1, 2, 3, 4, 5, 6,)

In [16]: f2 = add_sum(1, 2, 3, 4, 5, 6)

In [17]: f1 == f2
Out[17]: False

In [18]: f1()
Out[18]: 21

In [19]: f2()
Out[19]: 21

```


欢迎访问[Treehl的博客](https://family-treesy.github.io/)
[Github](https://github.com/Family-TreeSY/Python-Study-Notes)