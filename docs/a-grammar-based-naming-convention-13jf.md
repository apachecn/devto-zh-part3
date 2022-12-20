# 基于语法的命名约定

> 原文：<https://dev.to/somedood/a-grammar-based-naming-convention-13jf>

[![rachelsoderberg](img/14c43b3b4159992b0312f2ea70a8746e.png)](/rachelsoderberg) [## 编写好的方法和变量名

### 雷切尔·索德伯格 1915 年 5 月 3 日阅读

#programming #bestpractices #code #beginners](/rachelsoderberg/writing-good-method-variable-names-47il)

我最近读了一篇来自 [@rachelsoderberg](https://dev.to/rachelsoderberg) 的关于写好变量名意味着什么的文章。在她的文章中，她讨论了编写描述性变量名的许多策略和注意事项。

这绝对是一个很棒的读物，但是当我读完之后，我突然意识到在编程中完成这个看似平凡的任务是多么的困难。作为程序员，我们经常努力为变量命名，不是因为它本身就很难，而是因为我们必须确保我们选择的名称对下一个阅读我们代码的人(可能是也可能不是我们自己)来说是清晰的、描述性的和可维护的。

为了节省自己的时间和精力，我对所有项目都使用简单的命名约定。今天，我想把它分享给社区，这样我们就可以少花点时间考虑变量名了。

> 注意:我在文章中使用的代码示例是用 JavaScript 编写的，但是它们适用于任何编程语言，因为它们毕竟只是命名约定。

# 基本规则

所有变量、函数、参数和标识符都用`camelCase` ~~编写，除非你是一个 [Rustacean](https://www.rustaceans.org/)~~ 。常量写入`SCREAMING_CASE`。进行这种区分很重要，这样我们就可以知道哪些变量是不可变的，哪些是只读的。

在强烈鼓励(甚至强制)不可变变量的编程语言中，我们必须区分**不可变变量**和**真常量**。

任何不依赖于运行时可变性(如用户输入和其他动态值)的静态值都可以归类为真常数。例如， [`PI`](https://en.wikipedia.org/wiki/Pi) 的值被认为是真常数，因此必须写入`SCREAMING_CASE`。否则，`camelCase`用于表示可变和不可变的变量，这些变量存储临时变量、别名、计算和运行时可变性的输出。

```
// Immutable Variables
const userInput = document.getElementsByTagName('input')[0].value;
const hasDevto = /dev\.to/g.test(userInput);

// True Constants
const WEBSITE_NAME = 'dev.to';
const TAU = 2 * Math.PI; 
```

Enter fullscreen mode Exit fullscreen mode

不过，应该指出的是，背景很重要。区分不可变变量和真正常量的标准可以根据情况而改变。例如，如果在整个程序中将`userInput`视为一个静态值，可以使用`SCREAMING_CASE`来表示`userInput`(即使它可能在不同的设备上随运行时而变化)。最终，作为程序员，我们需要辨别哪些变量是我们希望作为不可变变量或真正的常量来交流的。

# 语义数据类型

数据类型传达了可以对某个变量执行什么方法和操作。因此，用类型系统来命名变量是我们最感兴趣的，特别是对于弱类型语言。这样做将有助于我们暗示变量可能具有的数据类型及其相应的方法、属性和操作。反过来，这导致更可读的代码。

### 数字、字符串和对象

在大多数情况下，数字、字符串和单个对象都用最合适的单数名词来命名。

```
const usernameInputField = document.getElementById('username-field');
const username = nameInputField.value;
const hypotenuse = Math.sqrt(a**2 + b**2);
const profileData = {
  name: 'Presto',
  type: 'Dog'
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 布尔

布尔的名字通常以是非问句的形式出现，就好像我们亲自询问布尔变量本身的状态一样。

```
// Yes-or-no questions
const isDog = true;
const hasJavaScriptEnabled = false;
const canSupportSafari = false;
const isAdmin = false;
const hasPremium = true;

// Functions or methods that return booleans
// are also named in a similar fashion
function isOdd(num) { return Boolean(num % 2); } 
```

Enter fullscreen mode Exit fullscreen mode

### 数组和集合

数组等类似集合的数据结构(如[`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)[`Set`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set))用`camelCase`中最合适的*复数*名词命名。如果名词的*复数*和*单数*形式看起来过于相似，我们可以用适当的*集合*名词替换*复数*形式。这样，这些名词的相应*单数*形式可以在迭代过程中用作变量名。

```
// We use plural or collective nouns for arrays.
const dogs = [ 'Presto', 'Lucky', 'Sparkles' ];

// We can use the singular form of the
// variable name of the array
// in callback functions.
dogs.forEach(dog => console.log(dog));

// We can also use it in `for...of` loops.
for (const dog of dogs)
  console.log(dog);

// Here, we can use collective nouns
// for better readability.
const herdOfCows = [ 'Bessie', 'Bertha', 'Boris' ];
herdOfCows.forEach(cow => console.log(cow));
for (const cow of herdOfCows)
  console.log(cow); 
```

Enter fullscreen mode Exit fullscreen mode

### 功能

编写函数的目的是将它们与动作相关联。这就是为什么它们通常由两部分组成:[一个**及物动词**和一个**直接宾语**](http://www.chompchomp.com/terms/transitiveverb.htm) 。换句话说，函数的名字通常采用`verb + noun`的形式。这告诉我们，名称是一个命令，或者说是一个函数，我们可以随时调用它。

```
function getSum(a, b) { return a + b; }
function findBanana(str) { return str.indexOf('banana'); }
function getAverage(numbers) {
  const total = numbers.reduce((prev, curr) => prev + curr);
  return total / numbers.length;
} 
```

Enter fullscreen mode Exit fullscreen mode

[PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview) ，相当于 Linux 中的 [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) ，是一个很好的例子，它是一种语言，这种语言为函数强制执行这种命名约定(或者在语言中称为 [`cmdlet` s](https://docs.microsoft.com/en-us/powershell/developer/cmdlet/cmdlet-overview) )。

下面的脚本计算了所有当前运行的 Chrome 进程的总内存分配。语法不是最友好的，但是 PowerShell 对其`cmdlet` s 的`verb + noun`约定的强制是显而易见的。下面的例子只使用了 [`Get-Process`](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-process) 、 [`Where-Object`](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object) 和 [`Measure-Object`](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/measure-object) `cmdlet` s，但是请放心，PowerShell 提供的其他`cmdlet` s 都遵循这种命名约定。[本站将其全部列出，以供参考。](https://www.pdq.com/powershell/)T15】

```
# Get all processes currently running  $processes  =  Get-Process;  # Filter to retrive all Chrome processes  $chromeProcesses  =  $processes  |  Where-Object  {  $_.ProcessName  -eq  'chrome'  }  # Sum up all of the memory collectively  # allocated for the Chrome processes  $memoryUsage  =  $chromeProcesses  |  Measure-Object  WorkingSet64  -Sum;  # Log the result to the console  "{0:F2} MB used by Chrome processes."  -f  ($memoryUsage.Sum  /  1mb); 
```

Enter fullscreen mode Exit fullscreen mode

### 类

在`PascalCase`中用适当的*专有*名词给类命名。这告诉我们，这个变量不像我们程序中任何其他遵循`camelCase`命名约定的变量；相反，它是一个特殊的变量，存储具有特殊属性和方法的用户定义类型。

```
class User { }
class Admin extends User { }
class Moderator extends Admin { }
class Player extends User { } 
```

Enter fullscreen mode Exit fullscreen mode

### 类字段和方法

类字段是根据前面讨论的不变性和数据类型约定来命名的。

另一方面，类方法的命名方式与函数相似。他们仍然使用`verb + noun`约定，但是在某些情况下，他们可以省略名称中的**直接宾语**(名词)部分。**及物动词**(动作)的执行者因此被暗示为拥有所述对象方法的类的对象实例。

```
// Class
class Player {
  constructor(name) {
    // String
    this.username = name;

    // Number
    this.level = 100;

    // Boolean
    this.isAdmin = false;

    // Array
    this.weapons = [
      'bow',
      'sword',
      'spear'
    ];
  }

  // Class Method (with noun)
  initiateBattle() { }

  // Class Method (without noun)
  attack() { }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 把一切都包起来...

```
const TRUE_CONSTANT = Math.PI;
const stringName = '';
const numberName = 0;
const isBooleanName = true;
const objName = { };
const arrayNames = [ ].map(name => name);
function getFunctionName() { }
class ClassName { } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段简洁地总结了我的整个命名约定。很明显，英语的语法规则和语义极大地影响了这一惯例。拥抱它们并以某种方式将它们与编程联系起来，使得命名变量和暗示它们的数据类型的行为比以往任何时候都更加直观。

如果我们愿意，我们可以简单地在所有变量前面加上其数据类型的缩写——类似于用形容词来描述名词——但是这样做的话，变量名会变得非常冗长，这是我们不希望看到的，如下例所示。我们最好对显式类型注释使用 TypeScript。

```
// This is... eww. ❌
const NUM_TAU = 2 * Math.PI;
const str_Username = 'Some Dood';
const num_Hypotenuse = Math.sqrt(num_A**2 + num_B**2);
const boo_AdminStatus = false;
const obj_ProfileData = { };
const arr_Articles = [ ];
function fun_GetUser() { }
class Cls_Class { } 
```

Enter fullscreen mode Exit fullscreen mode

举个例子，命名变量是除了调试之外编程~~最令人沮丧的方面之一。遵循基于语法的命名惯例肯定会使它不那么令人不快。感谢这种命名约定的语言学渊源，我们将能够编写更直观、更易读的代码(默认为，这总是一件好事。当然，我们仍然必须确保我们软件的设计和架构本质上不是“糟糕的”，但至少我们可以放心，我们的代码对于接下来可能从事它的任何人来说确实是直观和可读的。~~

一种编程语言**被称为**语言**是有原因的...**