# 面向对象编程，入门

> 原文：<https://dev.to/madhadron/object-oriented-programming-part-1-1ehj>

当你今天学习编程时，你会遇到一种叫做“面向对象编程”的东西每个人都在争论什么是面向对象，什么不是。艾伦·凯发明了这个术语，人们甚至和他争论。

汤姆·德马科的《最后期限:一部关于项目管理的小说》中有一句相关的台词:“当我在一个规范中遇到任何不清楚的东西时，我会四处寻找冲突。我总能找到。”如果这么长的时间有这么多的争论，那意味着确实有不同的实践都试图共享面向对象编程这个名字。没有一个是错的。它们都是由它们特定的需求和条件形成的，但是对于一个局外人或只生活在其中一种传统中的人来说，整个泥沼是令人困惑的。

让我们浏览一下这些不同的传统。我将从他们都承认的开端开始:语言 Simula-67。 <sup id="fnref1">[1](#fn1)</sup>

Simula 采用了 ALGOL 60，这是当时命令式语言的通用语言，并将其用于模拟离散系统。作为一个非常简单的例子，考虑杂货店的三个收银台。人们以一定的速度到达结账处，然后排着最短的队伍去结账台。收银台会随机花费一些时间来处理每个顾客，此时顾客会从模拟中消失。我们的实体是三个收银台和顾客。

在 ALGOL 60 中，我们会写一个类似这样的程序(我使用 Python，因为它有我们需要的所有特性，而且比 ALGOL 60 更熟悉和可用):

```
import random
import queue

def argmin(xs):
    """Return the index of the minimum value in the list xs.

    If the minimum value appears multiple times, return the index
    of its first appearance. If the list is empty, return None.
    """
    if len(xs) == 0:
        return None
    elif len(xs) == 1:
        return 0
    else:
        idx = 0
        val = xs[0]
        for i, v in enumerate(xs):
            if v < val:
                idx = i
                val = v
        return idx

def peek_priority(q):
    """Return the priority of the first element in the priority queue q.

    This does not change the contents of q, so q.get() will get the
    same element whether we run this function first or not.
    """
    return q.queue[0][0]

customer_arrival_rate = 1.0  # customer/minute shortest_checkout_time = 1.0  # minutes longest_checkout_time = 5.0  # minutes 
# We will enqueue events with the time they
# are supposed to happen as their priority.
# We will enqueue (time, checkout line) for
# events of a customer finishing at the specified
# checkout line, and (time, -1) for a new customer
# arriving to join a line. event_queue = queue.PriorityQueue()

CUSTOMER_ARRIVES = -1

n_checkout_lines = 3
checkout_line_lengths = [0] * n_checkout_lines
next_customer_arrives_at = random.expovariate(customer_arrival_rate)

# We run the simulation for 100 events. We have to
# stop it somewhere or it runs forever. total_customers = 30
customers = 0
while True:
    if not event_queue.empty():
        # Customers might arrived in the time before
        # the next event in the queue. We peek that time and then
        # add customer arrival events to the queue until
        # we pass it.
        timestamp = peek_priority(event_queue)
        while timestamp > next_customer_arrives_at and customers < total_customers:
            event_queue.put((next_customer_arrives_at, CUSTOMER_ARRIVES))
            customers += 1
            next_customer_arrives_at += random.expovariate(customer_arrival_rate)

        timestamp, event = event_queue.get()
        if event == CUSTOMER_ARRIVES:
            shortest_line = argmin(checkout_line_lengths)
            checkout_line_lengths[shortest_line] += 1
            checkout_time = random.uniform(
                shortest_checkout_time, longest_checkout_time
            )
            event_queue.put((timestamp + checkout_time, shortest_line))
            print(
                f'{timestamp:6.2f} - ({" ".join(str(v) for v in checkout_line_lengths)}) '
                f"- Customer joined line {shortest_line} "
            )
        else:
            checkout_line_lengths[event] -= 1
            print(
                f'{timestamp:6.2f} - ({" ".join(str(v) for v in checkout_line_lengths)}) '
                f"- Line {event} finished with customer "
            )

    elif customers < total_customers:  # queue is empty, enqueue a customer
        event_queue.put((next_customer_arrives_at, CUSTOMER_ARRIVES))
        customers += 1
        next_customer_arrives_at += random.expovariate(customer_arrival_rate)
    else:
        break 
```

Simula 的设计是为了让我们可以分别编写每个实体的行为，而不是像我们在这里一样将所有的事情交织在一起。

它的模拟思想是让不同的实体发出并等待离散的事件。您希望能够将相同的事件发送到模拟中的不同实体，并让它们以不同的方式处理它们，因此它引入了实体类和子类的概念。子类可以有自己的行为来响应给定的事件。

事件处理本身是通过让您在类上定义协程来完成的。我们将使用 Python 的`asyncio`来模拟这一点，这很尴尬，因为它并不是一个模拟系统，但它让我们看到每个收银台和客户的来源都编写成自己的脚本，并允许我们像在 Simula-67 中一样进行交互。

```
import random
import asyncio
import time
import collections
import math

# This simulation runs in real time as opposed to
# as fast as the event loop can spin. For the sake
# of not waiting forever, we speed up the rates by
# scaling all rates uniformly. scale = 100.0

customer_arrival_rate = 1.0 * scale  # customer/minute shortest_checkout_time = 1.0 / scale  # minutes longest_checkout_time = 5.0 / scale  # minutes 

def argmin(xs):
    """Return the index of the minimum value in the list xs.

    If the minimum value appears multiple times, return the index
    of its first appearance. If the list is empty, return None.
    """
    if len(xs) == 0:
        return None
    elif len(xs) == 1:
        return 0
    else:
        idx = 0
        val = xs[0]
        for i, v in enumerate(xs):
            if v < val:
                idx = i
                val = v
        return idx

# Our first entity is the checkout station. It maintains a list
# of how long each customer queued will take to check out. class CheckoutStation:
    def __init__(self, i):
        self.line_length = 0
        self.i = i  # For reporting, we want to know which checkout station this is
        self.closed = False  # Bookkeeping so we can shut down gracefully 
    def queue_length(self):
        return self.line_length

    def join_queue(self):
        self.line_length += 1
        return self.line_length

    def close_station(self):
        self.closed = True

    async def run(self):
        while True:
            if self.line_length > 0:
                checkout_time = random.uniform(
                    shortest_checkout_time, longest_checkout_time
                )
                await asyncio.sleep(checkout_time)
                self.line_length -= 1
                report(
                    f"Queue {self.i} finished with customer in {checkout_time*scale:.2f} seconds."
                )
            elif self.line_length == 0 and self.closed:
                report(f"Closed queue {self.i}.")
                return
            else:
                await asyncio.sleep(0)

# And here is the entity that generates customers and has them
# queue. async def customer_source(stations):
    for i in range(30):
        time_until_customer = random.expovariate(customer_arrival_rate)
        await asyncio.sleep(time_until_customer)

        queue_lengths = [st.queue_length() for st in stations]
        shortest_queue = argmin(queue_lengths)
        n = stations[shortest_queue].join_queue()
        report(f"Customer joined queue {shortest_queue}, position {n} in line")
    report("All customers arrived.")
    for st in stations:
        st.close_station()

n_stations = 3
stations = [CheckoutStation(i) for i in range(n_stations)]

start_time = time.time()

def report(msg):
    queue_lengths = [st.queue_length() for st in stations]
    print(
        f'{(time.time() - start_time)*scale:6.2f} - {" ".join(str(v) for v in queue_lengths)} '
        f"- {msg}"
    )

loop = asyncio.get_event_loop()
futures = [st.run() for st in stations] + [customer_source(stations)]
loop.run_until_complete(asyncio.gather(*futures)) 
```

关键段落为(略有修改，去掉了终止模拟的边缘情况和报告代码):

```
while True:
    time_until_customer = random.expovariate(customer_arrival_rate)
    await asyncio.sleep(time_until_customer)

    queue_lengths = [st.queue_length() for st in stations]
    shortest_queue = argmin(queue_lengths)
    n = stations[shortest_queue].join_queue() 
```

和

```
while True:
    if self.line_length > 0:
        checkout_time = random.uniform(
            shortest_checkout_time, longest_checkout_time
        )
        await asyncio.sleep(checkout_time)
        self.line_length -= 1
    else:
        await asyncio.sleep(0) 
```

每个实体的行为都是独立而清晰的。与 ALGOL 60 风格的版本相比。对于这个简单的模拟，我们可以用任何一种方式来编写，但是考虑一下涉及成千上万个实体以多种方式交互的模拟。对于 Simula 方法，工作量很大，但很简单。对于 ALGOL 60 方法，这将是一场巨大的斗争。

这种对象建模实体和定义实体的类和子类的层次结构的思想在一些面向对象编程的传统中仍然存在。在另一些情况下，它遭到了拒绝，取而代之的是截然不同的方法。

Java、Python 或 Ruby 等现代语言中常见的类和方法的概念在 Simula-67 中都有。很多人在这里止步。

* * *

1.  67 代表 1967 年发布的 Simula 版本。在计算的大部分历史中，用年份来命名语言版本是非常典型的，例如 ALGOL 60、FORTRAN 77、C 99 和 C++ 14。 [↩](#fnref1)