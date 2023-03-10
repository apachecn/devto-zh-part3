# WRK:现代 HTTP 基准测试工具

> 原文：<https://dev.to/lyfolos/wrk-a-modern-http-benchmarking-tool-2a8e>

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ wg ](https://github.com/wg) / [ wrk](https://github.com/wg/wrk)

### 现代 HTTP 基准测试工具

<article class="markdown-body entry-content container-lg" itemprop="text">

# 一个 HTTP 基准测试工具

wrk 是一个现代的 HTTP 基准测试工具，当在单个多核 CPU 上运行时，它能够产生巨大的负载。它将多线程设计与 epoll 和 kqueue 等可扩展事件通知系统相结合。

可选的 LuaJIT 脚本可以执行 HTTP 请求生成、响应处理和定制报告。脚本中提供了详细信息，几个示例位于[脚本/](https://raw.githubusercontent.com/wg/wrk/master/scripts/) 中。

## 基本用法

```
wrk -t12 -c400 -d30s http://127.0.0.1:8080/index.html 
```

这将运行一个基准测试 30 秒，使用 12 个线程，并保持 400 个 HTTP 连接打开。

输出:

```
Running 30s test @ http://127.0.0.1:8080/index.html
  12 threads and 400 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   635.91us    0.89ms  12.92ms   93.69%
    Req/Sec    56.20k     8.07k   62.00k    86.54%
  22464657 requests in 30.00s, 17.76GB read
Requests/sec: 748868.53
Transfer/sec:    606.33MB 
```

## 命令行选项

```
-c, --connections: total number of HTTP connections to keep open with
                   each thread handling N = connections/threads
-d, --duration:
```

…</article>

[View on GitHub](https://github.com/wg/wrk)

没什么好说的，简单！