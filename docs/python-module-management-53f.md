# Python:模块管理

> 原文：<https://dev.to/email2vimalraj/python-module-management-53f>

最初发表于我的[博客](http://www.vimalselvam.com/2019/03/04/python-module-management/)。

这个帖子是我上一个帖子的延续:[用 Pipenv 进行 Python 包管理](https://dev.to/email2vimalraj/python-package-management-with-pipenv-44kh-temp-slug-7990359)。我已经完成了包/依赖项管理，现在我想设置文件夹结构并执行脚本。

我的问题陈述是我没有任何可以调用任何模块来触发的主脚本。有时，我会单独执行任何文件夹中的任何脚本，并且我不应该与任何文件有任何硬依赖关系。让我给你看一个例子，它可以很容易地解释我的问题。

考虑一下我现在的文件夹结构:

[![Folder structure](img/54d1854cfc344956a53ef38b06785e4f.png)](https://i1.wp.com/www.vimalselvam.com/wp-content/uploads/2019/03/python-demo-project-structure.png)

`__init__.py`是一个特殊的文件，帮助 python 把文件夹/文件当作模块。例如，`common/`目录中的`__init__.py`文件使它成为一个模块，这样用户可以在任何地方将它作为一个模块导入。而`__init__.py`文件的内容只是空的。

我的`constant.py`的内容很简单:

```
ENDPOINT = "http://vimalselvam.com" 
```

和`src/datastore/`目录中的`start.py`:

```
import common.constant as constant

def run():
    print(constant.ENDPOINT)

if __name__ == " __main__":
    run() 
```

光是看上面的代码，你就觉得执行起来没问题了吧？这是不对的。让我们执行并看看发生了什么:

```
~ pipenv run python src/datastore/start.py
Traceback (most recent call last):
  File "src/datastore/start.py", line 1, in <module>
    import common.constant as constant
ModuleNotFoundError: No module named 'common' 
```

怎么回事！问题是，如果您执行的文件与当前目录无关，Python 不会解析模块依赖关系。所以你必须将`start.py`文件作为一个模块来执行。让我们这样做:

```
~ pipenv run python -m src.datastore.start
http://vimalselvam.com 
```

抓住你了。我花了一些时间来解决这个问题，因此这是一个帖子。如果你觉得这很有用，请点赞并分享。