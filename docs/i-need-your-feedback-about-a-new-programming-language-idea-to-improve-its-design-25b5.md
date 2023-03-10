# 我需要你对一个新的编程语言想法的反馈，以改善其设计。

> 原文：<https://dev.to/mak12776/i-need-your-feedback-about-a-new-programming-language-idea-to-improve-its-design-25b5>

你好，我想创建一个新的编程语言，名为`codescript`。它将是开源和免费的。这个仓库是我开始的地方。

我需要你的反馈。所以我可以改进语言的设计。

它将是一种[脚本语言](https://en.wikipedia.org/wiki/List_of_programming_languages_by_type#Scripting_languages)，并将被编译成自己的字节码。

免责声明:我不太擅长说英语，但我是一名经验丰富的程序员，所以我可以通过代码示例对其进行更好的解释。

好，我们开始吧。

假设你打开了一个名为`main.code`的文件来写`codescript`。

刚开始写文件时，没有定义变量或函数，也没有编译器能理解的`if`、`else`、`else if`、`for`或`while`标记。程序流程和其他东西将只由符号来编写。此外，没有将文本写入`stdout`的`print`或`puts`功能。(可通过`[# system]`模块访问)。所以对你的变量名没有限制，除了它们应该匹配[正则表达式](https://www.google.com/search?q=regular+expression+definition)模式`[_a-zA-Z][_a-zA-Z0-9]*`并且它们不能是`T`、`F`或`N`常量。

现在，可以存储在变量中的数据类型有:

```
// comments start with two slashes.

char = 'a'
int = 10
float = 2.3

bool = T                 // or F
none = N

// `;` can be used to separate statements, but aren't mandatory.

array = [1, 'a', T];
string = "abcd";
code = #(a = 10);

object =
{
    name = "jack"
    age = 10
};

function num => 10 * num;

// `function` is not a keyword. you are defining a function named `function`. 
```

#### char，int，float

`char`、`int`、`float`都是你可能知道的简单数据类型。

```
english_first_letter = 'a'
seconds_in_a_year = 60 * 60 * 24 * 365
pi_number = 3.14159265 
```

#### bool，无

`bool`和`none`分别代表布尔和无。在`bool`和`none`数据类型之间不能有`&&`和`||`或`||`:

```
boolean = T || N            // error: `bool` and `none` can't be Or-ed. 
```

其他数据类型之间也是如此:

```
boolean = "Hello" && 5      // error: `string` and `int` can't be And-ed. 
```

它们只允许在`bool`数据类型之间使用:

```
true = F || T
false = true && F

// we can also xor between booleans

var = T ^^ F            // var = T 
```

#### 数组，字符串

`array`和`string`数据类型只是因为计算机内存数据模型而存在。我想设计尽可能简单的代码脚本。并让开发人员用自己的创造力完成额外的工作。`string`和`array`唯一的区别就是`string`数据类型只能存储`char`。

```
name = "John"
name_len = name.len            // name_len == 4

numbers = [2, 4, 6]
numbers_len = numbers.len      // numbers_len == 3 
```

#### 代码

`code`数据类型只是一段代码。这意味着当您键入:

```
code = #(a = 10) 
```

您正在将一个`variable definition statement`存储到另一个变量中。

好，我给你举几个例子:

```
define_var = #(var = "Hello");

define_var;                      // it's the same as: var = "Hello"

hello_world = var + " World";    // hello_world == "Hello World" 
```

我们还可以存储计算报表:

```
var1 = #(2 * 3);
var2 = 2 * 3;
// type of var1 is `code`, but var2 contains `6`, which is an `int`.

a = var1;
b = var2;
// now, `a` and `b`, both are of the type `int`. 
```

或者存储多条语句:

```
code_object = #(name = "Jack"; age = 20;);

code_object;
// now, `name` and `age` are defined. 
```

或者根据以前的定义定义变量:

```
define_b = #(b = a * 10);
a = 3;

define_b;
// now, b == 30 
```

值得一提的是，我们可以将代码语句存储到一个变量中，稍后我将向您展示这样做的好处。

#### 物体

数据类型就像名称空间，或者类。可以在里面定义变量，可以通过`.`成员访问来访问或者赋值。

```
object = 
{
    name = "Jack"
    age = 20
}

// object.name == "Jack"
// object.age == 20

// assign color member of object, not previosly defined:
object.color = "white" 
```

对象可用于代码块，例如:

```
array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

ten_times_array_len = 
{
    result = 10 * array.len;
}.result;

// you might have guessed that ten_times_array_len is equal to 100.
// and there is no variable defined named `result`. 
```

#### 功能

数据类型只是函数。它们可以被调用，并返回任何类型的数据。

我不太确定函数定义的风格，我在考虑一些风格:

```
// --- style 1 ---

// function declaration:
sum a b => a + b;

// anonymous declaration:
sum = (a b) => a + b;

// --- style 2 ---

// function declaration:
sum a b = a + b;

// anonymous declaration:     
sum = (a b) = a + b;

// or anything else... 
```

如果你想创造一种新的编程语言，你更喜欢哪种风格？在我看来，参数之间不使用分号的代价更小。它们可以用空格分开。

> 这篇文章还没有结束，我会努力完成它。