---
title: Python多进程
date: 2018-06-25 16:59:52
tags: Python
---
**参考自：**
> [廖雪峰的Python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431927781401bb47ccf187b24c3b955157bb12c5882d000)
> [董伟明的博客](http://www.dongwm.com/archives/%E4%BD%BF%E7%94%A8Python%E8%BF%9B%E8%A1%8C%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B-%E8%BF%9B%E7%A8%8B%E7%AF%87/)

**知识点：**
- Process
- Pool
- 进程间通信

 ---



#### **进程（Process）**
> 对于操作系统来说，一个任务就是一个进程（Process），比如打开一个word就是启动一个word进程。它不止干同一件事情，可以同时进行打字、打印等子任务操作，这些进程内的子任务就是线程（Thread）

- 单核CPU也可以执行多任务，操作系统轮流
- 让各个任务交替，Task1执行0.01秒，切换到Task2，Task2执行0.01秒，再切换到Task3，执行0.01秒........这样反复执行下去
- 真正的并行执行多任务只能在多核CPU上实现


**多进程（Multiprocessing）**

Multiprocessing模块提供了一个Process类来代表一个进程对象，下面代码演示了创建一个子进程，并等待其结束

- os.getpid()  获取父进程的ID
- 创建子进程时，首先创建Process类的实例，再传入一个函数和它的参数
- 用start()来启动子进程
- jion()  等待子进程结束后再继续运行下去，用于进程之间的同步


```python

# -*- coding:utf-8 -*-
import os
from multiprocessing import Process


def run_process(name):
    print('Run child process %s(%s)' % (name, os.getpid()))


if __name__ == '__main__':
    print('Run parent process %s' % os.getpid())
    # 创建子进程（创建Process类的实例p），传入一个函数和函数的参数
    p = Process(target=run_process, args=('test', ))
    print('Child process will start!')
    # 启动子进程
    p.start()
    # 等待子进程结束后再运行下去，用于进程间的同步
    p.join()
    print('Child process end!')
```

执行结果如下：

```python

Run parent process 9156
Child process will start!
Run child process test(1564)
Child process end!

Process finished with exit code 0

```

---

#### **Pool**
如果要启动大量的子进程，可以使用进程池（Pool）。


```python

# -*- coding:utf-8 -*-
import os
import time
import random
from multiprocessing import Pool


def long_time_task(name):
    print('Run task %s()%s' % (name, os.getpid()))
    start = time.time()
    # random.random()：随机生成0~1之间的浮点数
    time.sleep(random.random() * 3)
    end = time.time()
    print('Task %s run %0.2f seconds' % (name, end - start))


if __name__ == '__main__':
    print('Parent process %s' % os.getpid())
    # 创建进程池，大小是4，创建Pool类的实例p，不写子进程数量，默认开到CPU最大
    p = Pool(4)
    # 创建5个子进程，apply_async传入一个函数和它的参数
    for i in range(5):
        p.apply_async(long_time_task, args=(i, ))
    print('Waiting all subprocess done!')
    # join()之前必须先调用close()，调用close()后就不能再添加新的subprocess了
    p.close()
    # 等待所有子进程执行完毕
    p.join()
    print('All subprocess done!')

```

执行结果如下：

```python

Parent process 13524
Waiting all subprocess done!
Run task 0()14440
Run task 1()15016
Run task 2()8072
Run task 3()3268
Task 0 run 0.28 seconds
Run task 4()14440
Task 3 run 1.14 seconds
Task 2 run 1.54 seconds
Task 1 run 2.11 seconds
Task 4 run 2.82 seconds
All subprocess done!
```

Pool对象需要调用join()方法会等待所有subprocess执行完毕，调用join()方法之前必须要先调用close()方法，调用close()方法后就不能再添加新的subprocess了

我们看到输出是task0, 1, 2, 3执行了，而task4需要等待某个task执行完毕才能运行，这是因为我们设置了进程池大小为4，它只能同时执行4个子进程，如果不设置进程池大小，就是默认电脑的CPU最大核数


之前用Multiprocess写过多进程爬虫，抓取速度极快，数据量小的甚至可以做到秒抓

---

#### **进程间通信**

Process之间都需要通信，Multiprocessing提供了Queue（队列）Pipe（管道）来进行数据的交换

这里是Queue（队列）为例子，创建两个子进程，一个写数据，一个读数据。创建Queue的实例q，父进程创建队列，分别传递给write和read两个子进程


```python

# -*- coding:utf-8 -*-
import os
import time
import random
from multiprocessing import Process, Queue


def write(q):
    print('Process write to: %s' % os.getpid())
    for value in ['A', 'B', 'C']:
        print('Put %s to queue...' % value)
        # 调用put将value放置队列当中
        q.put(value)
        time.sleep(random.random())


def read(q):
    print('Proecss to read: %s' % os.getpid())
    while True:
        # 将数据从队列中取出
        value = q.get(True)
        print('Get %s from queue' % value)


if __name__ == '__main__':
    # 父进程创建队列，并传递给各个子进程
    q = Queue()
    # 创建write子进程
    pw = Process(target=write, args=(q, ))
    # 创建read子进程
    pr = Process(target=read, args=(q, ))
    # 开启write子进程
    pw.start()
    # 开启read子进程
    pr.start()
    # 等待write子进程结束
    pw.join()
    # read子进程是死循环，需要强制关闭
    pr.terminate()

```

**执行结果如下：**


```python
Process write to: 868
Put A to queue...
Proecss to read: 9844
Get A from queue
Put B to queue...
Get B from queue
Put C to queue...
Get C from queue

Process finished with exit code 0

```

---

董伟明老师写的多进程教程明显难度要高于廖老师的，用了很多Python的语法糖，需要额外去理解闭包、装饰器、递归，但廖老师的更容易理解



欢迎访问[Treehl的博客](https://family-treesy.github.io/)
[Github](https://github.com/Family-TreeSY/Python-Study-Notes)

