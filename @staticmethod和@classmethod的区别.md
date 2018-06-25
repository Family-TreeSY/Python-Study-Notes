---
title: @classmethod和@classmethod的区别
date: 2018-06-25 21:53:20
tags: Python
---


#### **@staticmethod和@classmethod的区别**

- class 类
- @staticmethod
- @classmethod

---

首先创建一个类，如果想要调用Student类中的方法get_score()，就需要先创建类的实例，然后再用类的对象再去调用方法

```python
In [3]: class Student():
   ...:     def __init__(self, name, score):
   ...:         self.name = name
   ...:         self.score = score
   ...:     def get_score(self):
   ...:         print('%s get %s ' % (self.name, self.score))
   ...:
   ...:

In [4]: student = Student('treehl', 100)

In [5]: student.get_score()
treehl get 100

```


---


@staticmethod和@classmethod一个是静态方法，另一个是类的方法，两个装饰器的作用都可以使类不必再创建实例，直接用类来调用方法（类名.方法（））

它们使用上的区别
- @staticmethod不需要传递self，也不需要cls参数，就跟使用函数一样（类名.方法（）或类名.属性名）
- @classmethod也不需要像实例方法一样要传递self，但它需要cls参数（cls.类名（）或cls.方法（）或cls.属性（））


要理解这些，首先需要理解[类属性和实例属性的区别](https://github.com/Family-TreeSY/Python-Study-Notes/blob/master/Python%E5%AE%9E%E4%BE%8B%E5%8F%98%E9%87%8F%E5%92%8C%E7%B1%BB%E5%8F%98%E9%87%8F.md)

```python

In [6]: class A():
   ...:     bar = 1
   ...:     def foo(self):
   ...:         print 'foo'
   ...:     @staticmethod
   ...:     def static_foo():
   ...:         print 'static_foo'
   ...:         print A.bar
   ...:     @classmethod
   ...:     def class_foo(cls):
   ...:         print 'class_foo'
   ...:         print cls.bar
   ...:         cls().foo()
   ...:

In [7]: A.static_foo()
static_foo
1

In [8]: A.class_foo()
class_foo
1
foo
```

欢迎访问[Treehl的博客](https://family-treesy.github.io/)
[Github](https://github.com/Family-TreeSY/Python-Study-Notes)