---
title: Python实例变量和类变量
date: 2018-05-29 11:46:51
tags: Python
---
#### Python实例变量和类变量

类变量（类属性）：
> 类变量属于类所有，所有实例共享一个变量

实例变量（实例属性）
> 实例变量属于各个实例所有，相互不干扰

**1、实例属性**
给实例绑定属性的方法可以通过**self和实例变量**

```python
In [23]: class Cat(object):
    ...:     def __init__(self, name):
    ...:         self.name = name
    ...:
In [25]: c = Cat('Fat')
In [26]: c.name
Out[26]: 'Fat'

# 通过实例变量绑定属性

In [27]: c.age = 2

In [28]: c.age
Out[28]: 2

```


**2、类属性**

类属性归类所有，但所有实例都可以访问它


```python
In [31]: class Cat(object):
    ...:     name = 'Fat' # 类属性
    ...:

In [32]: c = Cat() # 生成实例

In [33]: c.name # 实例调用类属性
Out[33]: 'Fat'

In [34]: c
Out[34]: <__main__.Cat at 0x78ba2e8>

In [35]: Cat.name # 类调用类属性
Out[35]: 'Fat'

In [36]: c.name = 'thin' # 通过实例变量更改属性“名字为short”

In [37]: c.name # 由于实例属性优先级比类属性高，会屏蔽类属性name=thin，所以输出就是thin
Out[37]: 'thin'

In [38]: Cat.name  # 类调用类属性依旧是Fat
Out[38]: 'Fat'

In [39]: c.name
Out[39]: 'thin'

In [40]: del c.name # 删除实例属性

In [41]: c.name # 再次调用，由于实例属性被删除了，就又返回到了类属性
Out[41]: 'Fat'
```




欢迎访问[Treehl的博客](https://family-treesy.github.io/)
[Github](https://github.com/Family-TreeSY/Python-Study-Notes)