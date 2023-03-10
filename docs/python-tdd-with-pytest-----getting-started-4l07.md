# 使用 Pytest 的 Python TDD 入门

> 原文：<https://dev.to/wangonya/python-tdd-with-pytest-----getting-started-4l07>

测试驱动的开发很棒。如果您是 Pythonista，pytest 会更好——一个使构建简单和可伸缩的测试变得容易的框架。

在本系列中，我将探索 pytest 提供的一些很酷的特性，这些特性有助于轻松测试 Python 代码。

## 准备您的 python 环境

让我们用`virtualenv`设置我们的测试环境。我们将使用 Python 3。`cd`进入你的工作目录，创建一个新的虚拟环境:

```
python3 -m venv env 
```

Enter fullscreen mode Exit fullscreen mode

激活虚拟环境:

```
source env/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

### 安装`pytest`

```
pip install pytest 
```

Enter fullscreen mode Exit fullscreen mode

这就是准备 pytest 所需要做的全部工作。您可以通过运行
来检查安装的版本

```
pytest --version 
```

Enter fullscreen mode Exit fullscreen mode

## 创建您的第一个测试

假设我们需要创建一个应用程序，当它运行时，它会向你给它起的名字问好(我知道，非常有创意😅).

```
# simple hello world test 
def hello_world(name):
    pass

def test_hello():
    assert hello_world("World!") == "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到我们的测试函数名称以单词 *test* 开始。pytest 就是这样发现测试方法的。还有，:

*   测试文件应该命名为`test_<something>.py`或`<something>_test.py`
*   测试类应该被命名为`Test<Something>`

运行`pytest hello.py`应返回:

```
collected 1 item
hello.py F                         [100%]

===================== FAILURES ======================
    def test_hello():
>       assert hello_world("World!") == "Hello World!"
E       AssertionError: assert None == 'Hello World!'
E        +  where None = hello_world('World!')

hello.py:6: AssertionError 
```

Enter fullscreen mode Exit fullscreen mode

Pytest 显示测试失败的原因:`AssertionError: assert None == 'Hello World!’`。显然，我们的 hello 函数中没有代码，所以让我们来解决这个问题。

```
def hello_world(name):
    return "Hello {}".format(name)

def test_hello():
    assert hello_world("World!") == "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

正在运行的`pytest hello.py`现在应该返回:

```
collected 1 item     
hello.py .                       [100%]

==================== 1 passed in 0.04 seconds ====================== 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，使用 pytest 开始在 python 中进行测试非常容易。现在我们已经设置好了一切，随着本系列的继续，我们将进入更高级的特性。