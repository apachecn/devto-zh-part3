# 第 16 天-使用 oneliner 的日志执行时间- 100 天 100 个 python 脚本

> 原文：<https://dev.to/ganesh/day-16-log-execution-time-with-oneliner-100-days-100-python-scripts-37nl>

## 第 16 天:执行 _ 时间 _ 日志 _ 装饰

这是一个 Python 中的 decorator 脚本，当你使用 [@log](https://dev.to/log) 作为函数的 decorator 时，它可以自动记录函数名和执行时间。

```
import logging
import functools,time
from os.path import expanduser,join

def log(fun):
    functools.wraps(fun)
    def wrapper(*args,**kwargs):
        logger=logging.getLogger("execution_time_log")
        if not logger.hasHandlers():
            logger.setLevel(logging.INFO)
            filehandler=logging.FileHandler(join(expanduser("~"),"execution_time.log"))
            formatter=logging.Formatter('%(asctime)s - %(function_name)s - %(exec_time)f')
            filehandler.setFormatter(formatter)
            logger.addHandler(filehandler)
        start_time=time.time()
        called_return=fun(*args,**kwargs)
        end_time=time.time()
        logger.info(None,extra={'exec_time':end_time-start_time,'function_name':fun.__name__})
        return called_return
    return wrapper

# Examples 
@log
def test1():
    time.sleep(10)

@log
def test2():
    time.sleep(20)
@log
def test3():
    time.sleep(15)

test1()
test2()
test3() 
```

Enter fullscreen mode Exit fullscreen mode

请访问我的 Git Repo 查看所有前一天的挑战。

[https://github.com/ganeshraja10/automated-python-scripts](https://github.com/ganeshraja10/automated-python-scripts)

### 输出

[![Output](img/2d1c87a4674f41419688ac7ef2f7ef47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U-h_Yb-O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/65g1vim3pefz3tx10lff.png)