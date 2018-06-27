---
title: Python多线程
date: 2018-06-26 10:29:21
tags: Python
---

参考自：
> [廖雪峰的Python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143192823818768cd506abbc94eb5916192364506fa5d000)
> [董伟明的博客](http://www.dongwm.com/archives/%E4%BD%BF%E7%94%A8Python%E8%BF%9B%E8%A1%8C%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B-%E7%BA%BF%E7%A8%8B%E7%AF%87/)

---

> 多任务可以由多进程完成，也可以由一个进程内的多线程完成

#### **Python多线程**

- 一个进程默认会启动一个线程，我们称作为主线程。主线程又可以启动多个新的线程
- threading模块有一个current_thread()方法表示返回当前线程实例
- 主线程实例名字叫MainThread
- 子线程在创建时指定，这里定义为LoopThread
- start()方法启动线程
- join()方法等待线程结束



```python
# -*- coding:utf-8 -*-
import time
import threading


def loop():
    """当前新线程
    current_thread(): 返回当前线程实例
    """
    print('thread %s is running...' % threading.current_thread().name)
    n = 0
    # 设置循环条件，大于5就停止
    while n < 5:
        n = n + 1
        print('thread %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
    print('thread %s ended.' % threading.current_thread().name)


# 进程启动默认会启动一个主线程
print('thread %s is running！！！' % threading.current_thread().name)
# 创建Thread实例，传入loop函数
t = threading.Thread(target=loop, name='LoopThread')
# 开启线程
t.start()
# 等待所有线程结束，用于线程间的同步
t.join()
print('thread %s ended' % threading.current_thread().name)

```

执行结果：

```python
thread MainThread is running！！！
thread LoopThread is running...
thread LoopThread >>> 1
thread LoopThread >>> 2
thread LoopThread >>> 3
thread LoopThread >>> 4
thread LoopThread >>> 5
thread LoopThread ended.
thread MainThread ended

```


--- 


#### **Lock**
在多线程中，所有变量是由所有线程共享的，因此任何变量都会被任何线程修改，所以线程之间数据共享最大的危险就是多个线程去修改一个变量，会把内容给改乱了


下面代码就没有加上锁，balance为0，开启两个线程，理论上运行结果应该都是0，但由于线程的调度是由操作系统决定的，只要循环次数够多就不会一直为0
```python
# -*- coding:utf-8 -*-
import threading


balance = 0


def change_it(n):
    global balance
    balance = balance + n
    balance = balance - n


def run_thread(n):
    for i in range(1000000):
        change_it(n)


t1 = threading.Thread(target=run_thread, args=(5, ))
t2 = threading.Thread(target=run_thread, args=(8, ))
t1.start()
t2.start()
t1.join()
t2.join()
print(balance)

```

看下上Lock后的代码

这里给change_it()上了锁，当一个线程执行chang_it()的时候，其他线程就不能同时执行change_it()，需要等到这个线程执行完毕，并且释放锁，其他线程可以执行change_it()。锁只有一个，无论有多少个线程，同一时刻只有一个线程会有这个锁，其他线程处于等待状态，这样修改就不会发生冲突

- 锁的好处：确保一段代码只有一个线程从头到尾执行完毕，不会发生冲突
- 锁的坏处：无法实现多线程的并发执行，包含锁的代码只能以单线程运行，效率会大打折扣
- 由于可以存在多个锁，不同的线程持有不同的锁，在试图获取对方锁时，可能会造成死锁，造成多个线程全部挂起，不能执行也无法结束，只能强制关闭


```python
# -*- coding:utf-8 -*-
import threading


balance = 0
lock = threading.Lock()


def change_it(n):
    global balance
    balance = balance + n
    balance = balance - n


def run_thread(n):
    for i in range(1000000):
        # 获取锁
        lock.acquire()
        try:
            change_it(n)
        finally:
            # 释放锁，获取所以后必须释放锁，不然其他线程会一直等待下去，成为死线程
            lock.release()


t1 = threading.Thread(target=run_thread, args=(5, ))
t2 = threading.Thread(target=run_thread, args=(8, ))
t1.start()
t2.start()
t1.join()
t2.join()
print(balance)

```


---


#### **GIL（Global Interperter Lock）全局解释锁**

Python的多线程代码并不能利用多核的优势，而是通过GIL来处理。对于计算型任务（CPU计算占主要的任务），使用多线程GIL就会使运行速度变慢，这里举个例子看下Gil的问题。执行结果，开启多线程后反而速度更慢

Python为了保证线程安全而采取的独立线程运行的限制，就是一个核在同一时间只能运行一个线程；对于IO密集型的任务，Python多线程就能起到作用，提高并发效率


```python

# -*- coding:utf-8 -*-
import time
import threading


def profile(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        func(*args, **kwargs)
        end = time.time()
        print('Cost: {}'.format(end - start))
    return wrapper


def fib(n):
    if n <= 2:
        return 1
    return fib(n - 1) + fib(n - 2)


@profile
def nothread():
    fib(35)


@profile
def hasthread():
    for i in range(2):
        t = threading.Thread(target=fib, args=(35, ))
        t.start()
    main_thread = threading.current_thread()
    for t in threading.enumerate():
        if t is main_thread:
            continue
        t.join()


nothread()
hasthread()

```

执行结果：


```python

Cost: 2.1560332775115967
Cost: 4.16700291633606

Process finished with exit code 0
```

---


#### **Semaphore（信号量）**

前面Lock小结里面就讲过所有变量都是由所有线程共享的，要防止多个线程同时改修一个变量，需要控制同时访问的数量。信号量的同步基于内部的计数器，每调用一次acquire()就减1，每调用一次release()就+1，acquire就被阻塞了


```python
# -*- coding:utf-8 -*-
import time
import random
from threading import Thread, Semaphore


sema = Semaphore(3)


def foo(tid):
    with sema:
        print('{} acquire sema'.format(tid))
        wt = random.random() * 2
        time.sleep(wt)
    print('{} release sema'.format(tid))


threads = []


for i in range(5):
    t = Thread(target=foo, args=(i, ))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

```

执行结果：

这里我们限制了同时访问资源的数量为3


```python
0 acquire sema
1 acquire sema
2 acquire sema
1 release sema
3 acquire sema
2 release sema
4 acquire sema
0 release sema
4 release sema
3 release sema

```



---


#### **Queue**

多线程的队列，这里以生产者/消费者的模式来做例子，生产者把生产出来的消息放入队列，消费者从这个队列中去执行对应的消息

首先这个进程创建队列，然后传递给两个线程


```python
# -*- coding:utf-8 -*-
import time
import threading
from random import random
from Queue import Queue

# 创建队列
q = Queue()


def double(n):
    return n * 2


def producer():
    while 1:
        wt = random()
        time.sleep(wt)
        q.put((double, wt))


def consumer():
    while 1:
        task, arg = q.get()
        print(arg, task(arg))
        q.task_done()

# 创建两个线程 producer, consumer
for target in(producer, consumer):
    t = threading.Thread(target=target)
    # 开启线程
    t.start()


```




欢迎访问[Treehl的博客](https://family-treesy.github.io/)
[Github](https://github.com/Family-TreeSY/Python-Study-Notes)