# Nim Lang 入门

> 原文：<https://dev.to/itachiuchiha/getting-started-nim-lang-3g5b>

# 入门尼姆郎

嗨，我为一个周末活动研究了编程语言。在这篇文章中，我不会告诉一切。

科技界有许多编程语言。我在 Groovy 和 Nim 语言之间犹豫不决。

事实上，我在寻找一种类似 Python 的编程语言。Python 的语法对我来说很漂亮。Nim 也有类似的语法。

刚开始编程的时候用了很多编程语言像 Visual Basic 6，Pascal，ASP。

Nim 有 Delphi (Object Pascal)之类的语法。例如，可以像这样创建一个方法；

```
proc shopping(basket: Basket): int =
  if basket.id > 0:
    updateShoppingAmount(basket)

  saveShopping(basket) 
```

Enter fullscreen mode Exit fullscreen mode

你可以像那样使用这个方法。

```
type
  Basket = ref object of RootObj
    id:* int
    amount:* float
    productName:* string
    userID: int

basket = Basket(id: 0, amount: 22.5, productName: 'Shoes')

shopping(basket) 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，类型就像 Nim 语言的类。在 C #类中，私有字段是这样创建的；

```
public class Basket
{
  private int userID { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

Private 意味着该字段对其他模块隐藏。要在 Nim 语言中公开字段，需要使用`*`星号。另一方面，如果你想创建私有字段，你不应该使用星号。

这些是对尼姆语的介绍。让我们看几个例子。

[![](img/387fa7e87f4a6f29e854731f43d515b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FXn95gLX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dietndvjl68y6tu5hlrz.png)

## 安装

Nim 在 Linux、Mac 和 Windows 上工作。您可以使用[这个](https://nim-lang.org/install.html)链接进行安装。我没有使用 Windows，所以我不知道安装过程应该是怎样的。

对于 Linux 和 Mac，可以使用 **choosenim** 。

choosenim 是 nim 编程语言的安装程序。它允许您轻松地在 Nim 版本之间切换，无论是最新的稳定版本还是最新的开发版本。

要使用 choosenim 安装 Nim 的最新稳定版本，只需在您的终端中运行以下命令，然后按照屏幕上的说明进行操作:

```
curl https://nim-lang.org/choosenim/init.sh -sSf | sh 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是安装 Nim 最简单的方法。

**安装软件包管理器**

阿奇/曼哈罗

```
pacman -S nim 
```

Enter fullscreen mode Exit fullscreen mode

Debian / Ubuntu

```
apt-get install nim 
```

Enter fullscreen mode Exit fullscreen mode

码头工人

获取最新稳定图像:

```
docker pull nimlang/nim 
```

Enter fullscreen mode Exit fullscreen mode

最新开发版本:

```
docker pull nimlang/nim:devel 
```

Enter fullscreen mode Exit fullscreen mode

Fedora

```
dnf install nim 
```

Enter fullscreen mode Exit fullscreen mode

对于其他发行版，请访问这个链接

## 你好世界

在 Nim 语言中，我们使用 echo for 将给定的对象打印到标准输出。

`echo`和`echo()`做同样的事情。可以调用不带括号的过程。

```
echo("Hello World") 
```

Enter fullscreen mode Exit fullscreen mode

我将这段代码保存到名为 **hello_world.nim** 的文件中

## 注释

注释对于编程语言来说很重要。在 Nim 语言中，有不同的注释类型。

**单行注释**

我对单行注释使用散列；

```
# This is a comment 
```

Enter fullscreen mode Exit fullscreen mode

**多行注释**

多行注释以#[和]#开始。多行注释也可以嵌套。

```
#[
  This procedure gives Basket object.
  shopping(basket)
  #[
     Note: userID isn't accessible from the other modules.  ]#  ]# 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用 discard 语句。

```
discard """ This procedure gives Basket object.
Note: userID isn't accessible from the other modules..
      shopping(basket) """ 
```

Enter fullscreen mode Exit fullscreen mode

## 数字

数字文字的书写方式与大多数其他语言一样。但是你也可以在数值中使用下划线。怎么会？

```
var price = 1_000_000 
```

Enter fullscreen mode Exit fullscreen mode

并且可以用**点**、 **e** 或者**E**T6】

```
var price = 1.0e2

echo price # 100.0 
```

Enter fullscreen mode Exit fullscreen mode

## Var 语句

var 语句声明了一个新的局部或全局变量。您可以同时在单行或多行中声明一个变量。

**单线**

```
var x, y: int 
```

Enter fullscreen mode Exit fullscreen mode

**Multiline**

```
var
    # user's name
    name: string

    # user's surname
    surname: string

    # user's phone
    phone: string 
```

Enter fullscreen mode Exit fullscreen mode

## Const 和 Let 语句

const 和 let 看起来是一样的。常数是绑定到值的符号。常量的值不能改变。编译器必须能够在编译时对常量声明中的表达式求值:

```
const x = "abc" 
```

Enter fullscreen mode Exit fullscreen mode

也可以使用缩进。

let 语句的工作方式类似于 var 语句，但声明的符号是单赋值变量:初始化后它们的值不能改变:

```
let x = "abc" 
```

Enter fullscreen mode Exit fullscreen mode

let 和 const 的区别在于:let 引入了一个不可重新赋值的变量，const 的意思是“强制编译时求值并放入数据段”:

**const**T2】

```
const input = readLine(stdin) # Error: constant expression expected 
```

Enter fullscreen mode Exit fullscreen mode

**让**T2】

```
let input = readLine(stdin)   # works 
```

Enter fullscreen mode Exit fullscreen mode

## 编译运行

为了编译和运行 nim 代码，我们使用以下命令:

```
nim c -r hello_world.nim 
```

Enter fullscreen mode Exit fullscreen mode

它编译 hello_world.nim 文件并运行它。

## 结论

我真的很喜欢用尼姆语。

*   您可以将您的 nim 代码用于 C、CPP 和 JS 语言。
*   您可以创建类型安全变量。
*   如果你来自 Python 语言，你就不会说“我什么也不懂”。
*   面向对象的编程范例可能与其他不同。
*   过程是 Nim 中的方法
*   Nim 中没有自我或这个关键词
*   你应该看看 Nim 的标准库。

## 资源

我在本文中使用了这些资源:

*   [通过 Nim 官方网站学习 Nim](https://nim-lang.org/learn.html)
*   [Nim 基础知识](https://narimiran.github.io/nim-basics/)
*   [尼姆以身作则](https://nim-by-example.github.io/getting_started/)

即使你不用尼姆，你也应该给尼姆一个周末活动的机会。

感谢阅读。我希望你会喜欢这篇文章。