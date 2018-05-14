---
title: MongoDB数据库学习
date: 2017-11-30 17:00:23
tags:
- PYTHON
- MongoDB
---
##### **1. 什么是MongoDB数据库？**
![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/UOPr3jkXyfGKr.VjNF02tCCUoFH7h7sxSE427VZ0z3Q!/b/dPMAAAAAAAAA&bo=IAMWAQAAAAARBwQ!&rf=viewer_4)
MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。
MongoDB中的一条记录就是一个文档，是一个数据结构，由字段和值对组成。MongoDB文档与JSON对象类似。字段的值有可能包括其它文档、数组以及文档数组。
![](http://www.mongoing.com/docs/_images/crud-annotated-document.png)

##### **2. 安装与环境**
[MongoDB下载地址](https://www.mongodb.com/download-center?jmp=nav#community)
Windows安装：可以参考http://www.runoob.com/mongodb/mongodb-window-install.html
Linux安装：可以参考http://www.runoob.com/mongodb/mongodb-linux-install.html
Mac安装：可以参考http://www.runoob.com/mongodb/mongodb-osx-install.html

##### **3. 创建数据库、集合&删除数据库、集合**
打开Mongo shell
> use test

这样就创建了一个叫**test**的数据库，但这个时候数据库还是空的，用下面命令查看
> show dbs

可以看到并没有**test**这个数据库（lianjia数据库是我之前创建的，大家忽略，哈哈！！）

> admin    0.000GB
   lianjia  0.000GB
   local    0.000GB
  
 在数据库中添加一个集合（**collection**）
 > db.createCollection('movies')
 > { "ok" : 1 }
 
 我们再执行**show dbs**，**test**数据库就显示出来啦！！
 > admin    0.000GB
	lianjia  0.000GB
	local    0.000GB
	test     0.000GB

先暂时不要**movies**这个集合，执行删除命令
> db.movies.drop()
> true

执行**show collections**命令就不会再出现集合啦

再来执行删除数据库的命令
> db.dropDatabase()

shell返回{ "dropped" : "test", "ok" : 1 }就删除啦！！，再确认下数据库

> show dbs

**test**数据库就没了
> admin    0.000GB
	lianjia  0.000GB
	local    0.000GB

##### **4. 插入文档**
先创建数据库和集合，别忘了噢！
```python
# 创建数据库
use test
# 创建集合
db.createCollection('movies')
# 查看数据库
show dbs
```

[肖申克的救赎](https://movie.douban.com/subject/1292052/)
![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/dK1GMZZjXdWg.9pszN81pZPSlW7zj6Bv6.ZF6bpHsK8!/b/dPMAAAAAAAAA&bo=ZAQ7AgAAAAADB3s!&rf=viewer_4)
我们以肖申克的救赎为例，把电影信息存储到MongoDB中，电影信息：
- 电影名字
- 导演
- 编剧
- 主演
- 类型
- 制片国家
- 语言
- 上映时间
- 片长
- 评分

插入数据
>   db.movies.insert(
... {
... title: '肖申克的救赎',
... directed_by: '弗兰克',
... actor: ['摩根.摩里曼', '罗宾斯'],
... type: '剧情',
... country: '美国',
... language: '英语',
... release_time: '1994-09-10',
... time: '142',
... score: 9.6
... }
... )


**insert操作**有几点需要注意：
- 数据是以key:value键值对形式组成，有点类似与JSON
-  如果一个key有多个value，value要用[]，现在只有一个value，到后面还要添加可以预先使用[]
-  一整个数据块要用花括号{}

shell对格式要求很高，中英文的逗号都一样，肉眼分辨不出来，大家注意格式，不然不断报错很浪费时间。。。。，看到**WriteResult({ "nInserted" : 1 })**，说明写入成功

查询数据
> db.movies.find().pretty()

返回的数据
> {
        "_id" : ObjectId("5a1ff3a6bff1b3cbd8e8f812"),
        "title" : "肖申克的救赎",
        "directed_by" : "弗兰克",
        "actor" : [
                "摩根.摩里曼",
                "罗宾斯"
        ],
        "type" : "剧情",
        "country" : "美国",
        "language" : "英语",
        "release_time" : "1994-09-10",
        "time" : "142",
        "score" : 9.6
}

这里使用了**pretty()**，它的作用是让美化数据返回，使用**db.movies.find()**
返回的数据就是一坨一坨的 - -！！！大家可以试一下！
> { "_id" : ObjectId("5a1fee21bff1b3cbd8e8f80f"), "title" : "肖申克的救赎", "directed_by" : "弗兰克", "actor" : [ "摩根.摩里曼", "罗宾斯" ], "type" : "剧情", "country" : "美国", "language" : "英语", "release_time" : "1994-09-10", "time" : "142", "score" : "9.6" }


同时插入多个数据
> db.movies.insert([
... {
... title: '肖申克的救赎',
... directed_by: '弗兰克',
... actor: ['摩根.摩里曼', '罗宾斯'],
... type: '剧情',
... country: '美国',
... language: '英语',
... release_time: '1994-09-10',
... time: '142',
... score: 9.6
... },
... {
... title: '霸王别姬',
... directed_by: '陈凯歌',
... actor: ['张国荣', '巩俐'],
... type: '剧情',
... country: '中国',
... language: '汉语普通法',
... release_time: '1993-01-01',
... time: '171',
... score: 9.5
... }
... ])

如果插入成功，就会返回
> BulkWriteResult({
        "writeErrors" : [ ],
        "writeConcernErrors" : [ ],
        "nInserted" : 2,
        "nUpserted" : 0,
        "nMatched" : 0,
        "nModified" : 0,
        "nRemoved" : 0,
        "upserted" : [ ]
})

多数据插入模式为：**db.collection.insert([{key1: 'value1'},{key2: 'value2'}]**


#### **5. 查询数据**
以特定条件查询
> db.movies.find({'language': '汉语普通法'}).pretty()

我们看下返回的数据
> {
        "_id" : ObjectId("5a1ff19bbff1b3cbd8e8f811"),
        "title" : "霸王别姬",
        "directed_by" : "陈凯歌",
        "actor" : [
                "张国荣",
                "巩俐"
        ],
        "type" : "剧情",
        "country" : "中国",
        "language" : "汉语普通法",
        "release_time" : "1993-01-01",
        "time" : "171",
        "score" : 9.5
}

**AND**条件查询
> db.movies.find({'title':'霸王别姬', 'language':'汉语普通法'}).pretty()

只有同时满足两个条件的电影才会被查找出来

**OR条件查询**
> db.movies.find({$or:[{'title':'霸王别姬'}, {'language':'英语'}]}).pretty()

这里面的括号略显复杂，写的时候要仔细点

**条件操作符**
大于操作符 - $gt，查找评分大于9的电影
> db.movies.find({'score':{$gt:9}}).pretty()

小于操作符 - $lt
> db.movies.find({'score':{$lt:10}}).pretty()

类似操作符还有，大于等于操作符 - $gte，小于等于操作符 - $lte，不等于操作符 - $ne
这里就不一一列举了，与上同理


**limit和skip**条件操作符
limit()方法接受一个数字参数，该参数指定从MongoDB中读取的记录条数
> db.movies.find().limit(1).pretty()

这样就返回一部电影信息
>  {
        "_id" : ObjectId("5a1feb04bff1b3cbd8e8f80e"),
        "title" : "肖申克的救赎",
        "directed_by" : "弗兰克",
        "actor" : "摩根.摩里曼",
        "type" : "剧情",
        "country" : "美国",
        "language" : "英语",
        "release_time" : "1994-09-10",
        "time" : "142",
        "score" : 9.6
}

skip()方法来跳过指定数量的数据，skip方法同样接受一个数字参数作为跳过的记录条数
> db.movies.find().skip(1).pretty()

#### **6. 删除文档**
 > db.movies.remove({'title': '霸王别姬'})
 
 删除title为霸王别姬的文档，再查询下还剩几条文档
> db.movies.find()

#### **7. 更新文档**
> db.movies.update({'title':'肖申克的救赎'},{$set:{'title':'MongoDB_Test'}})

把value改成了MongoDB_Test
> {
        "_id" : ObjectId("5a200300bff1b3cbd8e8f813"),
        "title" : "MongoDB_Test",
        "directed_by" : "弗兰克",
        "actor" : [
                "摩根.摩里曼",
                "罗宾斯"
        ],
        "type" : "剧情",
        "country" : "美国",
        "language" : "英语",
        "release_time" : "1994-09-10",
        "time" : "142",
        "score" : 9.6
}

**$set** 操作符表示更新文档中的某一个字段，而不是全部替换
{ $set: { <field1>: <value1>, ... } }

**修改多条相同的文档，只需要设置 multi 参数为 true**
> db.movies.update({'title':'肖申克的救赎'},{$set:{'title':'MongoDB_Test'}},{multi:true})

以上两个操作会把原有数据改掉，如果想在原有值的基础上增加一个值的话需要添加**$push**，比如在actor中添加一个新演员进去
> db.movies.update({'title':'霸王别姬'}.{$push:{'actor':'Treehl'}})

这样actor就有三个演员了
> actor" : [
                "张国荣",
                "巩俐",
                "Treehl"
        ]


#### **8. 排序和索引**
**sort()**方法

对数据进行排序，sort()方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而-1是用于降序排列
举个例子以score电影评分为例
> db.movies.find().sort({'score':1}).pretty()


**ensureIndex()**方法
索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。为文档中的一些key加上索引(index)可以加快搜索速度，比如给type电影类型加上索引，这样电影type这个字符串与数字形成了映射，搜索速度会快很多
> db.movies.ensureIndex({'type':1})

1:升序；-1：降序


#### **9. 聚合**
MongoDB中聚合(aggregate)主要用于处理数据(诸如统计平均值,求和等)，并返回计算后的数据结果

``` python
> db.movies.aggregate([{$group:{_id:'$title',num_movies:{$sum:1}}}])
```

返回
> { "_id" : "霸王别姬", "num_movies" : 1 }
{ "_id" : "MongoDB_Test", "num_movies" : 1 }

理解一下命令
``` python
$group: 将集合中的文档分组，可用于统计结果
$sum:计算总和
```
这是建议可以参考菜鸟教程的[聚合](http://www.runoob.com/mongodb/mongodb-aggregate.html)

#### **10. Python操作MongoDB**

``` python
from pymongo import MongoClient

# 链接数据库
client = MongoClient('localhost', 27017)
# 建立douban数据库
db = client.douban
# 建立movies集合
movies = db.movies

movie_name_list = []

movie_name = {
    'title': '肖申克的救赎',
    'description': '小有成就的青年银行家安迪',
    'year': '1991'
}
movie_name_list.append(movie_name)

# 使用for循环打印出数据
for item in movie_name_list:
    movies.insert(item)
    
# 查找数据
for item in movies.find():
    print(item)

```

返回

``` python
{'_id': ObjectId('5a20c4f3aa85c109e881c7c7'), 'title': '肖申克的救赎', 'description': '小有成就的青年银行家安迪', 'year': '1991'}
```

这是个简单的python操作MongoDB的例子，有兴趣的可以看下我的[Python爬取上海链家房源存入MongoDB数据库(https://family-treesy.github.io/2017/11/29/lianjia/)

相信大家看完一遍能对MongoDB有个最基本的认识啦！！！


参考链接[菜鸟教程](http://www.runoob.com/mongodb/mongodb-tutorial.html)

欢迎大家访问我的博客[Treehl的博客](https://family-treesy.github.io/)
[简书](http://www.jianshu.com/u/a01bc3ec4f88)
[GitHub](https://github.com/Family-TreeSY)
















