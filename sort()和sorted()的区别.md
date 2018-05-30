---
title: sort()和sorted()的区别
date: 2018-05-30 10:10:33
tags: Python
---

### **sort()和sorted()的区别**
上周面试问的一个问题，瞬间懵了！我想不就是排序么。。。。。非常简单的问题，居然没有说清楚！！！今天写个小结，加深下印象！！！！


**1、sort()**

sort()是python列表内置的方法，它会对列表进行永久性排序，无法恢复到原来的排序列表，看代码

```python
In [11]: l = [3, 1, 5, 2, 9, 'c', 'a']

In [12]: l.sort()

In [13]: l
Out[13]: [1, 2, 3, 5, 9, 'a', 'c']

```


也可以已反方向排序

```python
In [14]: l.sort(reverse=True)

In [15]: l
Out[15]: ['c', 'a', 9, 5, 3, 2, 1]
```


**2、sorted()函数**

sorted()函数对列表进行临时性排序，l列表被排序后改变了原列表顺序，再输入l，列表依旧是原来的样子

```python
In [20]:  l = [3, 1, 5, 2, 9, 'c', 'a']

In [21]: sorted(l)
Out[21]: [1, 2, 3, 5, 9, 'a', 'c']

In [22]: l
Out[22]: [3, 1, 5, 2, 9, 'c', 'a']
```



欢迎访问[Treehl的博客](https://family-treesy.github.io/)
[Github](https://github.com/Family-TreeSY/Python-Study-Notes)