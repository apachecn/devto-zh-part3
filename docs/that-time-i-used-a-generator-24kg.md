# 那次我用了发电机

> 原文：<https://dev.to/jeffposnick/that-time-i-used-a-generator-24kg>

# 背景故事

早在 2014 年，我就转到谷歌的 Web DevRel 团队工作，我承担的第一批任务之一就是为新的 Web 平台功能编写简短的更新文章和代码示例。这些是...回顾起来有些尴尬，所以我不会在这里链接到很多，但我首先放在一起的一个覆盖了[发电机](https://developers.google.com/web/updates/2014/10/Generators-the-Gnarly-Bits)。关于生成器，我没有太多要说的，所以本文中使用的一般方法是链接到更多的[规范资源](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)，同时指出一些我认为可以增加一些价值的有趣的“问题”。

所以我写了这个，继续前进，然后几乎忘记了发电机在接下来的 4 年里的存在。

# 重构

这就把我们带到了几个月前，当时我正在为`workbox-precaching`模块的[重写](https://github.com/GoogleChrome/workbox/issues/1793)，作为 [Workbox v4](https://github.com/GoogleChrome/workbox/releases/tag/v4.0.0) 版本的一部分。这给了我一个机会去重温一些很久没有接触过的代码。

## 原始逻辑

实际的代码不是非常相关(我将在下面链接到 before 和 after，对于那些感兴趣的人来说)，但是要点是:

*   这是用来比较一个字符串和几个可能的匹配的代码。
*   计算每一个可能的匹配是(有点)昂贵的。
*   代码一找到匹配就返回`true`。
*   如果没有一个条件匹配，它返回`false`。

原始代码类似于:

```
const originalUrl = '...';
const urlToMatch = '...';

const urlVariation1 = generateVariation1(originalUrl);
if (urlToMatch === urlVariation1) {
  return true;
}

const urlVariation2 = generateVariation2(originalUrl);
if (urlToMatch === urlVariation2) {
  return true;
}

// etc.

return false; 
```

Enter fullscreen mode Exit fullscreen mode

我不太喜欢美学上的一系列重复的`if(...) { return ... }`语句，这样的代码结构会使人更难理解每个测试用例实际上都在做同样的事情。

## 无生成器重构

强调重复逻辑的一个潜在重构可能是:

```
const originalUrl = '...';
const urlToMatch = '...';

const urlVariations = [
  generateVariation1,
  generateVariation2,
  // etc.
].map((generateVariation) => generateVariation(originalUrl));

for (const urlVariation of urlVariations) {
  if (urlToMatch === urlVariation) {
    return true;
  }
}

return false; 
```

Enter fullscreen mode Exit fullscreen mode

从美学的角度来看，我喜欢这个版本的代码，但是有一个缺点是，您最终会提前运行每个`generateVariationN()`函数。如果列表中前面的一个变体最终匹配了，那么您就白白运行了(潜在的昂贵)代码。

## 用生成器重构

所以！这时我想起了生成器是一个东西，在这个用例中可以派上用场。

发电机是[可重复](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators#Generator_functions)的，所以它可以直接放入 [`for...of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 循环中。

生成器只在它们的`next()`值被请求时运行:它们将一直执行到遇到一个`yield`关键字，这时它们暂停，控制返回到触发迭代的地方。如果我们在生成器中一次生成一个可能很昂贵的函数的结果，我们就不必担心执行那些结果实际上并不需要的函数。我们仍然可以将代码构造成一个循环，而不是一系列的`if(...) { return ...; }`语句。真是两全其美！

使用一个生成器和一个`for...of`循环，我们得到的代码类似于:

```
function* generateVariations(originalUrl) {
  // You could put these yields inside a loop, too!
  yield generateVariation1(originalUrl);
  yield generateVariation2(originalUrl);
  // etc.
}

for (const urlVariation of generateVariations(originalUrl)) {
  if (urlToMatch === urlVariation) {
    return true;
  }
}

return false; 
```

Enter fullscreen mode Exit fullscreen mode

## 工作箱中的实际变化

如果你很好奇，Workbox v3 中的原始代码是[这里是](https://github.com/GoogleChrome/workbox/blob/d27aafbdf164f051a883965058e6eb4c0df3a052/packages/workbox-precaching/_default.mjs#L76-L130)。v4 代码被分成新的[生成器](https://github.com/GoogleChrome/workbox/blob/f1164254b8abdd12c5c601ee7e7fc7d73fffd979/packages/workbox-precaching/utils/generateURLVariations.mjs#L23-L55)和循环生成值的[代码的模块。](https://github.com/GoogleChrome/workbox/blob/f1164254b8abdd12c5c601ee7e7fc7d73fffd979/packages/workbox-precaching/utils/getCacheKeyForURL.mjs#L25-L35)