# 编写您的第一个 Python 程序

> 原文：<https://dev.to/codetips/writing-your-first-python-programme-5clk>

[![Writing Your First Python Programme](img/120122e14fa28203076c0e67e9db6172.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vgA3OIDU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/05/digital-dreams-1155366.jpg)

> 到本文结束时，您将已经编写了一个 Python 程序，该程序基于许多变量输出一个句子。

如果你已经阅读了这篇文章，你应该已经阅读了 [Python 简介](https://www.codetips.co.uk/languages/python/python-introduction/)，并且熟悉了[变量](https://www.codetips.co.uk/beginner/what-is-a-variable/)和[数据类型](https://www.codetips.co.uk/beginner/what-is-a-data-type/)。

正如我们在 [Python 简介](https://www.codetips.co.uk/languages/python/python-introduction/)中所说，Python 是一种[解释的](https://www.codetips.co.uk/intermediate/translation-and-types/)语言，因此它的类型是[推断的](https://www.codetips.co.uk/intermediate/translation-and-types/)。

在 Python 中只有一种方法来声明变量。

在以下示例中:

*   `x`被赋予值`"Hello World"`，并具有推断类型`String`。
*   `y`被赋予值`True`，并具有推断类型`Bool`。
*   `a`被赋予值`1`，并具有推断类型`Int`。
*   `b`被赋予值`3.14159`，并具有推断类型`Float`。

```
x = "Hello World"
y = True
a = 1
b = 3.14159 
```

Enter fullscreen mode Exit fullscreen mode

由于 Python 是一种[动态类型语言](https://www.codetips.co.uk/intermediate/translation-and-types/)，所以无法将变量声明为特定的[数据类型](https://www.codetips.co.uk/beginner/what-is-a-data-type/)。

然而，可以使用`type()`函数基于类型执行逻辑。

下面的 [`if`语句](https://www.codetips.co.uk/beginner/what-is-an-if-statement/)检查`y`的类型是否是`bool`，如果是`True`，将运行`<do something>`动作。否则，它将跳过代码的缩进部分。

```
y = True

if type(y) is bool:
    <do something> 
```

Enter fullscreen mode Exit fullscreen mode

也可以在 Python 中执行“类型转换”。在以下示例中:

*   变量`x`被赋予值`1`并具有推断类型`Int`。

*   `x` ( `1`)的值被“铸造”成一个`float`并赋给变量`y`。因此`y`的类型是`float`。

```
x = 1
y = float(x)

print(type(x)) // => outputs int 
print(type(y)) // => outputs float 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 你的第一个 Python 程序

终于到了编写第一个 Python 程序的时候了。您可以在机器上的任何地方编写这些代码，但是，最好的做法是将您的开发代码放在一个特定的地方并组织好。

在您的主目录中(Linux 和 Mac 上的`$HOME`，Windows 上的`%userprofile%`，创建一个名为`python`的新目录(例如，完整的 Linux 路径将是`/home/codetips/python`)。导航到这个目录，创建另一个名为`firstApplication`的目录(例如，完整的 Linux 路径是`/home/codetips/python/firstApplication`)，并在您选择的代码编辑器中打开它。

我们将省略 Python 最佳实践的大部分细节，这样您就不会被太多的信息淹没，但是一切都将在以后的文章中解释。

在新的`firstApplication`目录中，创建一个名为`main.py`的新文件，将下面的代码添加到其中，并保存该文件。

```
#!/bin/env python

# Start of our code

print("Welcome to my first Python programme!")

# End of our code 
```

Enter fullscreen mode Exit fullscreen mode

现在，打开一个终端，导航到您的`firstApplication`文件夹，并运行以下命令:

`python main.py`

您的输出应该类似于下面的内容:

```
$ [user@linux]: python main.py
Welcome to my first Python programme! 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`print`功能将文本输出到终端。不幸的是，作为第一个节目，这是相当无聊的。让我们用[变量](https://www.codetips.co.uk/beginner/what-is-a-variable/)把它变得更加个人化！

用以下代码替换`main.py`的内容:

```
#!/bin/env python

# Start of our code

name = "Jordan"
age = 31
profession = "Developer"

print("""Welcome to my first Python programme!\n\
My name is {0}\n\
I am {1} years old\n\
I work as a {2}""".format(name, age, profession))

# End of our code 
```

Enter fullscreen mode Exit fullscreen mode

这次我们输出四个不同的句子。前一个例子中的一个和其他三个使用相同的`print`函数，但是注意字符串定义末尾的`.format()`函数。

Python 中的`"""`语句意味着我们要创建一个多行字符串。

`format`函数允许程序员将变量传递给字符串。在上面的例子中:

*   `name`变量(在位置`0`，在字符串中称为`{0}`)
*   `age`变量(在位置`1`，在字符串中称为`{1}`)
*   `profession`变量(在位置`2`，在字符串中称为`{2}`)。

知道变量的位置，也称为`index`，允许程序员通过简单地引用字符串中的位置来重复同样的事情任意次。我们将在以后的文章中讨论这个问题。

我们可以用以下方式定义字符串:

```
string1 = "This is a string using double-qoutes"
string2 = 'This is a string using single-quotes"
string3 = `This is a string using back-ticks`
string4 = """This is a multi-line string \
using double-quotes"""
string5 = '''This is a multi-line string \
using single-quotes''' 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，最好使用双引号，但以上都是有效的 python 语法。

现在看看你能否完成以下挑战:

*   改变变量，让程序打印出你的姓名、年龄和职业。
*   修改程序，在一行中输出所有内容，而不是四行。
*   添加一个新变量`favoriteAnimal`，并将其合并到您输出的句子中。

请发微博给我们，地址是 [@RealCodeTips](https://twitter.com/realcodetips) ，并向我们展示您的解决方案。如果你中途遇到困难，请伸出援手，我们会尽力帮助你。

我们还建立了一个 Slack 社区来更实时地帮助人们。你可以在这里加入我们。