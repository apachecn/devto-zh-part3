# 一个小小的 python 日志装饰器

> 原文：<https://dev.to/mandrewcito/a-tiny-python-log-decorator-1o5m>

这篇文章只是我为创建一个日志装饰器而阅读的其他文章的汇编。

首先，我们需要正确地配置一个日志，一个很好的例子可以在这里找到

```
import logging
logger = logging.getLogger('decorator-log')
logger.setLevel(logging.DEBUG)

# create console handler and set level to debug ch = logging.StreamHandler()
ch.setLevel(logging.DEBUG)

# create formatter formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# add formatter to ch ch.setFormatter(formatter)

# add ch to logger logger.addHandler(ch) 
```

Enter fullscreen mode Exit fullscreen mode

一旦配置了日志实例，我们就用它来创建装饰器。

```
 import functools
class LogDecorator(object):
    def __init__(self):
        self.logger = logging.getLogger('decorator-log')

    def __call__(self, fn):
        @functools.wraps(fn)
        def decorated(*args, **kwargs):
            try:
                self.logger.debug("{0} - {1} - {2}".format(fn.__name__, args, kwargs))
                result = fn(*args, **kwargs)
                self.logger.debug(result)
                return result
            except Exception as ex:
                self.logger.debug("Exception {0}".format(ex))
                raise ex
            return result
        return decorated 
```

Enter fullscreen mode Exit fullscreen mode

快完成了，我们需要修饰一些功能来测试一下:

```
## Start example @LogDecorator()
def func(a, b, c):
    return a + b + c

@LogDecorator()
def crash(a,b):
    raise ValueError(str(a))

func(1,2,3)

try:
    crash(1,3)
except:
    pass 
```

Enter fullscreen mode Exit fullscreen mode

我们的输出将是:

```
2018-12-04 23:37:44,110 - decorator-log - DEBUG - func - (1, 2, 3) - {}
2018-12-04 23:37:44,110 - decorator-log - DEBUG - 6
2018-12-04 23:37:44,110 - decorator-log - DEBUG - crash - (1, 3) - {}
2018-12-04 23:37:44,110 - decorator-log - DEBUG - Exception 1 
```

Enter fullscreen mode Exit fullscreen mode