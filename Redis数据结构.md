---
title: Redis五种数据命令小结
date: 2018-4-1 20:08:08
tags:
- Redis
---

### **Redis数据结构**
结构类型
1. STRING
2. LIST
3. SET
4. HASH
5. ZSET（有序集合）



**STRING**
1. 字符串存储一下3种类型的值
字符串
整数
 浮点数
- GET（获取值）
- SET（设置值）
- DEL（删除值）

redis的自增和自减命令

- INCR incr key-name ----将键存储的值加上1
- DECR decr key-name----将键存储的值减去1
- INCRBY incrby key-name amount 将键存储的值加上整数amount
- DECRBY decrby key-name amount 将键存储的值减去整数amount
- INCRBYFLOAT incrby key-name amount 将键存储的值加上浮点数amount

redis处理子串和二进制位的命令
- append（append key-name value--------将值value追加到给定键key-name当前存储的值的末尾）
- getrange（getrange key-name value----------获取一个由偏移量start至偏移量end范围内所有字符串组成的子串，包括start和end在内）
- setrange（setrange key-name offset value-----将从start偏移量开始的子串设置为给定值）
- getbit（getbit key-name offset 将字符串看作是二进制位串，并返回位串中偏移量位offset的二进制位的值）
- setbit（setbit key-name offset value---------将字符串看作是二进制位串，并将位串中偏移量为offset的二进制位的值设置为value）
- bitcount
- bitop

打开redis-cli

设置键hello的值为world
> set hello world

获取键hello的值

> get hello

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/HWteMrVannnp4EOqWJ1WJevzQA1zvpmyWP7uYFsT4NY!/b/dDABAAAAAAAA&bo=LALjAAAAAAADB.8!&rf=viewer_4)

将键存储的值+1
> import redis
 conn = redis.Redis()
 conn.get('key')
conn.incr('key')
1
 conn.incr('key', 15)
16

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/HkAVFsObaaHGJcC.F2iDE60rQHyzcztXrTUJ1dJn8oo!/b/dDIBAAAAAAAA&bo=XwHCAAAAAAADB74!&rf=viewer_4)

将键存储的值减去5
>conn.decr('key', 5)
11

先设置一个键,key=13，然后再incr自增
> conn.set('key', '13')
True
conn.incr('key')
14

将value追加到给出的键当前存储的值的末尾
>  conn.append('new-string-key', 'hello')
5
 conn.append('new-string-key', 'world')
10

getrange获取从2开始到6结束范围内的所有子串

> conn.getrange('new-string-key', 2, 6)
b'llowo'


setrange 范围设置操作，在start索引位置替换字符串中已有的内容
- 10表示当前字符串总长度
> conn.setrange('new-string-key', 0, 'H')
> conn.setrange('new-string-key', 6, 'W')
10


增长字符串长度
>  conn.setrange('new-string-key', 11, ', how are you?')
25


setbit
setbit key-name offset value

> conn.setbit('another-key', 2, 1)
0
conn.setbit('another-key', 7, 1)
0
conn.get('another-key')
b'!'



**LIST**
- RPUSH（将给定值推入列表的右端）
- LRANGE（获取列表在给定范围上的所有值）
- LINDEX（获取列表在给定位置上的单个元素）
- LPOP（从列表的左端删除一个值，并返回被删除的值）
- LTRIM （对列表进行修剪，只保留从start偏移量到end偏移量范围内的元素）

阻塞式的列表弹出命令以及在列表之间移动元素的命令
- blpop 
- brpop
- rpoplpush
- brpoplpush


将键为list_key的值推入列表的右端

> rpush list_key treehl

获取指定范围内的值
- 使用0为范围的起始索引，-1为范围的结束索引，可以取出列表包含的所有元素
> lrange list_key 0 -1


从列表中获取单个元素
- 1 key后面的数字是索引，从0开始
> lindex list_key 1

从列表中删除元素
> lpop list_key

只保留起始索引到结束索引之间的元素

>  conn.lrange('list-key', 0, -1)
[b'a', b'b', b'c']


将一个元素从一个列表移动到另一个列表，并返回被移动的元素
- 1表示timeout

> conn.brpoplpush('list2', 'list', 1)
b'item6'




