# 使用表情符号的节点依赖之间的差异🤷‍♂️ 🤷‍♀️

> 原文：<https://dev.to/intercoder/difference-between-node-dependencies-using-emojis-43ne>

节点依赖有不同的形式:`dependencies`、`devDependencies`、`peerDependencies`、`bundleDependencies`和`optionalDependencies`。

我们将在这篇博文中讨论前三个，它们是我们最感兴趣的，但是我将简要解释一下`bundleDependecies`和`optionalDependencies`是什么。

*   如果您运行 npm 包，这些依赖项将与您的库捆绑在一起。来自@npm 的人们自己:

> "这定义了一个包名数组，在发布包时会被捆绑."

*   是可选的依赖项。我自己出去😀。如果找不到此处安装的依赖项，它们不会导致库安装失败。我认为它们是不那么严格的依赖性的替代版本。来自你的朋友@npm:

> 如果可以使用某个依赖项，但您希望 npm 在找不到该依赖项或安装失败时继续运行，则可以将其放在 optionalDependencies 对象中

🚨请记住，您必须指定依赖项或可选依赖项，但不能同时指定两者🚨

> optionalDependencies 中的条目将覆盖 Dependencies 中的同名条目，因此通常最好只放在一个位置

解决了这个问题后，让我们开始研究 node 给我们的其他三种依赖关系。

* * *

如果你在编程的日子里遇到了一个`package.json`，你可能会看到一个`dependencies`、`devDependencies`和/或甚至可能是它们的表兄弟，即`peerDependencies`对象，也就是所谓的🍐依赖性。

……懂了，懂了？…因为听起来好像🍐…好吧，我现在就停下来...🙂

最后一个，通常是在你试图安装 x 包时发出的警告。通常以下列形式出现:

`the package 'x' has an unmet peerDependency of 'y@1.0.0'`

你可能会想，这些事情怎么会有联系呢？让我们通过创建自己的“制作汉堡”来看看这三个问题🍔“图书馆。

```
// pseudo package.json with some emojis

{
 “name”: “making-a-burger”,
 “version”: “1.0.0”,
 “description”: “🍔 lib”,
 “main”: “index.js”,
 “author”: “🙋‍♂️”,
 “license”: “MIT”,
 “dependencies”: {
   “🥩”: “meat@2.0.0”
  },
 “devDependencies”: {
   “🥚”: “egg-yolk@1.O.O”
  },
 “peerDependencies”: {
   “🍞”: “>= bread@1.x”
  }
} 
```

实际上，这可以翻译成…

*   依赖:嘿，要做这个🍔图书馆(又名经营汉堡店)你必须拥有🥩手头上。图书馆在上面运行……(除非你在做蔬菜汉堡🚫 🐮，当然)。

一个很好的例子是`vue`、`react`、`react-native`、`eslint`(如果创建一个可共享的 eslint-config 文件)等等。

*   devDependencies:哟，在开发的时候🍔你最好有一个🥚手头上，因为这将简化过程…(并给你的食物增添风味😋…如果烹饪不当，可能会感染沙门氏菌…？).

对原始代码进行修改/操作/测试的包就是这种依赖的一个很好的例子:`@babel/*`、`webpack`、`jest`等等。

*   同伴依赖性:兄弟/姐妹(…🤔？)，不要试图使这个🍔图书馆没有一些🍞也有等于或高于 1.x 版的版本。(版本无麸质，正常，全谷物…好吧，这一个实际上是一个相当糟糕的笑话)。

这最后一个是最有趣的。我们指定`peerDependencies`是为了让你的库的用户知道，他们也必须安装这个其他依赖项的最低版本，因为整个库都在他们上面运行。换句话说:

`package ‘x’ also depends on having package ‘y@1’ installed.`

* * *

做一个完整的循环，🙂如果我们要将`optionalDepedencies`和`bundleDependencies`添加到这个库中，它们的形式将是:

```
{
 “name”: “making-a-burger”,
 “version”: “1.0.0”,
 “description”: “🍔 lib”,
 “main”: “index.js”,
 “author”: “🙋‍♂️”,
 “license”: “MIT”,
 “dependencies”: {
   “🥩”: “meat@2.0.0”
  },
 “devDependencies”: {
   “🥚”: “egg-yolk@1.O.O"
  },
 “peerDependencies”: {
   “🍞”: “>= bread@1.x”
  }
 “optionalDependencies”: {
   “🥬”: “lettuce@3.2.x”
  },
 “bundleDependencies”: [
   “🍟”, "🥤"
  ]
} 
```

我想就是这样，我希望这有所帮助或至少有趣。

如果您想了解更多关于节点依赖和 package.json 中所有好东西的信息，不要忘记看看 [npm-docs](https://docs.npmjs.com/files/package.json) ,因为其中充满了有用的信息。

感谢您的阅读，如果有什么不清楚的地方，请给我留言。欢迎所有反馈。

* * *

**注:**当我写完这篇文章时，我意识到`devDependencies`更好的例子应该是绞肉机(？)，但是我找不到那个表情符号😔。