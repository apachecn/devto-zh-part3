# 如何让 Python 代码与 3 行代码并发

> 原文：<https://dev.to/rhymes/how-to-make-python-code-concurrent-with-3-lines-of-code-2fpe>

我的灵感来自于 [@rpalo](https://dev.to/rpalo) 探索 Python 标准库中的宝石

[![rpalo](img/36441fefd071e9a78e983319aa0f9264.png)](/rpalo) [## 默认规则:永远不要再检查一个键是否存在！

### 瑞安帕洛 11 月 18 日 184 分钟阅读

#python #standardlibrary #defaultdict](/rpalo/defaultdicts-never-check-if-a-key-is-present-again-5hlp)

我决定通过一个例子来分享 Python 的标准库中我最喜欢的一个技巧。整个代码运行在 Python 3.2+上，没有外部包。

## 初始问题

假设您有一千个 URL 要处理/下载/检查，那么您需要发出尽可能多的 HTTP GET 调用并检索每个响应的主体。

这是一种方法:

```
import http.client
import socket

def get_it(url):
    try:
        # always set a timeout when you connect to an external server
        connection = http.client.HTTPSConnection(url, timeout=2)

        connection.request("GET", "/")

        response = connection.getresponse()

        return response.read()
    except socket.timeout:
        # in a real world scenario you would probably do stuff if the
        # socket goes into timeout
        pass

urls = [
    "www.google.com",
    "www.youtube.com",
    "www.wikipedia.org",
    "www.reddit.com",
    "www.httpbin.org"
] * 200

for url in urls:
    get_it(url) 
```

Enter fullscreen mode Exit fullscreen mode

*(我不会使用标准库作为 HTTP 客户端，但对于这篇文章来说，这没问题)*

如你所见，这里没有魔法。Python 迭代 1000 个 URL，并调用每个 URL。

我电脑上的这个东西占用了 2%的 CPU，大部分时间都在等待 I/O:

```
$ time python io_bound_serial.py
20.67s user 5.37s system 855.03s real 24292kB mem 
```

Enter fullscreen mode Exit fullscreen mode

它大约运行 14 分钟。我们可以做得更好。

## 给我看看窍门！

```
from concurrent.futures import ThreadPoolExecutor as PoolExecutor
import http.client
import socket

def get_it(url):
    try:
        # always set a timeout when you connect to an external server
        connection = http.client.HTTPSConnection(url, timeout=2)

        connection.request("GET", "/")

        response = connection.getresponse()

        return response.read()
    except socket.timeout:
        # in a real world scenario you would probably do stuff if the
        # socket goes into timeout
        pass

urls = [
    "www.google.com",
    "www.youtube.com",
    "www.wikipedia.org",
    "www.reddit.com",
    "www.httpbin.org"
] * 200

with PoolExecutor(max_workers=4) as executor:
    for _ in executor.map(get_it, urls):
        pass 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看发生了什么变化:

```
# import a new API to create a thread pool from concurrent.futures import ThreadPoolExecutor as PoolExecutor

# create a thread pool of 4 threads with PoolExecutor(max_workers=4) as executor:

    # distribute the 1000 URLs among 4 threads in the pool
    # _ is the body of each page that I'm ignoring right now
    for _ in executor.map(get_it, urls):
        pass 
```

Enter fullscreen mode Exit fullscreen mode

所以，3 行代码，我们把一个缓慢的串行任务变成了一个并发任务，用了不到 5 分钟:

```
$ time python io_bound_threads.py
21.40s user 6.10s system 294.07s real 31784kB mem 
```

Enter fullscreen mode Exit fullscreen mode

我们从 855.03 秒到 294.07 秒，增长了 2.9 倍！

## 等等，还有更多

这个新 API 的伟大之处在于你可以用
来代替

```
from concurrent.futures import ThreadPoolExecutor as PoolExecutor 
```

Enter fullscreen mode Exit fullscreen mode

用

```
from concurrent.futures import ProcessPoolExecutor as PoolExecutor 
```

Enter fullscreen mode Exit fullscreen mode

告诉 Python 使用进程而不是线程。出于好奇，我们来看看运行时间会发生什么:

```
$ time python io_bound_processes.py
22.19s user 6.03s system 270.28s real 23324kB mem 
```

Enter fullscreen mode Exit fullscreen mode

比线程版少 20 秒，差别不大。请记住，这些是不科学的实验，当这些脚本运行时，我正在使用计算机。

## 奖金内容

我的计算机有 4 个内核，让我们看看线程版本增加工作线程数量后会发生什么:

```
# 6 threads
20.48s user 5.19s system 155.92s real 35876kB mem
# 8 threads
23.48s user 5.55s system 178.29s real 40472kB mem
# 16 threads
23.77s user 5.44s system 119.69s real 58928kB mem
# 32 threads
21.88s user 4.81s system 119.26s real 96136kB mem 
```

Enter fullscreen mode Exit fullscreen mode

需要注意三点:RAM 占用明显增加，我们在 16 个线程左右遇到了瓶颈，在 16 个线程时，我们比串行版本快 7 倍以上。

如果你认不出`time`的输出是因为我给它起了这样的别名:

```
time='gtime -f '\''%Us user %Ss system %es real %MkB mem -- %C'\' 
```

Enter fullscreen mode Exit fullscreen mode

其中`gtime`由`brew install gnu-time`安装

## 结论

我认为 [ThreadPoolExecutor](https://docs.python.org/3.7/library/concurrent.futures.html#threadpoolexecutor) 和 [ProcessPoolExecutor](https://docs.python.org/3.7/library/concurrent.futures.html#processpoolexecutor) 是对 Python 标准库的超酷补充。你可以用“更老的”[线程](https://docs.python.org/3.7/library/threading.html)、[多处理](https://docs.python.org/3.7/library/multiprocessing.html)和 FIFO 队列做几乎所有的事情，但是这个 API 要好得多。