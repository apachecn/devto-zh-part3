# Python:标准装饰及其使用

> 原文：<https://dev.to/yorodm/python-decoradores-estndar-y-su-uso-3fe>

Python 的*电池包括*在内，有几个装饰家来为我们的生活提供便利。以下是一些使用方法的简短说明。

## 装饰者

### 属性

最常用的一种。将一组方法转换成做性质函数的**描述符**。当我们要向类属性添加某种逻辑时很有用。

```
class C:
    def __init__ (self):
        self._x = None

    def getx(self):
        return self._x or request.get('http://mydata.com/x')

    def setx(self, value):
        self._x = value
        request.post('http://mydata.com/x', data=x)

    def delx(self):
        del self._x
        request.delete('http://mydata.com/x')

    x = property(getx, setx, delx, "x como proxy a servicio REST.") 
```

Enter fullscreen mode Exit fullscreen mode

### 静态方法

定义静态方法。他们既可以从班里调用，也可以从其中一个实例调用

```
class C:

    @staticmethod
    def calculo_externo(val, val2):
        return val + val2 
```

Enter fullscreen mode Exit fullscreen mode

请注意缺少初始隐式参数。

### classmethod

将常规方法转换为类方法。类方法将类作为隐式参数而不是实例之一接收。最常见的用法之一是创建其它方法来实例化类。

```
class C:

    def __init__ (self, x, y ,z):
        self.x = x
        self.y = y
        self.z = z

    @classmethod
    def from_dict(cls, data):
        return cls(data[0], data[1], data[3]) 
```

Enter fullscreen mode Exit fullscreen mode

### abc.abstractmethod

必须由子类强制实施的方法**。相当于*界面*或*抽象类*，你在其他语言中可以找到这么多。** 

```
import abc

class Animal:

    @abc.abstractmethod
    def sonido(self):
        pass

class Perro(Animal):

    def sonido(self):
        print("Ladrido")

class Vaca(Animal):

    def sonido(self):
        print("Mugido") 
```

Enter fullscreen mode Exit fullscreen mode

可与 *@property* 或 *staticmethod* 结合使用，创建抽象的静态属性或方法。

### functools . total _ ordering

允许我们创建支持比较运算符的类。我们只需提供 _ **eq** _ 方法和六种预定义顺序方法之一。标准胆库文档警告此装饰器可能会影响性能。

```
@total_ordering
class Student:
    def _is_valid_operand(self, other):
        return (hasattr(other, "lastname") and
                hasattr(other, "firstname"))
    def __eq__ (self, other):
        if not self._is_valid_operand(other):
            return NotImplemented
        return ((self.lastname.lower(), self.firstname.lower()) ==
                (other.lastname.lower(), other.firstname.lower()))
    def __lt__ (self, other):
        if not self._is_valid_operand(other):
            return NotImplemented
        return ((self.lastname.lower(), self.firstname.lower()) <
                (other.lastname.lower(), other.firstname.lower())) 
```

Enter fullscreen mode Exit fullscreen mode

### data class . data class

允许我们在只处理数据的类声明中省略 _**【init】**_ 和 _ **【压抑】_ 的声明。用途可扩展至子类** 

```
import dataclasses

@dataclasses.dataclass
class Point:
    x: float
    y: float

    def euclidian_distance(self, other: Point) -> float:
        return math.sqrt(
            (other.x - self.y)**2 + (other.y - self.y)**2
        )

@dataclasses.dataclass
class ThreeDPoint(Point):
    z: float

    def euclidian_distance(self,other: Point) -> float:
        dx = (other.x - self.y)**2
        dy = (other.y - self.y)**2
        dz = (other.z - self.z)**2
        return math.sqrt(
            dx+dy+dz
        ) 
```

Enter fullscreen mode Exit fullscreen mode

## 功能装饰

### functools.lru_cache

缓存最后调用的 *n* 函数。参数必须可以用作字典索引键。

```
from functools import lru_cache
@lru_cache(20)
def get_exchange(local, foreign):
    exchange_service.convert(local, foreign) 
```

Enter fullscreen mode Exit fullscreen mode

### 异步。coroutine

将基于发电机的可兰经转换为与新型号*【async/await】*兼容。仅在需要携带的代码中使用

```
from asyncio import coroutine

@coroutine
def render_view(view_name, context):
    template = Template(view_name, context)
    yield template.render()

async def hanlde_get(route):
    await render_view(route.view, route.params) 
```

Enter fullscreen mode Exit fullscreen mode

### contextlib.contextmanager

将函数转换为上下文管理者。有两种使用方法:

1.  使用句子 *with*
2.  作为一个装饰者。

亲自制作属于*上下文管理者*的装饰品，我更喜欢使用*上下文管理者*类。

```
from contextlib import contextmanager

@contextmanager
def acquire_lock(key):
    credentials = get_credentials(key)
    lock = acquire_lock_using_credentials(credentials)
    try:
        yield lock
    finally:
        release_lock(lock)

with acquire_lock('my secret api key') as lock:
    use_critical_resource(lock) 
```

Enter fullscreen mode Exit fullscreen mode

## 装饰者多余

### functools.wraps

装饰者创造装饰者。有助于使函数保留诸如 _name_ 和 _ **doc** _
等属性

```
from functools import wraps
def my_decorator(f):
    @wraps(f)
    def wrapper(*args, **kwds):
        return f(*args, **kwds)
        return wrapper

@my_decorator
def funcion():
    """
    Esta es la doc
    """
    pass 
```

Enter fullscreen mode Exit fullscreen mode

### functools . single dispatch

根据第一个参数的类型创建过载特征。使用类型注释。

```
class Perro:
    pass

clas Vaca:
    pass

@singledispatch
def sonido(animal):
    pass

@sonido.register(Perro)
def _(animal):
    print("Ladra")

@sonido.register(Vaca)
def _(animal):
    print("Muge") 
```

Enter fullscreen mode Exit fullscreen mode

* * *

1.  我不太赞成在 Python 中使用静态方法。<sup>【返回】</sup>**