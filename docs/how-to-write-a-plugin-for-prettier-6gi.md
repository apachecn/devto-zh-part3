# 如何为 beauty 写一个插件

> 原文：<https://dev.to/fvictorio/how-to-write-a-plugin-for-prettier-6gi>

*原贴[此处](https://medium.com/@fvictorio/how-to-write-a-plugin-for-prettier-a0d98c845e70)。*

在这篇文章中，我将向你展示如何为[beautiful](https://prettier.io/)构建一个非常简单的插件。您将学习如何构建、测试，当然，还有编写插件。我远不是这方面的专家，但是我通过对 [Solidity 插件](https://github.com/prettier-solidity/prettier-plugin-solidity/)的贡献学到了很多，并且注意到除了官方文档之外，似乎没有太多关于这个主题的资料。

我们将使用 [TOML](https://github.com/toml-lang/toml) 作为示例语言。我选择 TOML 是因为它的语法简单，而且据我所知，没有比它更好的插件了。结果不会是一个可用的插件，正如你将看到的，但是如果幸运的话，你将学到足够的东西来开发一个合适的插件。

你为什么想学这个？嗯，有两个强有力的原因。首先，你可以为任何还不被支持的语言创建一个插件(比如 TOML 和 Dockerfile，但是可能还有很多其他的)。第二，你将能够为现有的[插件](https://prettier.io/docs/en/plugins.html#official-plugins)做出贡献。此外，由于 Prettier 本身的核心是使用插件 API 编写的，如果你愿意，你甚至可以贡献给主存储库。

## 比较漂亮的作品如何？

就其核心而言，Prettier 所做的事情非常简单:它获取一些代码(一个字符串)，将其转换为 AST(抽象语法树，代码的一种表示)，然后仅使用 AST 打印代码。这意味着原始代码的风格(几乎)完全被忽略了。可以在[原博文](https://jlongster.com/A-Prettier-Formatter)了解更多。

就我们的目的而言，重要的是我们需要一个将代码转换成 AST 的解析器，以及一个接受并输出它的函数。我们的初始设置已经使用 toml-node 配置了解析器，所以我们只需要担心打印机功能。

## 设置

首先，我们将克隆[这个存储库](https://github.com/fvictorio/prettier-plugin-toml)，它拥有您需要启动的所有样板文件。我很快会解释它的内容。克隆之后，转到顶层目录并运行`npm install`来安装依赖项。现在，您应该能够使用以下命令运行示例文件(`example.toml`)中的插件:

```
./node_modules/.bin/prettier --plugin . example.toml 
```

Enter fullscreen mode Exit fullscreen mode

对此也有一个 npm 脚本，所以`npm run example`应该也能工作，但是这显示了如何在你想要的任何文件中运行插件。

运行该命令后，您不会看到任何输出，这也没关系。目前，我们的插件没有发出任何东西:当打印机函数接收到 AST 时，它只是返回一个空字符串。

还有一个可以用`npm test`运行的初始测试。我们将使用[快照](https://jestjs.io/docs/en/snapshot-testing)开玩笑地编写测试，但是因为设置已经存在，你唯一要做的就是添加新的夹具。该初始测试将格式化`tests/StringAssignements/example.toml`的内容，并将结果与快照中的预期输出进行比较。我们所有的测试都会像这样:一个 TOML 文件和一个正确格式的快照。这个测试当然会失败，但我们的首要目标是让它通过。

我们要写的所有代码都在`src/index.js`文件中。事实上，一切都将在一个函数中:`printToml`。你可以看看文件的其余部分，但不要担心细节。如果你很好奇，这里的[都有解释](https://github.com/prettier/prettier/blob/master/docs/plugins.md#developing-plugins)。

如果你想读完成的代码而不是写它，只需检查`finished`分支。

## 打印机功能

printToml 函数非常简单。它需要三个参数:

*   `path`，表示 AST 中的一个节点
*   `options`，它表示给 prettier 的配置(除了别的以外，`.prettierrc`和给命令的标志的组合)
*   这就是我们递归调用打印机函数的方式

请注意，我说过`path`是 AST 中的某个节点，而不是根节点。这是因为函数是递归调用的。例如，如果我有一个函数体，我可能想把每个单独的语句分别打印出来，然后用这个结果做一些事情。随着我们继续下去，这一点会变得更加清楚。

这是我们函数的样板:

```
function printToml(path, options, print) {
  const node = path.getValue()

  if (Array.isArray(node)) {
    return concat(path.map(print))
  }

  switch (node.type) {
    default:
      return ''
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一行只是从`path`中提取 AST 节点。这是因为`path`有一些与 AST 节点相关的额外信息和逻辑。

然后我们有一个奇怪的块来检查节点是否是一个数组。这只在初始调用中是必要的，因为我们使用的解析器将代码表示为节点列表，而不是节点树。不要担心这一点，但请记住，因为稍后这将对我们的插件施加一些严重的限制。

最后，我们有了开关。这里是我们将花费大部分时间的地方。我们的逻辑非常简单:我们检查 AST 节点的类型并相应地采取行动。我们开始填吧。

## 简单的分配

如果您看一下我们的测试，您会看到它包含两个键/值对。代表第一对的节点是这样的:

```
{
  type: 'Assign',
  value: {
    type: 'String',
    value: 'TOML Example',
    line: 1,
    column: 9
  },
  line: 1,
  column: 1,
  key: 'title'
} 
```

Enter fullscreen mode Exit fullscreen mode

(我们是怎么知道这个的？有很多方法可以获得它:一个很好的旧版本`console.log`，使用节点 REPL 中的解析器，或者使用 [ndb](https://github.com/GoogleChromeLabs/ndb) 运行插件并检查值。)

这里有两件有趣的事情。首先是`type`属性，这是我们在开关中使用的。第二个是，我们对的`key`是一个简单的字符串，*我们的值是另一个 AST 节点*，它的类型是`String`。

所以我们要做的第一件事是为`Assign`节点添加一个子句:

```
case 'Assign':
  return concat([node.key, ' = ', path.call(print, 'value'), hardline]) 
```

Enter fullscreen mode Exit fullscreen mode

这里有很多东西要解开，但主要思想很容易理解:我们告诉更漂亮的是，一个赋值是通过连接四个东西打印出来的:

*   钥匙。记住这只是一个普通的字符串
*   用空格填充的文字等号
*   打印输出赋值的结果，不管它是什么
*   还有一个`hardline`

什么是`concat`和`hardline`？它们被称为**构建器**，它们是我们用来构建我们想要的结果的功能和价值——由 beauty 公开。我们已经导入了`concat`，但是我们需要将`hardline`添加到我们正在使用的构建器列表中:

```
const {
  doc: {
    builders: { concat, hardline }
  }
} = require('prettier') 
```

Enter fullscreen mode Exit fullscreen mode

构建器很容易理解:它告诉更漂亮的只是连接它给出的部分列表。而`hardline`只是“放一个断行”的意思，不管怎么样。你可以在这里看到建筑商[的完整名单](https://github.com/prettier/prettier/blob/master/commands.md)。

那`path.call(print, 'value')`部分呢？这是一个更漂亮的习语，它只是意味着“递归调用打印机函数，使用`'value'`键中的节点”。为什么我们不能只做`print(node.value)`？好吧，记住打印机函数期望一个路径，也就是一个包装的节点，而不是一个节点。所以你必须这样做。

如果我们只添加这个并运行我们的测试，它将失败。diff 告诉我们键和等号是打印出来的，但值不是。这是有意义的，因为这些值是类型为`String`的节点，而我们还没有一个这样的子句。幸运的是，该条款非常简单。再看一下 AST 子节点，看看能不能猜出来。

是的，就这么简单:

```
case 'String':
  return concat(['"', node.value, '"']) 
```

Enter fullscreen mode Exit fullscreen mode

您可能只猜到了`return node.value`，但那是错误的，因为在那种情况下，我们将只打印字符串的内容，而不是完整的字符串。例如，`foo = "bar"`会被打印成`foo = bar`。

如果我们再次运行我们的测试，它应该通过了。

## 增加对其他值的支持

除了字符串，TOML 还支持其他数据类型，我们也应该支持它们。如果您查看根目录中的示例，您会看到它包含数字、布尔值、日期和列表。

数字和布尔很简单:

```
case 'Integer':
  return node.value.toString()
case 'Boolean':
  return node.value.toString() 
```

Enter fullscreen mode Exit fullscreen mode

我们必须将它们转换成字符串，因为这是 prettier 所期望的，但仅此而已。

日期有点复杂，这里我们将遇到我们使用的解析器的第一个限制。下面是日期赋值的 AST 表示:

```
{
  type: 'Assign',
  value: {
    type: 'Date',
    value: 1979-05-27T15:32:00.000Z,
    line: 5,
    column: 7
  },
  line: 5,
  column: 1,
  key: 'dob'
} 
```

Enter fullscreen mode Exit fullscreen mode

看日期的价值。那是一个`Date`对象，一个日期的唯一表示。但是如果你看一下 [TOML 规范](https://github.com/toml-lang/toml)，你会发现你可以用许多不同的格式指定日期。这在解析过程中会丢失，所以我们总是用相同的表示打印日期。

```
case 'Date':
  return node.value.toISOString() 
```

Enter fullscreen mode Exit fullscreen mode

那一点都不好！但是要正确地完成它，我们应该知道日期的原始表示。我们可以使用节点的位置和原始文本(我们在`options.originalText`中接收到的)来获取它，但是如果有一个解析器在 AST 中保存原始值，那就更好了。因为我们的解析器不这样做，所以我们只能满足于此。

## 表格

在 TOML 中，我们可以用规范中所谓的“表格”来分隔不同的部分，但是我们的解析器会分配类型`ObjectPath`。AST 节点如下所示:

```
{
  type: 'ObjectPath',
  value: [ 'owner' ],
  line: 3,
  column: 1
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，节点的值不是一个字符串，而是一个数组。这是因为我们可以拥有像`[servers.alpha]`这样的嵌套部分。我们用下面的子句打印它:

```
case 'ObjectPath':
  return concat(['[', node.value.join('.'), ']', hardline]) 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么新鲜的。我们用句点将值的每个部分连接起来，并用方括号将所有内容括起来。

## 数组

到目前为止，我们所做的一切都非常简单。数组稍微复杂一点，我们必须做出一些决定。有几种方法可以打印一个数组，例如:

```
arr1 = [1, 2, 3]
arr2 = [ 1, 2, 3 ]
arr3 = [1,2,3]
arr4 = [
  1,
  2,
  3
] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，beautiful 通常会这样做:如果数组适合放在一行中，就把它打印在一行中。否则，打印每一行。因此，当数组合适时，我们将使用`arr1`方法，当数组不合适时，我们将打印类似于`arr4`的内容。

这似乎很难，不是吗？但是漂亮可以帮助我们。这就是我们想要的子句:

```
case 'Array':
  return group(
    concat([
      '[',
      indent(
        concat([
          softline,
          join(concat([',', line]), path.map(print, 'value'))
        ])
      ),
      softline,
      ']'
    ])
  ) 
```

Enter fullscreen mode Exit fullscreen mode

我知道这与我们目前所做的有很大的不同。坏消息是许多插件代码看起来有点像这样。好消息是你很快就习惯了。

让我们从这个表达式最里面的部分开始，然后逐步解决。

首先我们有一个`path.map(print, 'value')`表达式。这类似于我们之前讨论的`path.call`习语，但是这里我们说“在这个节点的`value`键中，我有一个子节点数组；在每一个上调用打印机函数，给我一个结果数组”。换句话说，这就像做`node.value.map(print)`，但是记住我们不能那么做。

所以我们有一个数组，它输出了列表中的每个元素。下一步是添加逗号。为此我们使用了`join`构建器。它的签名是`join(separator, list)`，它只是用给定的分隔符连接部件列表。例如，`concat(["1", ",", "2", ",", "3"])`相当于`join(",", ["1", "2", "3"])`。我们可以在这里做，对吗？只是`join(",", path.map(print, 'value'))`。但是当列表适合一行时，我们希望在逗号后面有一个空格，当我们拆分它时，我们希望有一个换行符。这是用`line`生成器完成的，这也是我们加入`concat([",", line])`的原因。文件很清楚:

> 指定一个换行符。如果一个表达式适合一行，换行符将被替换为空格。换行符总是以当前缩进级别缩进下一行。

因此，如果列表适合一行，我们打印每个值，用逗号和空格分隔，如果不适合，我们用换行符替换空格。我们应该准备好了，对吧？只需添加开始和结束方括号，就可以了。不，因为当我们分割列表时，我们想缩进列表中的每个元素。

我们通过用`indent(concat([softline, ...]))`包围我们到目前为止所做的来做到这一点。这是怎么回事？首先我们在列表的开头放一个`softline`。`softline`与`line`非常相似，但不同的是，如果所有内容都在一行中，`softline`被替换为一个空字符串。我们还使用了`indent`构建器，它只是增加了缩进。当所有的东西都在一行中时，我们就不会有换行符，所以`indent`不会做任何事情。

快到了！在那之后，我们用`concat('[', ..., softline, ']')`包围一切。我们只是添加括号。我们还在右括号前添加了一个`softline`，因为它在`indent`构建器之外，所以`]`将具有与我们开始时相同的缩进。否则我们的列表看起来会像这样:

```
arr = [TOML spec
  1,
  2
  ] 
```

Enter fullscreen mode Exit fullscreen mode

最后**我们调用`group`来包围一切。这是一个试图将所有内容放在一行中的构建器。如果没有，它将开始用换行符替换线条和软线条。它实际上更复杂一点，但是这个解释现在就可以了。查看文档，了解其中的细微差别。**

 **同样，这似乎很难，但当你开始玩漂亮的时候，你会很快明白的。如果你仔细想想，所有这些也显示了 prettier 是多么强大。我们只用了几块积木就打印出了所有的列表。事实上，这甚至适用于嵌套列表，不管它们有多深！

## 暂且不说:如何实验

除了阅读文档和用一些例子运行你的完整插件，你如何检查构建者如何交互？原来你可以使用节点 REPL 与更漂亮的互动。首先启动 REPL 并导入一些东西:

```
> const prettier = require('prettier')
> const print = prettier.doc.printer.printDocToString
> const { concat, group, join, line, softline } = prettier.doc.builders 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以用构建器做实验:

```
> print(concat(['foo', 'bar', 'baz']), {})
{ formatted: 'foobarbaz' }
> print(join('|', ['foo', 'bar', 'baz']), {})
{ formatted: 'foo|bar|baz' } 
```

Enter fullscreen mode Exit fullscreen mode

为了测试像 group 这样的东西，你需要指定一个 printWidth:

```
> print(group(join(line, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 20 })
{ formatted: 'foo bar baz qux' }
> print(group(join(line, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 10 })
{ formatted: 'foo\nbar\nbaz\nqux' }
> print(group(join(softline, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 20 })
{ formatted: 'foobarbazqux' }
> print(group(join(softline, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 10 })
{ formatted: 'foo\nbar\nbaz\nqux' } 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样学。我知道这不是一个很好的用户体验，如果有更好的东西就好了(也许是一个网络游乐场，你可以运行这样的表达式，并看到不同输入的结果？)，但我不知道有什么更好的。

## 待定的事情

如果我们再次运行我们的例子，我们会看到我们有一个与我们指定的相同的 TOML 输出:

```
> prettier-plugin-toml@0.0.1 example /home/fvictorio/repos/prettier-plugin-toml
> prettier --plugin . example.toml
title = "TOML Example"
[owner]
name = "Tom Preston-Werner"
dob = 1979-05-27T15:32:00.000Z
[database]
server = "192.168.1.1"
ports = [8001, 8001, 8002]
connection_max = 5000
enabled = true
[servers]
[servers.alpha]
ip = "10.0.0.1"
dc = "eqdc10"
[servers.beta]
ip = "10.0.0.2"
dc = "eqdc10"
[clients]
data = [["gamma", "delta"], [1, 2]]
hosts = ["alpha", "omega"] 
```

Enter fullscreen mode Exit fullscreen mode

但是很难说这个更漂亮。有太多非常重要的事情我们没有做，而且我们使用的解析器也不容易做到:

*   我们没有保留空白行。更漂亮的理念是保留它们(虽然如果有两个或更多的空行在一起，它们会被一个空行代替)。这是可以做到的，但要做到这一点，我们需要一种简单的方法来获取节点的开始和结束索引。正如您在节点示例中看到的，我们只有起始行和起始列。
*   我们不是在**缩进表格**。如果 AST 的表示是一个适当的树，这将相对容易，但是记住我们有一个每行的节点列表。例如，如果在表对象下我们有一个“children”键，我们可以做一些类似于`path.map(print, 'children')`的事情，通过硬连线连接并缩进它们。

## 接下来是什么？

希望你已经学到了足够的知识来创建你自己的插件或者为一个插件做贡献。看看[插件列表](https://prettier.io/docs/en/plugins.html#official-plugins):如果你想美化的语言不在那里，你可以创建你自己的！如果是这样的话，你可以加入进来并做出贡献。

漂亮插件的一个好处是用它们进行 TDD 非常容易。如果你想为一个插件做贡献，只需添加一个 fixture，并举例说明它不工作，并尝试通过所有测试。如果你正在创建一个新的插件，你可以从小处着手:用一些简单的语法子集添加测试，并使它们更漂亮！**