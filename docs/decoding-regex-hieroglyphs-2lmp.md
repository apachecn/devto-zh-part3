# 解码正则表达式象形文字

> 原文：<https://dev.to/billywitherspoon/decoding-regex-hieroglyphs-2lmp>

曾经在键盘上敲击来测试一个表单吗？见过加密密码吗？正则表达式使这些字符串看起来像海明威。正则表达式(regex)是无意义的，非语法的，只是简单的胡言乱语。或者他们是...

# 什么是正则表达式？

正则表达式是定义搜索模式的字符序列。通过设置这种搜索模式，您可以解析文本来查找与该模式匹配的字符集。正则表达式是一个非常强大的工具开发工具。

# 字符

### 字符字面量

字符文字是具有单个值的单个字符。例如，`b`、`N`、`8`和`~`都是字符文字。想象一下，在键盘上键入一个单字符键，并期望该键被赋予一个值。

##### 排序字符字面量

使用这些字符文字，您可以对它们进行排序，以创建单词、句子甚至段落等内容。`Spaghetti`是一个字符序列，就像`Mom's Spaghetti`一样(空格也是字符！)

##### 在正则表达式中使用字符文字

假设我们想找到一个字符序列。具体来说`JoeSmith@gmail.com`。实现这一点的一种方法是在正则表达式中使用字符。

让我们看看 javascript 中的一个例子，我们想要隐藏两个用户的电子邮件。

我们定义了一个名为`hideEmails`的函数，它将接受一个字符串参数，并使用 replace 插入`#####`来代替该字符串中的两封电子邮件。在 javascript 中,`replace`函数可以用来完成这个任务。

`string.replace(/regex/g, replacement string)`

正则表达式总是用`/forward slashes/`包装。可以在斜线`/regex/g`之外附加`g`标志，以全局替换整个字符串。

