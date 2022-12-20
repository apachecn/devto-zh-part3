# 剖析 JavaScript 导入语法

> 原文：<https://dev.to/irreverentmike/picking-apart-javascript-import-syntax-e5o>

*注:这是我第一篇[关于解构的文章](https://mike.biful.co/deconfusing-javascript-destructuring-syntax)的后续。Import 语法相当自由地使用了析构，对于刚开始使用它的人来说，这真的很难理解。如果这一切看起来令人困惑的话，先看看我的另一篇文章吧！*

让我们讨论一下将依赖项导入到您的节点项目中。随着您的工作变得越来越复杂，您将不可避免地遇到这样的语法:

```
import React, { useState, useEffect } from 'react';
import { Link } from 'gatsby';
import Button from '@material-ui/core/Button';
import moment from 'moment';

import { Layout } from '../components'; 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，这很简单。我们正在导入一些位以在 React 组件中使用。正如您可能想象的那样，我选择了这四行代码，因为每一行都是独一无二的。事实上，在我作为一名优秀的 Node/React 开发人员的旅程中，我已经找到了搞糟这些的方法。

你也会把事情搞砸的，没关系！目前，我是来帮忙的。

我们将按照复杂性的顺序逐一介绍，我将尽力解释到底发生了什么，以及我在工作中对进口的看法。

## 直截了当的导入语法——最简单的情况

```
import moment from 'moment'; 
```

Enter fullscreen mode Exit fullscreen mode

如果你曾在。Net 语言，或者 Python，或者 Ruby，或者世界上许多其他语言中的一种，这应该是你的第二天性。我在这里特别指出它，因为我们中的一些人可能从来没有见过它。

### 这是怎么回事？

事实证明这很简单。 [moment](https://www.npmjs.com/package/moment) 是一个 JavaScript 库，已经包含在我们节点项目的`package.json`文件的`dependencies`或者`devDependencies`中。如果你是 node 新手，对`package.json`不熟悉，请在这里阅读更多关于它的信息[。](https://nodesource.com/blog/the-basics-of-package-json-in-node-js-and-npm/)

这一行代码创建了对`moment`库中所有可用内容的引用，并将其放入一个我们可以用来访问它的变量中。`'moment'`位(在引号中)告诉编译器要获取哪个库。另一个，`moment` ( *不是*在引号里)是变量。所以从这里开始，我们可以像访问这个文件中的其他变量一样访问 moment:

```
import moment from 'moment';
console.log(
  moment().get('year')
);
// 2019 
```

Enter fullscreen mode Exit fullscreen mode

### 不明显的位

在幕后，这只是把所有由`moment`库通过`export default` [提供的东西放在它的主文件](https://github.com/moment/moment/blob/develop/src/moment.js#L95)中，并把它塞进一个变量——这个变量可以有*任何我们想要的有效名字*!

这可能会令人困惑，但是如果这个名字对你来说更有意义的话，你绝对可以这么做:

```
import ThatReallyUsefulDateLibrary from 'moment';
console.log(
  ThatReallyUsefulDateLibrary().get('year')
);
// 2019 
```

Enter fullscreen mode Exit fullscreen mode

## 从库中的某处导入组件

下一个——这个稍微复杂一点的野兽:

```
import Button from '@material-ui/core/Button'; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们从 [@ `material-ui`库](%5Bhttps://material-ui.com%5D(https://material-ui.com/))中抓取`<Button />`组件。同样，这相当简单——但是从 material-ui 项目的结构来考虑这一点可能会有所帮助。Material-ui 导出*装载*很棒的东西，并且都被组织成逻辑分组。稍微这样想一想:

```
// material-ui exports
const muiExports = {
  core: {
    Button: () => {}, // some component
    TextField: () => {} // another useful component
    // and loads more
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的`Button`的导入语法，我们告诉编译器给我们一个对名为`Button`的导出内容的引用，它可以在`/core/Button`下的`@material-ui`库中找到。编译器本质上将其视为上面代码片段中的 JSON 对象。

事情是这样的——*也就是*意味着我们可以破坏它！😁。这个语法也可以导入`Button` :

```
import { Button } from '@material-ui/core'; 
```

Enter fullscreen mode Exit fullscreen mode

这也意味着我们可以在一行中从`/core`导入*多个东西*！

```
import { Button, TextField} from '@material-ui/core'; 
```

Enter fullscreen mode Exit fullscreen mode

很酷吧。我知道这可能会令人困惑，但试着坚持下去。很快你就会明白了。这就引出了我们的下一个例子:

### 通过析构的方式导入库的子集

```
import { Link } from 'gatsby'; 
```

Enter fullscreen mode Exit fullscreen mode

嘣！现在应该很容易了。`Gatsby`提供的东西之一是它们的 [`link`](https://www.gatsbyjs.org/docs/gatsby-link/) 组件。我们进口*只是为了在这里使用那个组件*。

### 重命名导入

但是如果我们的项目中已经有了一个名为`Link`的组件呢？或者，如果我们正在制作一个塞尔达粉丝网站的传奇，而`Link`已经在一个我们不能重命名的组件或变量中定义了，该怎么办？事实证明，重命名导入中的内容就像重命名析构语句中的内容一样简单。我们可以像这样从`gatsby`中重命名相同的组件:

```
import { Link as GatsbyWebLink } from 'gatsby'; 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以在一条语句中重命名一个或多个被析构的导入:

```
import { 
  Link as GatsbyWebLink,
  graphql as graphqlQuery,
  useStaticQuery
} from 'gatsby'; 
```

Enter fullscreen mode Exit fullscreen mode

小菜一碟！🍰

## 相对进口

还有一件快速的事情——如果你在你的导入位置字符串
中使用了*相对路径*，编译器知道寻找你导出的东西

```
import { Layout } from '../components'; 
```

Enter fullscreen mode Exit fullscreen mode

就像在其他地方一样，你可以在这里随心所欲地组合和重命名东西:

```
import {
  Layout,
  Button as SuperButton
} from '../components'; 
```

Enter fullscreen mode Exit fullscreen mode

## 把所有的东西放在一起

最好的不总是最后的，但这肯定是我今天要分享的最后一个例子

```
import React, { useState, useEffect } from 'react'; 
```

Enter fullscreen mode Exit fullscreen mode

如果你一直在家玩，这应该很熟悉——我们从`react`获取默认导出，并将其放入变量`react`。我们还从同一个库中*析构了`useState`和`useEffect`。如果你问自己“那么我们不能在`React`的孩子时访问`useState`？答案是——嗯，其实，是的！*

这是完全有效的

```
const [loading, setLoading] = React.useState(false); 
```

Enter fullscreen mode Exit fullscreen mode

…但是打字和阅读都不如
好

```
const [loading, setLoading] = useState(false); 
```

Enter fullscreen mode Exit fullscreen mode

从执行的角度来看，它们的功能是一样的，但后者是习惯用法。

## 我想就是这样。

我想。事实证明这是一篇很难写的文章——导入文件的方法有十亿种，我可能错过了很多例子。对于这里显示的一些不同的导入语法，肯定还有性能和包大小的影响。虽然它们绝对是真正的约束，并且对您的应用程序的性能有真正的影响，但我将这个讨论留到了另一天——纯粹是为了简单起见。

还有一个不小的问题，使用 [import](https://caniuse.com/#feat=imports) 需要一个像 Babel 或 Webpack 这样的 transpiler。这是另一个超级复杂的宇宙，我不确定我是否有能力在一篇博文中解释清楚。这也意味着我已经跳过了展示上述任何语法如何与`require()`一起工作。坦白地说，这里需要解释的内容太多了 EcmaScript 和 node 的未来改进将会使它变得更好。

# 打个招呼！

像往常一样，如果我在这里有任何错误，我很想知道它！给我写封短信[@不敬麦克](https://twitter.com/irreverentmike)。我很乐意收到你的来信。👋

#### 信用

本帖封面照片的背景图片来自 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Ricardo Viana](https://unsplash.com/photos/-tYsPFKMm7g?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 。谢谢你的工作！*