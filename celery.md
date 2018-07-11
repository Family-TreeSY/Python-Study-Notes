---
title: 初探Celery
date: 2018-07-11 14:58:06
tags: Celery Python
---

### **初探Celery**

> **参考自董伟明的Python Web开发实战**



了解Celery前，可以先了解下[任务与消息队列](https://zhuanlan.zhihu.com/p/37648767)

> Celery是一个专注于实时处理和任务调度的分布式任务队列

使用Celery常见场景：

- Web应用：当用户触发的一个操作需要较长时间才能执行完成时，可以把它作为任务交给Celery去异步执行，执行完再返回给用户。所谓异步就是要执行耗时的IO操作时，它只发出指令，不需要等待结果，然后去执行其他代码了；一段时间后，返回结果后再去处理。
- 定时任务：
- 其他可以异步执行的任务



### **Celery的架构**

- Celery Beat：任务调度器，Beat进程会读取配置文件的内容，周期性地将配置中到期需要执行的任务发送给消息队列
- Celery Worker：执行任务的消费者，通常会在多台服务器运行多个消费者来提高执行效率
- Broker：消息代理，或消息中间件，接受任务生产者发送过来的任务消息，存进队列再按序分发给任务消费方（通常是消息队列或者数据库）
- Producer：调用了Celery提供的API、函数或者装饰器而产生任务并交给任务队列处理的都是任务生产者
- Result Backend：任务处理完后保存状态信息和结果，以供查询。Celery默认支持Redis、RabbitMQ、MongoDB、Django ORM

架构图：

![](https://pic1.zhimg.com/80/bed316c5eeffaea1e09eff340be15361_hd.jpg)



### **选择消息代理**

Celery目前支持RabbitMQ、Redis、MongoDB、Beanstalk等作为消息代理，但适用于生产环境的只有RabbitMQ和Redis



### **Celery序列化**

- pickle
- json
- yaml
- magpack



### **安装配置Celery**

- 选择Redis作为消息代理
- 选择Msgpack做序列化
- 选择Redis做结果存储



### **小试牛刀**



首先用pipenv创建虚拟环境，附上昨天写的[Pipenv简单小结](https://github.com/Family-TreeSY/Python-Study-Notes/blob/master/Pipenv.md)



```python
(celery_env-_UvHbTzp) treehl@ssaw:~/celery_env$ tree
.
├── Pipfile
├── Pipfile.lock
└── proj
    ├── celeryconfig.py
    ├── celery.py
    ├── __init__.py
    └── tasks.py

1 directory, 6 files

```



celery.py

```python
# -*- coding:utf-8 -*-
from __future__ import absolute_import # 拒绝隐式引入，celery.py和包名字有冲突，使程序运行正常

from celery import Celery

# proj.tasks包含了proj/tasks.py这个文件
app = Celery('proj', include=['proj.tasks'])
# app.config_from_object加载配置
app.config_from_object('proj.celeryconfig')


if __name__ == '__main__':
    app.start()

```



存放任务函数的文件，tasks.py

tasks.py只有一个任务函数add，添加app.task来生效

```python
# -*- coding:utf-8 -*-
from __future__ import absolute_import

from proj.celery import app


@app.task
def add(x, y):
    return x + y

```



配置文件celeryconfig.py



```python
# -*- coding:utf-8 -*-
# 消息代理，这里使用redis
BROKEN_URL = 'redis://localhost:6379/0'
# 任务结果存储在redis
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
# 任务序列化和反序列化
CELERY_TASK_SERIALIZER = 'msgpack'
# 读取任务结果
CELERY_RESULT_SERIALIZER = 'json'
# 任务过期时间，设置为24小时
CELERY_TASK_RESULT_EXPIRES = 60*60*24
# 指定接受的内容类型
CELERY_ACCEPT_CONTENT = ['json', 'msgpack']
```



任务生产者tasks将任务交给消息中间件redis，存入队列，在按序发放给worker



> cd celery_env 项目名字，随便写

> celery -A proj worker -l info  

-A参数会自动寻找proj.celery这个模块



```python
(celery_env-_UvHbTzp) treehl@ssaw:~/celery_env$ celery -A proj worker -l info
 
 -------------- celery@ssaw v4.2.0 (windowlicker)
---- **** ----- 
--- * ***  * -- Linux-4.15.0-20-generic-x86_64-with-Ubuntu-18.04-bionic 2018-07-10 19:54:51
-- * - **** --- 
- ** ---------- [config]
- ** ---------- .> app:         proj:0x7f16777619d0
- ** ---------- .> transport:   amqp://guest:**@localhost:5672//
- ** ---------- .> results:     redis://localhost:6379/0
- *** --- * --- .> concurrency: 8 (prefork)
-- ******* ---- .> task events: OFF (enable -E to monitor tasks in this worker)
--- ***** ----- 
 -------------- [queues]
                .> celery           exchange=celery(direct) key=celery
                

[tasks]
  . proj.tasks.add

[2018-07-10 19:54:52,087: INFO/MainProcess] Connected to amqp://guest:**@127.0.0.1:5672//
[2018-07-10 19:54:52,096: INFO/MainProcess] mingle: searching for neighbors
[2018-07-10 19:54:53,113: INFO/MainProcess] mingle: all alone
[2018-07-10 19:54:53,143: INFO/MainProcess] celery@ssaw ready.

```



开启另一个终端，用Ipython调用add函数

```python
In [1]: from proj.tasks import add

In [2]: r = add.delay(1, 3)

In [3]: r
Out[3]: <AsyncResult: 09a5ae36-baf2-49d5-a40c-092ee1941825>

In [4]: r.result
Out[4]: 4

In [5]: r.status
Out[5]: u'SUCCESS'

In [6]: r.successful()
Out[6]: True

In [7]: r.backend
Out[7]: <celery.backends.redis.RedisBackend at 0x7fbd1aa6dcd0>

```



worker的终端上显示执行了任务

```python
[2018-07-10 20:03:22,038: INFO/ForkPoolWorker-7] Task proj.tasks.add[09a5ae36-baf2-49d5-a40c-092ee1941825] succeeded in 0.00298636799562s: 4
[2018-07-10 20:10:25,297: INFO/MainProcess] Received task: proj.tasks.add[699f81a4-4fae-4c18-901b-ecf87be1f909]  
[2018-07-10 20:10:25,324: INFO/ForkPoolWorker-2] Task proj.tasks.add[699f81a4-4fae-4c18-901b-ecf87be1f909] succeeded in 0.0243313319952s: 3

```



任务的结果都需要根据上面提到的task_id获得



```python
In [13]: task_id = '09a5ae36-baf2-49d5-a40c-092ee1941825'

In [14]: from celery.result import AsyncResult
    
In [16]: AsyncResult(task_id).get()
Out[16]: 4

```



### **指定队列**

Celery会使用默认名为celery的队列用来存放任务，**我们可以使用优先级不同的队列来确保高优先级的任务不需要等待就得到响应**

我们在proj同目录下创建projq目录，将proj中的代码复制进projq，再给celeryconfig.py添加如下配置：

```python
from kombu import Queue


# 定义任务队列
CELERY_QUEUES = (
    Queue('default', routing_key='task.#'),  # 路由键以task.开头的消息都进入default队列
    Queue('web_tasks', routing_key='web.#'),  # 路由键以web.开头的消息都进入web_tasks队列, '#'表示任意数量
)
# 默认交换机名字为tasks
CELERY_DEFAULT_EXCHANGE = 'tasks' # 交换机就像邮局，通过它做路由分发
# 默认交换的类型为topic
CELERY_DEFAULT_EXCHANGE_TYPE = 'topic'
# 默认的路由键是task.default，这个路由键符合上面的default队列
CELERY_DEFAULT_ROUTING_KEY = 'task.default'

# tasks.add的消息会进入web_tasks队列
CELERY_ROUTES = {
    'projq.tasks.add': {
        'queue': 'web_tasks',
        'routing_key': 'web.add', 
    }
}
```



现在用指定队列的方式来启动worker ，worker只会执行web_tasks的任务

- -A自动寻找projq.celery
- -Q指定web_tasks队列

> celery -A projq worker -Q web_tasks -l info



### **使用任务调度**

之前的列子都是由发布者触发的，这里使用**Celery的Beat进程自动生成任务**，还记得前面的Celery架构图么，它和生产这一样都是处于任务发布者的位置

基于proj目录下的源码，创建一个projb目录，对projb/celeryconfig.py添加以下配置：

```python
CELERYBEAT_SCHEDULE = {
    'add': {
        'task': 'projb.tasks.add',
        'schedule': timedelta(seconds=10),
        'args': (16, 16),
    }
}
```

timedelta表示两个时间的差值，tasks.add这个任务每隔10秒跑一次



Beat和Worker一起启动

> celery -B -A projb worker -l info



运行后可以看到每10秒都会运行一次tasks.add



### **任务绑定、记录日志、重试**

任务绑定、记录日志和重试是Celery中常用的三个高级属性，修改proj/tasks.py，添加div函数

**当使用bind=True后，函数的参数发生变化，多出了参数self，相当于把div变成了一个已绑定的方法，通过self可以获得任务的上下文**

异常ZeroDivisionError中 as e，e为异常的实例，出现异常后就抛出重试retry

!r：表示为字符串格式化方法，只能用于format，在%中使用会报错



```python
from celery.utils.log import get_task_logger


logger = get_task_logger(__name__)


@app.task(bind=True)
def div(self, x, y):
    logger.info(('Executing task id {0.id}, args:{0.args!r}'
                'kwargs: {0.kwargs!r}').format(self.request))
    try:
        result = x / y
    except ZeroDivisionError as e:
        raise self.retry(exc=e, countdown=5, max_retries=3)
    return result
```



先启动worker进程

> celery -A proj worker -l info



另开一个终端，进入ipython

```python
In [1]: from proj.tasks import div

In [2]: r = div.delay(2, 1)
```

可以看到worker端出现一下执行信息：

```python
[2018-07-11 12:57:23,673: INFO/ForkPoolWorker-1] proj.tasks.div[5cf9ec88-5a90-4fd9-b4a4-f9580f2110ea]: Executing task id 5cf9ec88-5a90-4fd9-b4a4-f9580f2110ea, args:[2, 1]kwargs: {}
```



换成造成异常的参数

```python
[4]: r = div.delay(2, 0)
```



worker端

```python
[2018-07-11 12:59:07,177: INFO/ForkPoolWorker-3] Task proj.tasks.div[c5ec9012-f7c9-44cf-83bd-f94507f6ddc4] retry: Retry in 5s: ZeroDivisionError('integer division or modulo by zero',)
```

每5条就执行一次，执行完三次以后抛出异常结束