[https://repl.it/@billywitherspoo/character-literal?lite=true](https://repl.it/@billywitherspoo/character-literal?lite=true)

我们使用第一个正则表达式`/JoeSmith@gmail.com/`来搜索`JoeSmith@gmail.com`，并成功地用`#####`替换它！

注意，对于电子邮件`NancySmith@gmail.com`，替换函数无法将我们的第二个正则表达式`/nancysmith@gmail.com/`匹配到`exampleSentence`中的任何文本。这是因为`n`和`s`与`N`和`S`是不同的字符文字。

### 元字符

虽然字符文字很好也很简单，但是它们没有提供非常广泛的功能。输入元字符，这是正则表达式的构造块。

元字符的范围比字符字面量大得多。它们是可以匹配多个字符的特殊字符。

##### 语法

因为字符文字通常使用单个字符，所以元字符通常以前面的`\`生成

一些常见的例子有:

`\w`A-Z、A-Z 或 0-9 的任意字符
T1】任意数字 0-9
T2】任意空格(可以是多个空格)

它们也可以大写以产生相反的效果:

`\W`不是 a-z、A-Z 或 0-9 的任何字符【符号】
T1】任何非数字【that】任何非空格

[https://repl.it/@billywitherspoo/RichElatedMacro?lite=true](https://repl.it/@billywitherspoo/RichElatedMacro?lite=true)

请注意`\d`是如何替换 2 的，而`\D`是如何替换除 2 之外的所有内容的。

### 量词

量词是修饰文字和元字符的元字符。它们决定了你希望匹配多少个字符，或者这个字符应该在哪里匹配。它们通常放在正则表达式中的字符后面。

##### 一般量词

`*`如果一个字符出现 0 次或更多次则匹配
`+`如果一个字符出现 1 次或更多次则匹配
`?`如果一个字符出现 0 次或 1 次则匹配
`{min,max}`匹配一定范围内的字符出现
`{min,}`匹配最少的字符出现
`{,max}`匹配最多的字符出现
`{n}`匹配精确的字符出现次数

[https://repl.it/@billywitherspoo/CloudyCanineTrust?lite=true](https://repl.it/@billywitherspoo/CloudyCanineTrust?lite=true)

`-`字符后面的`?`表示`JoeSmith@gmail.com`和`Joe-Smithgmail.com`都匹配，因为`-`字符在序列中的那个位置出现了 0 次或 1 次。

##### 位置量词

`^`匹配出现在行首的字符(置于字符之前)
`$`匹配出现在行尾的字符
`\b`匹配出现在单词边界内的字符(置于字符前后)

*注意:如果您想搜索元字符的字符文字，如`* `simply escape it as so` *`*

### 人物类

字符类出现在`[brackets]`内。它们允许用户指定多个文字字符进行匹配。

`^`可以像 bang 运算符一样用在字符类的开头。

`[a-z]`是一组字符 A 到 z
`[A-Z]`是一组字符 A 到 Z
`[0-9]`是一组字符 0 到 9

[https://repl.it/@billywitherspoo/RotatingAbsolutePolyhedron?lite=true](https://repl.it/@billywitherspoo/RotatingAbsolutePolyhedron?lite=true)

在这里，一组元音作为字符文字输入，并用#替换。

然后，将胡萝卜`^`添加到正则表达式中，只删除非元音字母。

<figure>[![](img/49873f0715d5cbb3e564ddb9dcb595ce.png)](https://i.giphy.com/media/QVyK1uJsHVQ6k/giphy.gif) 

<figcaption>关于 Regex 的感受 Rad</figcaption>

</figure>

# 现在把它集合起来

让我们研究一个电子邮件正则表达式。快速看一下这个非常基本的例子。

`[\w-]+@[\w-]{2,}\.[\w-]{2,}`

一开始有点吓人，但是我们已经涵盖了它涉及的所有内容，所以让我们来分解一下。

一封电子邮件有以下组成部分:[local-part @ domain . domain-extension](mailto:local-part@domain.domain-extension)

这可以划分如下:

1.  局部零件
2.  @
3.  领域
4.  。
5.  领域扩展

### 1。局部零件

我们知道电子邮件的第一部分是一组字符 a-z，A-Z，0-9。我们可以用元字符`\w`来解决这个问题

`\w`

然而，我们知道一些电子邮件名称中有一个破折号。所以我们需要将一个`-`字符和一个`\w`元字符结合起来。我们可以用一个[角色类]做到这一点。

`[\w-]`

太好了。现在我们需要指定我们期望的这些字符的数量。在这种情况下，我们希望在@之前有一个或多个字符。对于这个动作来说,`+`量词是一个很好的选择。

`[\w-]+`

很好，我们已经完成了电子邮件正则表达式的第一部分。

### 2。@

第二部分很简单。我们知道`@`字符字面量将毫无例外地出现。

`@`

### 3。领域

在这里，我们可以使我们的模式类似于我们在本地部分所做的。

`[\w-]+`

但那有点无聊。假设我们知道所有的域名至少有两个字母。我们可以用`{2,}`代替`+` 1 或更多的量词，表示我们知道至少会有两个字符。

`[\w-]{2,}`

### 4。。

又一个简单的！字符字面量

`.`

等等，那是元字符。确保用`\`来转义它

`\.`

更好！

### 5。领域扩展

我们可以对长度做相同的假设，并重用我们为域做的模式。

`[\w-]{2,}`

### 测试出来

请击鼓。我们最终的正则表达式已经准备好了！

`[\w-]+@[\w-]{2,}\.[\w-]{2,}`

[https://repl.it/@billywitherspoo/SafePristineStatistics?lite=true](https://repl.it/@billywitherspoo/SafePristineStatistics?lite=true)

漂亮！它能够在文本中搜索并抓取我们期望的电子邮件。它还跳过了字符和结构与电子邮件相似但与我们的正则表达式不匹配的其他序列。当然还有改进的空间，所以我鼓励每个人自己去尝试！

我最喜欢的一些正则表达式资源:

*   [Youtube 上的编码列车](https://www.youtube.com/watch?v=7DG3kCDx53c&t=2s)

*   Rubular，一个基于 Ruby 的正则表达式测试器

*   您的内置 IDE！尝试 Ctrl + F 或 Cmd + F 并单击*选项来测试按正则表达式搜索。