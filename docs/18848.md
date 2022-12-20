# 分析:检查运行一个 Python 脚本需要多长时间

> 原文：<https://dev.to/flaviabastos/profiling-check-how-long-it-takes-to-run-a-python-script-35i6>

来自 [Python 的官方文档](https://docs.python.org/3/library/profile.html):“一个*配置文件*是一组统计数据，描述了程序的各个部分执行的频率和持续时间。”

Python 的标准库提供了同一个配置接口的两种实现: *cProfile* 和 *profile* 但是对于简短的脚本，我总是使用 cProfile，因为你可以像这样在运行时调用它(不需要导入！):

```
python3 -m cProfile foo.py 
```

输出为您提供了总的运行时间以及按调用类型的细分，这在您寻找优化脚本的方法时非常有用。

cProfile 示例输出:

```
 131 function calls in 0.001 seconds Ordered by: standard name ncalls tottime percall cumtime percall filename:lineno(function) 1 0.000 0.000 0.001 0.001 loops.py:4(<module>) 1 0.000 0.000 0.001 0.001 loops.py:4(multiple\_loops) 1 0.000 0.000 0.001 0.001 {built-in method builtins.exec} 58 0.001 0.000 0.001 0.000 {built-in method builtins.print} 1 0.000 0.000 0.000 0.000 {method 'disable' of '\_lsprof.Profiler' objects} 40 0.000 0.000 0.000 0.000 {method 'insert' of 'list' objects} 29 0.000 0.000 0.000 0.000 {method 'join' of 'str' objects} 
```

> 帖子[评测:检查运行一个 Python 脚本需要多长时间](https://wp.me/pa0b0y-1M)最初发表在 [flaviabastos.ca](https://flaviabastos.ca/)