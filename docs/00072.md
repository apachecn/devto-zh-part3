# 用装饰器记录 Python 执行时间

> 原文：<https://dev.to/teckert/logging-python-execution-time-with-a-decorator-56b1>

下面是一个用于记录 Python 函数执行时间的装饰器:

```
from time import time

def print_execution_time(function):
    def timed(*args, **kw):
        time_start = time()
        return_value = function(*args, **kw)
        time_end = time()

        execution_time = time_end - time_start

        arguments = ", ".join(
            [str(arg) for arg in args] + ["{}={}".format(k, kw[k]) for k in kw]
        )

        print(
            "{} ms {}({})".format(
                str(execution_time * 1000), function.__name__, arguments
            )
        )
        return return_value

    return timed

@print_execution_time
def repeat(number, n_repeats=30000):
    return [number for number in range(30000)]

repeat(9)
repeat(20, 40000)
repeat(1, n_repeats=4000) 
```

Enter fullscreen mode Exit fullscreen mode

这将打印以毫秒为单位的执行时间和运行的函数名及其参数:

```
1.001596450805664 ms repeat(9)
1.0008811950683594 ms repeat(20, 40000)
1.0001659393310547 ms repeat(1, n_repeats=4000) 
```

Enter fullscreen mode Exit fullscreen mode