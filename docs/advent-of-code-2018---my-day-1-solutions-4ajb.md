# Code 2015 的到来-我的第一天解决方案

> 原文：<https://dev.to/jonchiou/advent-of-code-2018---my-day-1-solutions-4ajb>

你好！

这是我关于 DEV 的第一篇文章，也是我第一年试图完成代码的

### 编辑

哎呀！这是从 *2015* ！我在 AOC 工作时启动了一个松弛频道，并将 2015 年的第一天作为团队的热身。标题已经过编辑，以反映这一点。

#### 第 1 天第 1 部分

我选择使用 Python 3，因为它是我在解决这类小问题时最擅长的语言。我在工作中也用 Java 编写，所以在这里使用 Python 是保持敏锐和继续培养我的语言技能的好方法。

##### `Day1Part1_v1.py`

```
import re

input_string = 'input.txt'

with open(input_string, 'r') as input_file:
    for line in input_file:

        open_parens = re.findall(r'\(', line)
        close_parens = re.findall(r'\)',line)

        open_parens_count = len(open_parens)
        close_parens_count = len(close_parens)

        floor = open_parens_count - close_parens_count
        print("Santa, go to floor " + str(floor))

input_file.close() 
```

我想使用`requests`模块来获取输入文本，而不是保存到`.txt`中，但是那样我就不得不花时间在我的脚本中编写一个登录和建立会话的方法。

使用 regex 可能是不必要的，因为所寻找的字符串模式只是单个字符，但是我已经习惯了在 Python 解析脚本中使用 regex，这是我出于习惯而倾向于使用的。

所以在看到一位同事的文章后，我决定进行重构，并对我的脚本中出现的括号数量感到高兴！

##### `Day1Part1_v2.py`

```
input_string = 'input.txt'

with open(input_string, 'r') as input_file:
    for line in input_file:
        print("Floor #: " + str(len(line.split('('))-len((line.split(')'))))) 
```

#### 第一天第二部分

当我注意到还有第二部分的时候，我决定第三次尝试重构。幸运的是，我的第三个版本通过迭代每一个字符，朝着适合我对这个问题的需求的方向前进，所以我只是修改它，然后解决第二个问题。

我不记得我是如何或者为什么来使用`operator`模块的，除了我可能在寻找一些东西来以 Java 拥有`.equals`的方式进行字符串比较？

##### `Day1Part2_v1.py`

```
import operator

input_string = 'input.txt'

floor = 0
char_count = 0
in_basement = False

with open(input_string, 'r') as input_file:
    for line in input_file:
        for char in line:
            if operator.eq('(',char):
                floor += 1
            elif operator.eq(')',char):
                floor -= 1
            else:
                print('invalid input')
            char_count += 1
            if(floor < 0):
                if not in_basement:
                    in_basement = True
                    print("Entered basement at char: " + str(char_count))

    print("Floor: " + str(floor))
input_file.close() 
```

* * *

我从这些谜题中获得了很多乐趣！我将在第二天写一篇文章，我已经完成了，并且(希望)每天都完成它们。

我当然欢迎任何和所有的反馈，因为我参与和分享的主要目标是成为一个更好的程序员。

感谢阅读！