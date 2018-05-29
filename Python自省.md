---
title: Python自省
date: 2018-05-28 22:06:21
tags: Python
---

> 参考自廖雪峰的Python教程

 **Python自省-------在运行时能够获得对象的类型**

- type()，判断对象类型
- dir()， 带参数时获得该对象的所有属性和方法；不带参数时，返回当前范围内的变量、方法和定义的类型列表
- isinstance()，判断对象是否是已知类型
- hasattr()，判断对象是否包含对应属性
- getattr()，获取对象属性
- setattr()， 设置对象属性



### **1、type()**

type()返回对应的class类型

```python
In [27]: type(123)
Out[27]: int

In [28]: type('123')
Out[28]: str

In [29]: type(None)
Out[29]: NoneType
```

---


### **2、dir()**

不带参数
```python
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']
```


带参数

```python
dir('ABC')
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']

```

这里看下len()

len()函数返回字符长度

```python
>>> len('ABC')
3
```


```python
在len()函数内部，它自动去调用该对象的__len__()方法


>>> 'ABC'.__len__()
3

```


看个函数


```python
In [34]: class Mydog(object):
    ...:     def __len__(self):
    ...:         return 100
    ...:

In [35]: mydog = Mydog()

In [36]: len(mydog)
Out[36]: 100

```

---


### **3、isinstance()**

判断a数据类型是不是list

```python
In [37]: a = [1, 2, 3]

In [38]: isinstance(a, list)
Out[38]: True
```

---


### **getattr、hasattr、setattr**获取对象的状态


```python
In [1]: class Myobject(object):
   ...:     def __init__(self):
   ...:         self.x = 9
   ...:     def power(self):
   ...:         return self.x * self.x
   ...:

In [2]: obj = Myobject()

In [3]: obj.power()
Out[3]: 81

In [4]: hasattr(obj, 'x') # 有属性x么？
Out[4]: True

In [5]: hasattr(obj, 'y') # 有属性y么？
Out[5]: False

In [6]: setattr(obj, 'y') # 设置属性必须有三个参数，少了值
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-6-3b0a0f22117d> in <module>()
----> 1 setattr(obj, 'y')

TypeError: setattr expected 3 arguments, got 2

In [7]: setattr(obj, 'y', 100) # 设置属性y的值为100

In [8]: hasattr(obj, 'y') # 有属性y么？
Out[8]: True

In [9]: getattr(obj, 'y') # 获取属性y！
Out[9]: 100

In [10]: obj.y # 获取属性y
Out[10]: 100

In [11]: obj.x # 获取属性x
Out[11]: 9

```

获取不存在的属性，会抛出AttributeError

```python
In [12]: getattr(obj, 'z')
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-12-95c3c0ec4e01> in <module>()
----> 1 getattr(obj, 'z')

AttributeError: 'Myobject' object has no attribute 'z'
```

可以传入一个默认参数，不存在就抛出默认参数


```python

In [13]: getattr(obj, 'z', 404)
Out[13]: 404
```





