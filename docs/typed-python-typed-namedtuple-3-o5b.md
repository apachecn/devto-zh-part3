# 类型化 python +类型化 NamedTuple <3

> 原文：<https://dev.to/andi/typed-python-typed-namedtuple-3-o5b>

# 注意局限性:

> 函数注释只不过是在编译时将任意 Python 表达式与函数的各个部分关联起来的一种方式。

[https://stackoverflow.com/a/43977962/953553](https://stackoverflow.com/a/43977962/953553)

# 使用方式:

*   用类型化字段定义类
*   指定函数接收的参数类型

# 它是如何被检查的？

*   像 pycharm 这样的好 IDE 就可以了
*   安装需要警告的`pep8`和`mypy-lang`

# 例子

## 基本类型

(安装`pep8`和`mypy-lang`后运行):

```
def greeting(name: str) -> str:
     return 'Hello ' + name 
```

然后呼叫:

```
greeting(2) 
```

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in greeting
TypeError: Can't convert 'int' object to str implicitly 
```

```
greeting('3')
#'Hello 3' 
```

## 支持类和继承

## 更复杂的类型:

### 列表

```
from typing import List 
```

### 元组

```
from typing import Tuple 
```

### 命名成对

```
from typing import NamedTuple 
```

### 字典

```
from typing import Dict 
```

### 工会

是允许 varaible 有多种类型的方法。

### Optional

### 这里有更多关于打字的好例子:

[https://blog . daft code . pl/first-steps-with-python-type-system-30e 4296722 af](https://blog.daftcode.pl/first-steps-with-python-type-system-30e4296722af)

# python 3 中的类型化元组是 collections.namedtuple 的类型化版本

```
from typing import NamedTuple

class Employee(NamedTuple):
    name: str
    id: int = 3

employee = Employee('Guido')
assert employee.id == 3 
```