# 多线程队列

> 原文：<https://dev.to/mandrewcito/multithread-queue-1oni>

这个实现详细描述了一个函数队列，它的执行依赖于线程数，如果线程数是 1，它就是一个执行队列。

## 实现

```
import queue
import threading

class Process(object):
    def _get_function(self):
        return self.__function

    def _set_function(self, value):
        if not callable(value):
            raise TypeError("function must be callable")
        self.__function = value
    __function = None
    function = property(_get_function, _set_function)

    def _get_params(self):
        return self.__params

    def _set_params(self, value):
        if type(value) is not list:
            raise TypeError("params must be a list of parameters")
        self.__params = value
    __params = None
    params = property(_get_params, _set_params)

    def __repr__(self):
        return str(self.params) + str(self.function)

class MultithreadQueue(object):
    def __init__(self, thread_number=10, callback_insert=None, callback_get=None):
        self._queue = queue.Queue()
        self.threads = []
        self._cb_insert = callback_insert
        self._cb_get = callback_get
        for i in range(thread_number):
            t = threading.Thread(target=self.run)
            t.start()
            self.threads.append(t)

    def append(self, function, params):
        p = Process()
        p.function = function
        p.params = params
        self._queue.put(p)

    def run(self):
        while True:
            element = self._queue.get(block=True)
            element.function(*element.params) 
```

Enter fullscreen mode Exit fullscreen mode

## 举例

```
queue = MultithreadQueue()
for i in range(1,10):
    queue.append(print,[1,2]) 
```

Enter fullscreen mode Exit fullscreen mode