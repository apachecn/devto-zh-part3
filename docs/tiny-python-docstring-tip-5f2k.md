# 小 python docstring 提示

> 原文：<https://dev.to/tadaboody/tiny-python-docstring-tip-5f2k>

当使用`abc.ABC`元类在 python 中定义接口时，有时有如此多的空方法等待被填充会变得相当烦人。

```
import abc
class Requester(abc.ABC):
    @abc.abstractmethod
    def get(self, endpoint:str) -> Response:
        """Sends a GET request."""
        pass

    @abc.abstractmethod
    def post(self, endpoint:str, params:dict) -> Response:
        """Makes a POST request."""
        pass 
```

Enter fullscreen mode Exit fullscreen mode

所有这些对`pass`的使用对我来说总是很难看，幸运的是有一个解决方案！

因为 docstrings 只是函数开始部分的一个字符串表达式——您可以放手！

```
import abc
class Requester(abc.ABC):
    @abc.abstractmethod
    def get(self, url:str, url_params:dict) -> Response:
        """Sends a GET request."""

    @abc.abstractmethod
    def post(self, url:str, url_params:dict) -> Response:
        """Makes a POST request.""" 
```

Enter fullscreen mode Exit fullscreen mode

这不是更好吗？