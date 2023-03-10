# 调试 Python 应用程序(外加免费备忘单)

> 原文：<https://dev.to/flaviabastos/debugging-python-applications-plus-free-cheat-sheet-456o>

我的主要调试工具是在我的程序中添加打印语句。Print 语句非常容易使用，并且适用于任何简单的脚本。但是这是一个问题:如果您正在调试一个应用程序和/或一个测试文件，print 语句是不够的或者是不起作用的(在测试文件的情况下)。

对于这些情况，我发现 *pdb* 、作为 Python 标准库一部分的[调试器是下一个最好的选择:使用起来也很简单，但能让你更深入地了解程序中正在发生的事情。](https://docs.python.org/3/library/pdb.html)

你所要做的就是调用 *pdb* 进入调试模式。可以用 *pdb* 调用脚本，比如:

```
python3 -m pdb myscript.py 
```

Enter fullscreen mode Exit fullscreen mode

或者在脚本内部调用 *pdb* 来停止常规执行并开始调试——对于 python 3.6 和更早版本:

```
import pdb; pdb.set_trace() # python 3.6 and older 
```

Enter fullscreen mode Exit fullscreen mode

或者对于 python 3.7 及以上:

```
breakpoint() # python 3.7 
```

Enter fullscreen mode Exit fullscreen mode

当处于调试模式时，你可以访问 *pbd* 控制台，在那里你可以使用 *pdb* 命令(如下所列),也可以检查你的脚本——比如打印变量内容(只需在控制台中键入变量的名称)。

#### 举例:

```
 number = '123456' total = 0 import pdb; pdb.set\_trace() for nb in number: total += int(nb) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的简短脚本中，一旦调试器控制台启动，您可以按' n '到达下一行，然后键入' nb '以读取该执行点的值。

就个人而言，最常用的 *pdb* 命令如下所列，但你可以在[官方文档](https://docs.python.org/3/library/pdb.html)或[免费备忘单](https://drive.google.com/file/d/1TRhDf0XuImgCTgkwwviu0V4c7c-NfVHL/view?usp=sharing)中找到完整列表:

**s(tep)** :执行当前行，在第一个可能的时机停止

**c(ont(inue))** :继续执行，只在遇到断点时停止。

**n(ext)** :继续执行，直到到达当前函数的下一行或返回

**r(eturn)** :继续执行，直到当前函数返回。 **q(uit)** :退出调试器。正在执行的程序被中止。

*帖子* [*调试 Python 应用(加免费小抄)*](https://wp.me/pa0b0y-3n) *最初发表于* [flaviabastos.ca](https://flaviabastos.ca/)