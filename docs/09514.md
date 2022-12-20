# 运行 Python 脚本时，在命令行中添加文件名

> 原文：<https://dev.to/flaviabastos/add-a-filename-in-the-command-line-when-running-a-python-script-29f5>

#### 或者:运行带参数的 Python 脚本

通过从 python 的标准库导入 [sys 模块，并使用其](https://docs.python.org/3/library/sys.html)*[argv](https://docs.python.org/3/library/sys.html#sys.argv)* [函数](https://docs.python.org/3/library/sys.html#sys.argv)，可以在运行 Python 脚本时向命令行添加参数。

一些例子:

显示用户名的简单脚本:

```
def main(): name = 'Frodo' print(f'Hello, {name}')if \_\_name\_\_ == "\_\_main\_\_": main() 
```

冉为 *python3 my_script.py* ，会显示:

```
Hello, Frodo 
```

当调用脚本时，这可以被重构以显示在命令行中用作参数的任何名称:

```
import sysdef main(): name = sys.argv[1] print(f'Hello, {name}')if \_\_name\_\_ == "\_\_main\_\_": main() 
```

将您想要使用的*名称*添加到命令行:

```
python3 my\_script.py Galadriel 
```

现在将输出:

```
Hello, Galadriel 
```

您会注意到，在脚本中，我们将索引 1 处的元素从 *argv* 分配给 name。如果你在脚本中加上这一行:

```
print(f'ARGS: {sys.argv}') 
```

您会注意到 *argv* 返回一个列表，其中包含传递给 Python 脚本的所有参数，第一个参数总是脚本的名称:

```
ARGS: ['my\_script.py', 'Galadriel'] 
```

例如，如果您需要编写一个读取文件的脚本，这将非常有用。在这种情况下，您不需要在脚本中硬编码文件名，而是在运行脚本时传递它:

```
python3 my\_script.py my\_file.txt 
```

> *post _[运行 Python 脚本时在命令行添加文件名](https://wp.me/pa0b0y-3b)* 最初发布于 _ [flaviabastos.ca](https://flaviabastos.ca/)