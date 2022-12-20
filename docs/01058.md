# 快速发布-订阅 python 实现:线程(二)

> 原文：<https://dev.to/mandrewcito/fast-pub-sub-python-implementation-threading-ii-1khp>

继续改进我们简单的酒馆。现在我们将在每个下标上触发一个线程。

### 实现

```
class ThreadedEventChannel(EventChannel):
    def __init__(self):
        super(ThreadedEventChannel, self).__init__()

    def publish(self, event, *args, **kwargs):
        threads = []
        if event in self.subscribers.keys():
            for callback in self.subscribers[event]:
                threads.append(threading.Thread(
                  target=callback,
                  args=args,
                  kwargs=kwargs
                ))

            for th in threads:
                th.start()

            for th in threads:
                th.join() 
```

Enter fullscreen mode Exit fullscreen mode

这个类将触发所有订阅的线程，并等待它们都继续执行

### 使发布者无阻塞

```
class ThreadedEventChannel(EventChannel):
    def __init__(self, blocking=True):
        self.blocking = blocking
        super(ThreadedEventChannel, self).__init__()

    def publish(self, event, *args, **kwargs):
        threads = []
        if event in self.subscribers.keys():
            for callback in self.subscribers[event]:
                threads.append(threading.Thread(
                  target=callback,
                  args=args,
                  kwargs=kwargs
                ))
            for th in threads:
                th.start()

            if self.blocking:
                for th in threads:
                    th.join() 
```

Enter fullscreen mode Exit fullscreen mode

这个类将触发所有订阅的线程，不等待它们继续执行

### 这种方法的优点

我们并行调用将提高我们的性能，例如，如果一个事件触发 2 次长时间的回调，我们只等待最长时间的回调。

```
import time
from event_channel.event_channel import EventChannel
from event_channel.threaded_event_channel import ThreadedEventChannel

non_thread = EventChannel()
threaded = ThreadedEventChannel()
non_blocking_threaded = ThreadedEventChannel(blocking=False)

non_thread.subscribe("myevent", time.sleep)
non_thread.subscribe("myevent", time.sleep)
start = time.time()
non_thread.publish("myevent", 3)
end = time.time()
print("non threaded function elapsed time {0}".format(end - start))
#non threaded function elapsed time 6.0080871582 threaded.subscribe("myevent", time.sleep)
threaded.subscribe("myevent", time.sleep)
start = time.time()
threaded.publish("myevent", 3)
end = time.time()
print("threaded function elapsed time {0}".format(end - start))
# threaded function elapsed time 3.00581121445 
non_blocking_threaded.subscribe("myevent", time.sleep)
non_blocking_threaded.subscribe("myevent", time.sleep)
start = time.time()
non_blocking_threaded.publish("myevent", 3)
end = time.time()
print("threaded function non blocking elapsed time {0}".format(end - start))
# threaded function non blocking elapsed time 0.00333380699158 
```

Enter fullscreen mode Exit fullscreen mode

### 链接

*   [黑桃](https://pypi.org/project/event-channel/)
*   [Github](https://github.com/mandrewcito/event_channel)

你会在哪些方面有所改进？。感谢您的阅读，并在:D 下面写下您的任何想法