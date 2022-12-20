# 我的第一个开源项目

> 原文：<https://dev.to/strdr4605/my-first-open-source-project-35ka>

# 前言

大约 4 年前，当我开始上大学的时候，我参加了我的第一次黑客马拉松。从那天起，我明白了我真的很喜欢建造东西，喜欢和别人一起做贡献。参加黑客马拉松时，我首先使用了 *JavaScript* ，因为它是开发快速原型的完美语言。我使用了很多开源库和工具，在某种程度上，我也想为任何项目做贡献，因为我认为贡献将帮助我提高我的开发技能。

所以偶尔我会尝试在 GitHub 上找一个我可以贡献的项目。我面临着一个问题，我想每个想做出贡献的人都会遇到这个问题。问题是找到合适的项目和你能解决的问题。我在 https://up-for-grabs.net/的[和其他网站上搜索我可以贡献的 *JavaScript 库或包。在大多数情况下，即使这些问题被贴上了*好的第一个问题*或*仅限第一次*的标签，它们也涉及到对项目的深刻理解。在我发现我可以实现的东西的情况下，其他贡献者已经在解决这个问题了。*](https://up-for-grabs.net/)

最后，我通过更新自述文件为一个项目做出了贡献，这在当时对我来说是一个成就🎉。但是我想用代码做贡献，所以我的第一个代码贡献是(正如我建议的)将 SVG 时钟标志转换成 http://rebble.io/的实时时钟。这是我的下一个成就。但是我仍然在寻找一些有趣的东西来贡献。但是当我创建我的第一个 npm 包时，一切都变了。

# 模仿案例

在向某人解释了`camelCase`、`snake_case`、`kebab-case`之间的区别后，我有了这个想法。我想起了[嘲讽海绵宝宝](https://knowyourmeme.com/memes/mocking-spongebob)的迷因，并快速搜索了一下，看看是否已经有这样的包。

这个包的目的是把一个`string`改成`sTrInG`。我考虑过把它命名为 ***【海绵宝宝】*** ，但是最后，我决定坚持用 ***模仿案例*** 因为它听起来更好(在我和我的朋友看来)。后来我在维基百科上找到了一篇解释这个案例的文章。所以在一两天内，我已经在 npm 上发布了第一个版本的[模拟案例](https://www.npmjs.com/package/mockingcase)。即使在那个时候，我也学到了很多关于 **[语义版本](https://semver.org/)** 和发布 npm 包的知识。

我在[reddit.com/r/javascript/](https://www.reddit.com/r/javascript/comments/9ytkno/my_first_npm_package/)上发了一个帖子后，很快就看到了大家的意见。有些人责备我，因为我做了一个无用的包，有些人称赞我，因为我学到了一些新东西，还有一些人只是喜欢这个包本身的想法，它甚至在 GitHub 上获得了一些 [⭐奖](https://github.com/strdr4605/mockingcase/stargazers)。喜欢这个包的人开始为它做贡献，比如添加单元测试和优化转换功能。
我决定添加更多的功能，即使它看起来像是这种包的过度工程化。我想到了一些可以添加的功能，并在[回购](https://github.com/strdr4605/mockingcase)中打开了问题，并在[https://up-for-grabs.net/](https://up-for-grabs.net/)中添加了项目，这样其他人也可以做出贡献。

由于软件包本身和应该实现的特性并不困难，这个开源项目对初学者非常友好。目前，这个项目已经有 11 个贡献者，他们做了很多出色的工作，真正改进了这个包。参与这个项目可以教会你软件版本控制、单元测试、持续集成、用于*类型脚本*的声明文件、文档以及许多关于 *JavaScript* 和开发本身的有趣事情。我甚至因为[原因](https://reasonml.github.io/)创建了[模拟案例绑定](https://redex.github.io/package/unpublished/strdr4605/bs-mockingcase/)。

目前，该软件包具有以下功能:

将字符串转换为模拟案例

```
mOcKiNgCaSe('foo-bar');
//=> 'fOo-bAr' 
```

Enter fullscreen mode Exit fullscreen mode

使用随机选项

```
mOcKiNgCaSe('aa', {random: true});
//=> 'aa'
//=> 'aA'
//=> 'Aa'
//=> 'AA' 
```

Enter fullscreen mode Exit fullscreen mode

移除非字母字符

```
mOcKiNgCaSe('a13%$a', {onlyLetters: true});
//=> 'aA' 
```

Enter fullscreen mode Exit fullscreen mode

改变转换顺序

```
mOcKiNgCaSe('foo bar', {firstUpper: true});
//=> 'FoO BaR' 
```

Enter fullscreen mode Exit fullscreen mode

支持数组

```
mOcKiNgCaSe(['foo','bar']);
//=> 'fOoBaR' 
```

Enter fullscreen mode Exit fullscreen mode

覆盖`String.prototype`

```
'foo_bar'.toMockingCase();
//=> 'fOo_bAr' 
```

Enter fullscreen mode Exit fullscreen mode

拥有全局配置

```
const mOcKiNgCaSe = require('mockingcase').config({onlyLetters: true, firstUpper: true});
// const mOcKiNgCaSe = mOcKiNgCaSe.config({onlyLetters: true, firstUpper: true});

mOcKiNgCaSe('foo bar42');
//=> 'FoO BaR' 
```

Enter fullscreen mode Exit fullscreen mode

你决定你是否喜欢这个包，它是否对你有用。但是我认为它可以作为一个笑话，向用户显示有趣的消息或错误。(在我看来)提高这个包的受欢迎程度的重要特性是[改变 HTML 页面上的所有文本](https://github.com/strdr4605/mockingcase/issues/37)，这将让开发者把这个包作为他们 web 应用程序的 4 月 1 日玩笑。

# 结论

即使对某些人来说它看起来像一个无用的项目，我在开发它的过程中学到了很多，也给了其他初学者一个为开源做贡献的机会。现在我专注于思考新功能，让其他人尝试实现它们。如果你喜欢这个项目，并有一些有趣的想法，或者只是想解决当前的问题，不要犹豫，贡献出来。