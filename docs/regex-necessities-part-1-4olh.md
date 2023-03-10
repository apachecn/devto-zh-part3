# 正则表达式必需品第 1 部分

> 原文：<https://dev.to/kbrock84/regex-necessities-part-1-4olh>

## TL；速度三角形定位法(dead reckoning)

正则表达式比你想象的要简单。让我们通过 JavaScript 中的例子来关注快速和肮脏的参考中最基本的需求。如果您是正则表达式的新手，或者需要快速回顾一下基础知识，这是一个很好的起点。我们将在第 2 部分更深入地研究用例。

## 什么是 Regex？

正则表达式是一种用于匹配文本模式的简洁语言。在 JavaScript 中，正则表达式可以这样定义`/expression/`。正则表达式最基本的形式如下:

```
/pizza/.test(entree); 
```

*提示:如果你想继续玩这些表达，我发现 [Regex Pal](https://www.regexpal.com/) 非常有用*

在上面的例子中，如果`entree`变量在字符串中的任何地方包含了`pizza`，它将返回 true。还真会举一些例子:`pizza` `pizzas` `spizzas` `i like to eat pizza`。

## 不惧正则

如果你没有使用正则表达式的经验，它可能看起来很吓人。如果你能写代码，你就能学习正则表达式。这只是语法，几乎每种语言都支持它们。我几乎每天都使用正则表达式来搜索文件。你知道，那些有人在找的文件，在那个很多人都接触过的服务器上。如果没有合适的工具，这可能会令人抓狂。

然而，在某些情况下，你应该直接去图书馆。一个臭名昭著的例子是验证电子邮件。如果正则表达式变得太复杂，调试和维护会变得很困难。所以如果可以的话，让别人来做吧。

好的，让我们先看看操作符。请记住，这不是一个全面的列表。刚够开始。我会在最后列出资源。

## 运算符

有几个操作员必须知道。掌握了这些，你就涵盖了大部分用例。我将从一个例子开始，然后解释发生了什么。

需要注意的是，以下示例是区分大小写的。表达式`/B/`不同于表达式`/b/`。这可以用一个*修饰符*来改变，我们将在后面讨论。

### 百搭牌`*`:

```
/cat*/.test("category");
// returns true 
```

最简单的操作符`*`匹配任意长度的任意字符或字符组合。让我们看一些匹配和不匹配的例子。

```
const matchRed = /red*/;
matchRed.test("reddit"); // returns true
matchRed.test("credit"); // returns true
matchRed.test("brew"); // returns true
matchRed.test("book"); // returns false 
```

这里发生了什么？通配符将匹配在找到组合`re`之后的文本**的任何组合。但是等等！我们想匹配`red`而不是`re`。我们如何补救？`*`使最后一个字符成为可选字符，所以它可以匹配任何内容。我们可以使用`.`操作符向表达式添加一个额外的字符，如下所示:**

```
/red.*/.test("brew"); //returns false 
```

更多信息请见下面的`.`。

### 圆点`.`

```
const matchAt = /.at/;
matchAt.test("cat"); // returns true 
```

`.`操作符匹配任意字符一次。在上面的例子中，它匹配 cat。让我们看一些其他的例子。

```
const matchAt = /.at/
matchAt.test("rat"); // returns true
matchAt.test("bat"; // returns true
matchAt.test("#at"); // returns true
matchAt.test("at"); // returns false 
```

好吧。这是怎么回事？`rat`、`bat`和`#at`匹配，因为我们指定了任何一个带有`.`和字母`at`的单个字符。只是字母`at`失败是因为字母前面没有字符。

### 字符`\w`

```
const matchAt = /\wat/;
matchAt.test("cat"); // returns true 
```

*几乎与`.`操作符相同的*,`\w`操作符匹配任意一个*字*字符一次。这意味着它将匹配任何字母或数字(还带有下划线`_`)。

```
const matchAt = /\wat/
matchAt.test("rat"); // returns true
matchAt.test("bat"; // returns true
matchAt.test("#at"; // returns false
matchAt.test("at"); // returns false 
```

### 数字`\d`

```
const matchNumber = /number\d/;
matchNumber.test("number1"); // returns true 
```

在上面的表达式中，我们将匹配任何包含字母`number`后跟任何数字的内容，因此有了`\d`操作符。这里还有几个例子。

```
const matchNumber = /number\d/;
matchNumber.test("number9"); // returns true
matchNumber.test("somenumber8plus"); // returns true
matchNumber.test("4"); // returns false 
```

所以即使序列`number8`在字符串中间，也会返回 true。

### 字界`\b`

```
const matchMoo = /\bmoo\b/;
matchMoo.test("the cow says moo"); // returns true 
```

`\b`操作符匹配单词的边界。牛只会哞哞叫。就是这样。如果我们再测试几个案例:

```
const matchMoo = /\bmoo\b/;
matchMoo.test("I have a moo cow"); // returns true
matchMoo.test("the cow says moot"); // returns false
matchMoo.test("the cow says smoo"); // returns false 
```

很简单。如果单词`moo`在句子中的任何地方，并且只有这个单词，它将返回 true。

### 人物类`[]`

```
const matchThese = /[1Aa]/;
matchThese.test("pineapple"); // returns true 
```

字符类是 regex 的一个强大部分。在上面的例子中，任何包含`1`、`A`或`a`的单词都将返回 true。很直接，对吧？这些例子怎么样？

```
/[a-e]/.test("banana"); // returns true
/[A-E]/.test("banana"); // returns false
/[1-5]/.test("The number 3"); // returns true 
```

在字符类中，我们可以定义数字、小写字符或大写字符的范围。你甚至可以把它们结合起来。

```
const matchThis = /[a-eA-E1-5]/;
matchThis.test("banana"); // returns true
matchThis.test("BANANA"); // returns true
matchThis.test("3"); // returns true 
```

现在我们有一个正则表达式来匹配所有包含字母 a 到 e 和数字 1 到 5 的单词。

### 开始`^`

```
/^a/.test("apple"); //returns true 
```

`^`代表一个字符串的开始。让我们看几个返回 false 的例子。

```
/^a/.test("pineapple"); //returns false
/^a/.test("eat apples"); //returns false 
```

这些失败是因为字符串没有以`a`开头。

### 结束`$`

```
/e$/.test("apple"); // returns true 
```

`$`代表一个字符串的结尾，因为`apple`以`e`结尾，所以它返回 true。很简单，但是这里有一些失败的案例需要说明。

```
/e$/.test("eat"); // returns false
/e$/.test("street"); // returns false 
```

这两个案例都失败了，仅仅是因为它们没有以`e`结束。

## 修饰语

修饰符与匹配模式关系不大，但有助于描述您希望如何搜索。在 JavaScript 中，我们总共有 6 个修饰符，但是 3 个必须知道的修饰符是`i`、`m`和`g`。这些修饰符可以单独使用，也可以任意组合使用，放在最后一个`/`之后，就像这个`/expression/m`一样，其中`m`是修饰符。

### 无视案件`i`

```
/c/i.test("Coffee"); // returns true 
```

这一个是相当自明的。将`i`修饰符放在表达式之后，允许我们匹配任何包含小写`c`和大写`C`的内容。

```
const matchBat = /bat/i;
matchBat.test("WOMBAT"); // returns true
matchBat.test("Bat"); // returns true 
```

### Multiline `m`

```
const groceryList = `apples
oranges
peaches
coffee
chocolate`;

/coffee/m.test(groceryList); // returns true 
```

multiline 修饰符允许正则表达式查看任何新行之外的内容。如果没有它，上面的例子将返回 false。

### 全局`g`

```
"apple".replace(/p/g, "b"); // returns abble 
```

使用 JavaScript `String.replace`的一个好方法是使用正则表达式。然而，如果你想替换**匹配模式的所有**出现，你需要包含 to `g`修饰符来进行全局搜索。如果没有它，上面的表达式只会替换它找到的第一个`p`。

```
"apple".replace(/p/); // returns abple 
```

#### 目前就这些。请继续关注 *Regex 必需品第 2 部分*，在这里我们将更深入地研究用例，把它们放在一起。

### 资源

[正则表达式对象 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp)

[正则表达式引用 w3schools](https://www.w3schools.com/jsref/jsref_obj_regexp.asp)

[Regex Pal](https://www.regexpal.com/)