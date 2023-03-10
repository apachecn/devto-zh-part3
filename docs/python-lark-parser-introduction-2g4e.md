# Python Lark 解析器简介

> 原文：<https://dev.to/vicentemaldonado/python-lark-parser-introduction-2g4e>

[![](img/39d30a5f80771867cea2e606816e1b5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yDuXZaLX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/450/1%2A_GVVwTmMRDTKNhP4OlxG_g.jpeg)

[Lark](https://lark-parser.readthedocs.io/en/latest/) 是一个 Python 解析库。不像 Yacc 这样的解析器生成器，它不从语法生成源代码文件——解析器是动态生成的。让我们看看它的工作原理。你进口百灵鸟:

```
from lark import Lark 
```

然后指定语法:

```
grammar = """
start: WORD "," WORD "!"
%import common.WORD
%ignore " "
""" 
```

语法可以是 Python 字符串，也可以从单独的文件中读取。之后，创建一个 Lark 类实例，用语法:
初始化它

```
parser = Lark(grammar) 
```

您已经准备好解析:

```
def main():
    print(parser.parse("Hello, world!"))
    print(parser.parse("Adios, amigo!"))

if \_\_name\_\_ == '\_\_main\_\_':
    main() 
```

parser.parse 返回一个包含解析树
的[树](https://lark-parser.readthedocs.io/en/latest/classes/#tree)实例

```
Tree(start, [Token(WORD, 'Hello'), Token(WORD, 'world')])
Tree(start, [Token(WORD, 'Adios'), Token(WORD, 'amigo')]) 
```

就这样，干净简单。由您决定如何处理解析后的字符串。让我们看看从那里我们能去哪里。下面是一个简单的算术表达式解析器的例子:

```
from lark import Lark

grammar = """
start: add\_expr
     | sub\_expr

add\_expr: NUMBER "+" NUMBER

sub\_expr: NUMBER "-" NUMBER

%import common.NUMBER
%ignore " "
""" 
```

语法忽略空格。还要注意，语法终端是用大写字母(数字)写的，而语法规则是用小写字母写的(start、add_expr 和 sub_expr)。%import 和%ignore 是指令。你可以在 Lark 文档中找到[语法参考](https://lark-parser.readthedocs.io/en/latest/grammar/)。我们可以从其他语法中导入定义——在本例中是 [common.lark](https://github.com/lark-parser/lark/blob/master/lark/grammars/common.lark) 。(common.lark 只是包含了一些有用的定义)。上面的语法将成功解析加减法表达式，比如:

```
1+1
2-1
3 - 2 
```

别无其他。接下来，创建 Lark 对象:

```
parser = Lark(grammar) 
```

我们准备好解析:

```
def main():
    print(parser.parse("1+1"))
    print(parser.parse("2-1"))
    print(parser.parse("3 - 2"))    

if \_\_name\_\_ == '\_\_main\_\_':
    main() 
```

输出如预期:

```
Tree(start, [Tree(add\_expr, [Token(NUMBER, '1'), Token(NUMBER, '1')])])
Tree(start, [Tree(sub\_expr, [Token(NUMBER, '2'), Token(NUMBER, '1')])])
Tree(start, [Tree(sub\_expr, [Token(NUMBER, '3'), Token(NUMBER, '2')])]) 
```

注意，这个例子只是像以前一样打印解析树。让我们把转换成更有用的东西:

```
from lark import Lark, Transformer

grammar = """
start: add\_expr
     | sub\_expr

add\_expr: NUMBER "+" NUMBER -> add\_expr

sub\_expr: NUMBER "-" NUMBER -> sub\_expr

%import common.NUMBER
%ignore " "
""" 
```

语法规则右侧的 add_expr 和 sub_expr 是成功解析规则时要应用的函数的名称。让我们来写它们:

```
class CalcTransformer(Transformer):

    def add\_expr(self, args):
        return int(args[0]) + int(args[1])

    def sub\_expr(self, args):
        return int(args[0]) - int(args[1]) 
```

呃。例如，当解析
时

```
2-1 
```

args[0]将包含“2”，args[1]将包含“1”。在我们的 transformer 函数中，我们将两者都转换成整数，然后将它们相加或相减，返回结果。现在创建 Lark 对象:

```
parser = Lark(grammar, parser='lalr', 
    transformer=CalcTransformer()) 
```

为了能够接受转换器，解析器需要是 LALR 解析器。我们终于准备好解析:

```
def main():
    print(parser.parse("1+1"))
    print(parser.parse("2-1"))
    print(parser.parse("3 - 2"))

if \_\_name\_\_ == '\_\_main\_\_':
    main() 
```

现在的输出是:

```
Tree(start, [2])
Tree(start, [1])
Tree(start, [1]) 
```

好些了吗？1+1 是 2，2–1 是 1，3–2 也是 1。

当然，这只是皮毛。如果你有兴趣，可以在 Github 上找到[完整示例](https://github.com/Wurdlack/Medium/tree/master/lark_examples)。