# 递归导入是 Python 的致命弱点

> 原文：<https://dev.to/prahladyeri/recursive-imports-is-the-achilles-heel-of-python-i25>

Python 在语法上是一门漂亮的语言，并且可读性和可维护性也很好。但是说到缺点，我认为它最大的缺点是*递归导入*。

这个弱点源于拥有可重用性的想法，据我所知，其他流行语言都没有。在一些像`java`和`C#`的地方，一切都是一个类，所以这种问题从来不会发生。在其他类似`PHP`的项目中，过程化代码是允许的，但是他们使用简单的`require`或`include`构造来添加可重用的代码。

在 python 中，有很多情况下 X 模块导入 Y 模块，同时 Y 模块需要从 X 模块导入一个对象。一旦你开始开发复杂的应用程序，这是一个非常常见的场景。例如，这甚至可以在一个非常基本的`flask`应用中看到，它有自己的包(比如说`app`)、一个初始化模块(`__init__.py`)和一个路由模块(`routes.py`)，就像这样:

```
└───app
    │   __init__.py
    │   routes.py 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的`flask`应用程序，我在 init 模块中定义了实际的 flask 对象:

```
# c:\source\app\__init__.py
from flask import Flask
from app import routes

app = Flask(__name__) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我将 routes.py 定义如下:

```
# c:\source\app\routes.py
from app import app

@app.route('/')
@app.route('/index')
def index():
    return "Hello, World" 
```

Enter fullscreen mode Exit fullscreen mode

问题是这个代码不行！原因很简单，init 模块试图导入路由，但是 routes 模块会失败(原因很简单，因为 routes 模块试图从尚未初始化的 init 模块导入 app.app 对象)。

这就是递归导入的问题。为了解决这个问题，我必须确保 routes 模块总是在 app 对象初始化后导入，如下所示:

```
# c:\source\app\__init__.py
from flask import Flask

app = Flask(__name__)

from app import routes 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，在 python 这样结构化且看似简单的语言中，递归导入可能很难处理。