**SET**
- SADD（将给定元素添加到集合）
- SMEMBER（返回集合包含的所有元素）
- SISMEMBER（检查给定元素是否存在于集合中）
- SREM（如果给定元素存在于集合中，那么移除这个元素）
- SCARD （返回集合包含的元素）
- SRANDMEMBER （从集合里面随机地返回一个或多个元素）
- SPOP （随机地移除集合中的一个元素，并返回被移除的元素）
- SMOVE smove source-key dest-key item

用于组合和处理多个集合的Redis命令
- sdiff（差集）
- sdiffstore（差集，存储到dest-key中）
- sinter（交集）
- sinterstore （交集，存储到dest-key）
- sunion（并集）
- sunionstore（并集，存储到dest-key中）



将键set-key值为item,item2......添加到集合
> sadd set-key item
> sadd set-key item2
> sadd set-key item3


添加同样的值会添加不进去
> sadd set-key item2
(integer) 0


返回集合包含的所有元素

> smembers set-key

检查给出的元素是否存在集合中
- 1代表元素在集合中
>  sismember set-key item
(integer) 1

- 0代表所查找的元素不在集合中
>  sismember set-key item5
(integer) 0

移除元素
- 只有这个元素存在才会被移除

> srem set-key item2
(integer) 1


将一个元素从一个集合移动到另一个集合
- 将a元素从set-key中移动到set-key2中
> conn.smove('set-key', 'set-key2', 'a')

交集、并集、差集
![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/BNKB4kSo4wqOf1VBwCGgvn5wlVvfdkopcRnqBUT4128!/b/dEABAAAAAAAA&bo=5wbdAAAAAAADBx4!&rf=viewer_4)




**HASH**

- 可以存储多个键值对之间的映射
- 存储值既可以是字符串也可以是数字值

1. HSET 在散列里关联给出的键值对
2. HGET 获取指定散列键的值
3. HGETALL 获取散列包含的所有键值对

添加和删除键值对的HASH操作
1. HDEL 如果指定的键存在于散列中，那么删除这个键
2. HLEN 返回散列包含的键值对数量
3. HMGET 从散列里面获取一个或者多个键
4. HMSET 为散列里面的一个或多个键设置值 

redis散列的更高级特性
1. hexists (检查给定键是否存在于散列中)
2. hkeys （获取散列包含的所有键）
3. hvals（获取散列包含的所有值）
4. hgetall （获取散列包含的所有键值对）
5. hincrby（将键key保存的值加上整数increment）
6. hincrbyfloat（将键key保存的值加上浮点数）
关联给出的键值对

> hset hash-key shen 25
(integer) 1

> hset hash-key yang 25
(integer) 



获取所有键值对

> hgetall hash-key
1) "shen"
2) "25"
3) "yang"
4) "25"



获取指定键的值

> hget hash-key yang
"25"

将多个键值对添加到散列里面
> conn.hmset('hash-key', {'k1':'v1', 'k2': 'v2', 'k3': 'v3'})

获取多个键值对

>  conn.hmget('hash-key', ['k2', 'k3'])

获取键值对数量
> conn.hlen('hash-key')


获取散列包含的所有键

>  conn.hkeys('hash-key2')
[b'long', b'short']


检查指定的键是否存在于散列中
>  conn.hexists('hash-key2', 'num')
False

将键key保存的值加上整数increment

> conn.hincrby('hash-key2', 'num')
1
 conn.hexists('hash-key2', 'num')
True




**ZSET（有序集合）**

- 有序集合和hash一样都是存储键值对（key:value）
- 键被称为member，每一个都是独一无二的
- 值被称为分值（SCORE），且必须为浮点数


1. ZADD 将一个带有分值的成员添加到有序集合里面
2. ZRANGE 根据元素在有序排列中所处的位置，从有序集合里面获取多个元素
3. ZRANGEBYSCORE 获取有序集合在给定的分值范围内的所有元素
4. ZREM 如果指定的成员存在于有序集合中，那么移除这个成员
5. ZCARD 返回有序集合包含的成员数量
6. ZINCRBY 将member成员的分值加上increment
7. ZCOUNT 返回分值介于min和max之间的成员数量
8. ZRANK 返回成员member在有序集合中的排名
9. ZSCORE 返回成员member的分值


