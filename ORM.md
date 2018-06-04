---
title: 什么是 Django ORM？
date: 2018-06-04 18:30:07
tags: Django
---

### **ORM**


> ORM（Object Relational Mapping）：对象关系映射。它的作用是在关系型数据库和业务对象之间作一个映射，一个对象对应一张表，不用再操作SQL语句

这里以我之前写的项目为例子，这里有两张表 Tag    Category

Category有6列分别对应 
- name
- status
- is_nav
- author
- created_time
- id




```python
# 这里是Django  Model层代码


# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.db import models
from django.contrib.auth.models import User
from django.db.models import F

import markdown


class Category(models.Model):
    STATUS_ITEMS = (
        (1, "正常"),
        (2, "删除"),
    )
    name = models.CharField(max_length=50, verbose_name="名称")
    status = models.PositiveIntegerField(
        default=1, choices=STATUS_ITEMS, verbose_name="状态"
    )
    is_nav = models.BooleanField(default=False, verbose_name="是否为导航")
    author = models.ForeignKey(User, verbose_name="作者")
    created_time = models.DateTimeField(auto_now_add=True, verbose_name="创建时间")

    class Meta:
        verbose_name = verbose_name_plural = '分类'

    def __unicode__(self):
        return self.name


class Tag(models.Model):
    STATUS_ITEMS = (
        (1, "正常"),
        (2, "删除"),
    )
    name = models.CharField(max_length=50, verbose_name="名称")
    status = models.PositiveIntegerField(
        default=1, choices=STATUS_ITEMS, verbose_name="状态"
    )
    author = models.ForeignKey(User, verbose_name="作者")
    created_time = models.DateTimeField(auto_now_add=True, verbose_name="创建时间")

    class Meta:
        verbose_name = verbose_name_plural = "标签"

    def __unicode__(self):
        return self.name

```


进入MySQL


```python
# category的表

mysql> SELECT * FROM blog_category;
+----+--------+--------+--------+----------------------------+-----------+
| id | name   | status | is_nav | created_time               | author_id |
+----+--------+--------+--------+----------------------------+-----------+
|  1 | Django |      1 |      1 | 2018-04-27 06:32:58.097000 |         1 |
|  2 | Python |      1 |      1 | 2018-04-27 11:32:39.440000 |         1 |
|  3 | Spider |      1 |      1 | 2018-04-27 11:32:45.248000 |         1 |
+----+--------+--------+--------+----------------------------+-----------+
3 rows in set (0.00 sec)

```



```python
# Tag表

mysql> SELECT * FROM blog_tag;
+----+-------+--------+----------------------------+-----------+
| id | name  | status | created_time               | author_id |
+----+-------+--------+----------------------------+-----------+
|  1 | MySQL |      1 | 2018-04-27 06:33:11.871000 |         1 |
+----+-------+--------+----------------------------+-----------+
1 row in set (0.00 sec)
```


假如这里有Category的实例，创建一条新的category分类叫treehl，最后存入数据库

```python
category = Category()
category.name = 'treehl' 
category.save()

```

对应mysql
> INSERT INTO (category.name) VALUES('treehl')

这就是对象到sql的映射


欢迎访问[Treehl的博客](https://family-treesy.github.io/)
[Github](https://github.com/Family-TreeSY/Python-Study-Notes)
