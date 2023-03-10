# PHP 语言构建块

> 原文：<https://dev.to/kluxury/php-language-building-block-18km>

PHP 构建块包括变量、运算符、数据类型、常量和文字。

## PHP 与其同时代的人相比

与 Ruby on rails 相比，PHP 更容易学习，允许简单地完成基本的事情，而且它没有 MVC 那么严格。不利的一面是，由于它不是严格的 MVC，它不鼓励好的实践。

## PHP 安装

在编写和测试 PHP 脚本之前，您需要一样东西——服务器！，因为 PHP 是一种服务器端脚本语言，所以你要么从一家支持 PHP 的托管公司那里获得一些网络空间，要么让你的计算机假装它安装了服务器。这是因为 PHP 不是在你的 PC 上运行的——它是在服务器上执行的。然后，结果被发送回客户端 PC(您的计算机)。

#### 苹果用户

如果你有 OS X，那么试试这些网站来启动和运行 PHP:

[Onlamp.com](http://www.onlamp.com/pub/a/mac/2001/12/07/apache.html)

[entropy.com](http://www.entropy.ch/software/macosx/php/)

您现在要做的是启动并运行 apache 服务器，这样您就可以离线运行 PHP 脚本。请特别注意文件存储的位置，以及“localhost”地址。

#### Linux 用户

有很多网站可以帮助 Linux 用户使用 Apache 服务器和 PHP。这里有三个值得一看的网站:

[维基](http://en.wikipedia.org/wiki/LAMP_(software_bundle))

[Php 和 MySQL 教程](http://www.php-mysql-tutorial.com/wikis/php-tutorial/installing-php-and-mysql.aspx)

[php 怪胎](http://www.phpfreaks.com/tutorials/12/0.php)

#### Windows 用户

下载 Xampp、Lampp 或 Wampp(推荐)并遵循安装过程。

## PHP 分隔符

PHP 分隔符只不过是用来封闭 PHP 脚本的开始和结束标记。当且仅当 PHP 代码用这些分隔符括起来时，它才可以被解析。

```
<?php 
//-------code goes here---
?> 
```

Enter fullscreen mode Exit fullscreen mode

## 用 PHP 进行变量初始化

变量由您选择的名称组成，前面有一个美元符号($)。变量名可以包括字母、数字和下划线字符(_)。它们不能包含空格。它们必须以字母或下划线开头。下面的代码定义了一些合法的变量:

```
<?php 
$man;
$a_long_name;
$N123;
?> 
```

Enter fullscreen mode Exit fullscreen mode

## PHP 数据类型

PHP 允许八种不同的数据类型。所有这些都在下面讨论。前五种称为简单数据类型，后三种是复合数据类型:

**整数**:整数只保存整数，包括正数和负数，即没有小数部分或小数点的数字。它们可以是十进制(基数为 10)、八进制(基数为 8)或十六进制(基数为 16)。默认基数是十进制(基数为 10)。八进制整数可以用前导 0 声明，十六进制可以用前导 0x 声明。整数范围必须介于-2^31 和 2^31.之间

```
<?php 
$Age = 31;
//31 here is an integer
?> 
```

Enter fullscreen mode Exit fullscreen mode

**Double** :可以保存包含分数或小数部分的数，包括正数和负数。默认情况下，变量添加最小数量的小数位数。

```
<?php 
$pi = 3.1428;
?> 
```

Enter fullscreen mode Exit fullscreen mode

**字符串**:保存字母或任何字母，包括偶数。这些在声明时用双引号括起来。字符串也可以写在单引号内，但是在打印变量时会被区别对待。

```
<?php 
$name = "Adekunle";
?> 
```

Enter fullscreen mode Exit fullscreen mode

**NULL** :这些是特殊类型的变量，只能保存一个值，即 NULL。我们遵循以大写形式书写的惯例，但是区分大小写。

```
<?php 
$Size = null;
?> 
```

Enter fullscreen mode Exit fullscreen mode

**Boolean** :只保存两个值，真或假。成功的事件将返回 true，不成功的事件将返回 false。在布尔值中，空类型值也被视为 false。除了 NULL 之外，0 在布尔运算中也被认为是假。如果字符串为空，则在布尔数据类型中它也被视为 false。

```
<?php 
$Admin = true;
?> 
```

Enter fullscreen mode Exit fullscreen mode

**数组**:数组是一种复合数据类型，可以存储同一数据类型的多个值。

```
<?php 
$odds = array(1, 3, 5, 7, 9);
?> 
```

Enter fullscreen mode Exit fullscreen mode

**对象**:对象被定义为用户定义的类的实例，可以保存值和函数。这是一个高级主题，将在后续文章中详细讨论。
**资源**:PHP 中的资源不是一个确切的数据类型。这些基本上用于存储对一些函数调用或外部 PHP 资源的引用。例如，考虑一个数据库调用。这是外部资源。

## PHP 常量

当你需要一个在整个脚本中不变的变量时，你需要一个常量。必须使用 PHP 内置的 define()函数来创建一个常量。完成此操作后，该常量将无法更改。要使用 define()函数，必须将常量的名称和要赋予它的值放在调用的括号内。这些值必须用逗号
隔开

```
<?php 
define("Username", "Alchemist");
?> 
```

Enter fullscreen mode Exit fullscreen mode

## PHP 操作符

运算符是一个符号或一系列符号，当与值结合使用时，它执行一个操作并通常产生一个新值。

### 操作员的类型

#### 赋值运算符

它由单个字符=组成。赋值运算符取其右边操作数的值，并将其赋值给左边的操作数:

```
<?php 
$Age = 21;
?> 
```

Enter fullscreen mode Exit fullscreen mode

#### 结论运算符

串联运算符由单个句点表示。将两个操作数都视为字符串，它将右边的操作数追加到左边。

```
<?php 
$firstname = "Adekunle";
$lastname = "Afolabi";
name = echo($firstname . $lastname);
?> 
```

Enter fullscreen mode Exit fullscreen mode

#### 算术运算符

算术运算符完全符合您的预期。他们执行算术运算。加法运算符(+)将右操作数与左操作数相加。减法运算符(-)从左边减去右边的操作数。除法运算符(/)将左边的操作数除以右边的操作数。乘法运算符(*)将左边的操作数乘以右边的操作数。模数运算符(%)返回左操作数除以右操作数的余数。

#### 比较运算符

比较运算符对其操作数执行测试。如果测试成功，它们返回布尔值 true，否则返回 false。

```
<?php 
// == Represents "equivalent to"
// != Represents "not equivalent to"
// === Represents "identical to"
// !== Represents "not identical to"
// < Represents "less than"
// <= Represents "less than or equal to"
// > Represents "greater than"
// >= Represents "greater than or equal to"
?> 
```

Enter fullscreen mode Exit fullscreen mode