有序集合的范围型数据获取命令和范围型数据删除命令以及并集和交集
1. zrevrank（返回成员member所处的位置，成员按照分值从大到小排列）
2. zrevrange（返回给定范围内的成员，成员按照分值从大到小排列）
3. zrangebyscore（返回分值介于min和max之间的所有成员）
4. zrevrangebyscore（获取分值介于min和max之间的所有成员，并按照分值从大到小的顺序来返回它们）
5. zremrangebyrank（移除有序集合中排名介于start和stop之间的所有成员）
6. zremrangebyscore（移除有序集合中分值介于min和max之间的所有成员）
7. zinterstore（交集）
8. zunionstore（并集）



将几个带有分值的成员添加到有序集合中
> zadd zset-key 888 treehl
(integer) 1


获取有序集合中的元素
- 0 -1 表示获取所有元素
- withcores表示获取分值并按分值大小进行排序

>  zrange zset-key 0 -1 withscores
1) "hao"
2) "666"
3) "treehl"
4) "888"
5) "yang"
6) "999"

获取给定的分支范围内的元素

> zrangebyscore zset-key 0 889 withscores

- hao 999 就包含在内了


1) "hao"
2) "666"
3) "treehl"
4) "888"


删除成员

>  zrem zset-key hao
(integer) 1

------------------------------------------------------------

在python客户端输入有序集合应该写输入成员再输入分值与redis-cli上的输入是有区别的

> conn.zadd('zset-key', 'a', 3, 'b', 2, 'c', 1)
3

查看成员数量
> conn.zcard('zset-key')
3

自增操作
>  conn.zincrby('zset-key', 'c', 3)
4.0


返回成员分值

> conn.zscore('zset-key', 'b')
3.0


返回成员的排名

> conn.zrank('zset-key', 'c')
2



返回介于min 和 max之间的成员数量

>  conn.zcount('zset-key', 0, 3)
2

移除指定成员

>  conn.zrem('zset-key', 'b')
1


返回start和stop之间的成员
> conn.zrange('zset-key', 0, -1, withscores=True)
[(b'a', 3.0), (b'c', 4.0)]


-------------------

 对集合做交集
 >  conn.zinterstore('zset-i', ['zset-1', 'zset-2'])
2

 根据元素在有序排列中所处的位置，从有序集合里面获取多个元素
 > conn.zrange('zset-i', 0, -1, withscores=True)
[(b'c', 4.0), (b'b', 6.0)]


对集合做并集

> conn.zunionstore('zset-u', ['zset-1', 'zset-2'], aggregate='min')
4

> conn.zrange('zset-u', 0, -1, withscores=True)
[(b'd', 0.0), (b'a', 1.0), (b'c', 1.0), (b'b', 2.0)]


**发布与订阅**
发布与订阅的命令
- subscribe （订阅给定的一个或多个频道）
- unsubscribe（退订给定的一个或多个频道，如果执行时没有给定任何频道，那么退订所有频道）
- publish（像给定的频道发送消息）
- psubscribe （订阅与给定模式相匹配的所有频道）
- pubsubscribe（退订给定的模式，如果执行时没有给定任何模式，那么退订所有模式）


**其他命令**
排序-sort

>  conn.rpush('sort-input', 23, 15, 110, 17)
4
 conn.sort('sort-input')   #升序
[b'15', b'17', b'23', b'110']


降序

>  conn.sort('sort-input', alpha=True)
[b'110', b'15', b'17', b'23']


**键的过期和时间**
用于处理过期时间的Redis命令
- persist（移除键过期时间）
- ttl（查看给定键距离过期还有多少秒）
- expire（让给定键在指定的秒数之后过期）
- expireat（将给定的过期时间设置为给定的UNIX时间戳）
- pttl（查看给定键距离过期时间还有多少毫秒）
- pexpire（指定的键在指定的毫秒数之后过期）
- pexpireat（将一个毫秒级精度的UNIX时间戳设置为给定键的过期时间）

> conn.expire('key', 100); conn.ttl('key')
True
100


[GitHub](https://github.com/Family-TreeSY)
欢迎访问博客[Treehl的博客](https://github.com/Family-TreeSY)