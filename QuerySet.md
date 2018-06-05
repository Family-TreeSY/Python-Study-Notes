---
title: Django的QuerySet
date: 2018-06-04 19:27:54
tags: Django
---

### **QuerySet**



- 懒加载
- 支持链式调用
- 不支持链式调用


**支持链式调用**

- all()
- filter()
- exclude()
- reverse()
- distinct()
- none()

**不支持链式调用**

- get()
- create()
- get_or_create()
- update_or_create()
- count()
- latest()
- earliest()
- first()
- last()
- exists()
- update()
- delete()
- bulk_create()
- in_bulk()




[Django常用API](https://docs.djangoproject.com/en/2.0/ref/models/querysets/)


QuerySet是Django中非常重要的概念，创建完model对象之后，django会自动对它关联一个Manager对象，这个对象是Model对数据库进行操作的接口，Manager对象默认名称为objects

以objects为数据接口，查询所有的category，Category.objects.all()返回一个queryset赋值给categories。

```python
In [11]: from blog.models import Category

In [12]: categories = Category.objects.all() # 没有调出数据

In [13]: categories # 调出数据
Out[13]: <QuerySet [<Category: Django>, <Category: Python>, <Category: Spider>, <Category: Web后端>, <Category: Code>]>
```

**queryset是懒加载的，写它的时候并没有从数据库中调出数据，只有使用数据时才会进入数据库查询，原因是queryset支持链式调用，如果每次都要进入数据查询，会损耗性能。**

这里举个例子

查询all后又给出条件查询状态等于1的category，filter对应sql就是where

```python

In [14]: categories = Category.objects.all().filter(status=1)

In [15]: categories
Out[15]: <QuerySet [<Category: Django>, <Category: Python>, <Category: Spider>, <Category: Web后端>, <Category: Code>]>
In [16]: print(categories.query)
SELECT "blog_category"."id", "blog_category"."name", "blog_category"."status", "blog_category"."is_nav", "blog_category"."author_id", "blog_category"."created_time" FROM "blog_category" WHERE "blog_category"."status" = 1

In [18]: categories = Category.objects.all().filter(status=1).filter(name__icontains="django")

In [19]: categories
Out[19]: <QuerySet [<Category: Django>]>

In [20]: print(categories.query)
SELECT "blog_category"."id", "blog_category"."name", "blog_category"."status", "blog_category"."is_nav", "blog_category"."author_id", "blog_category"."created_time" FROM "blog_category" WHERE ("blog_category"."status" = 1 AND "blog_category"."name" LIKE %django% ESCAPE '\')

```

#### **Q查询**

filter()对等sql查询语句AND操作，OR操作就对等Q()查询



> filter(Q(id=1) | Q(id=2))   等于 where id=1 or id=2
```python

n [7]: from django.db.models import Q

In [8]: cates = Category.objects.filter(Q(id=1) | Q(id=2))


# 等于sql操作

SELECT "blog_category"."id", "blog_category"."name", "blog_category"."status", "blog_category"."is_nav", "blog_category"."author_id", "blog_category"."created_time" FROM "blog_category" WHERE ("blog_category"."id" = 1 OR "blog_category"."id" = 2)

```


#### F()查询
F 查询主要用来处理表中字段之间的比较，例如查询 blog_category 表中 name=id 的记录。同时 F 查询还支持计算（加减乘除）

```python

In [19]: cates = Category.objects.filter(name=F('id'))

In [20]: print(cates.query)
SELECT "blog_category"."id", "blog_category"."name", "blog_category"."status", "blog_category"."is_nav", "blog_category"."author_id", "blog_category"."created_time" FROM "blog_category" WHERE "blog_category"."name" = ("blog_category"."id")
```




欢迎访问[Treehl的博客](https://family-treesy.github.io/)
[Github](https://github.com/Family-TreeSY/Python-Study-Notes)

