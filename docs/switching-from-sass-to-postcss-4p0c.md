# 从 SASS 切换到 PostCSS

> 原文：<https://dev.to/michaeldscherr/switching-from-sass-to-postcss-4p0c>

自从 [PostCSS](https://github.com/postcss/postcss) 出来后，我就专门用它来做[自动修复](https://github.com/postcss/autoprefixer)。我并没有真正挖掘出`PostCSS`的力量，以及它实际上是如何成为 [SASS](https://sass-lang.com/) 的可比解决方案。

这篇文章将深入探讨我的怀疑主义，以及我是如何使用`PostCSS`找到解决方案的。

# 推

最初考虑我转行的是我对[顺风 CSS](https://tailwindcss.com/docs/what-is-tailwind/) 的兴趣。我喜欢`utility libraries`的想法，但是发现很难向其他开发者推销。然而，我们一直在寻找优化开发时间的方法，所以`Tailwind`感觉是个不错的选择。它是为`PostCSS`设计的，所以我至少想试一试。

# 先前的做法

我之前的方法是混合了 [SASS](https://sass-lang.com/) 、 [BEM](http://getbem.com/) 、 [ITCSS](https://speakerdeck.com/dafed/managing-css-projects-with-itcss) 、& [SMACSS](https://smacss.com/) 。他们解决了以下问题:

*   `SASS` - `variables, loops, imports, nested selectors, etc`
*   `BEM` -减少`specificity`并允许更容易地添加/修改代码
*   `ITCSS` -(与`BEM`相同)，但也允许`specificity`的`inverted triangle`层级变得越来越具体。
*   `SMACSS`——多用于`modifier classes`，像`.js-is-active, .has-posts, etc`。

这对我真的很有效。我看到了`utility libraries`的力量，但是我认为我应该让它们进化，以后再尝试。

# 开关

我不会用细节来烦你，但是我经历了许多迭代，比如:

*   只有`PostCSS`，除了`Tailwind`没有`plugins`
*   `PostCSS + SASS`
*   只有`PostCSS`，与`Tailwind`和其他`plugins`互补

`PostCSS + SASS`方法的核心问题是不可避免的重复。比如这些情况不能一起作用:

```
/**
  * cannot set $font-size-px-base in PostCSS
  * since SASS gets run first, so would have
  * to duplicate code
*/
@function rem($pixels, $context: $font-size-px-base) {
  @if (unitless($pixels)) {
    $pixels: $pixels * 1px;
  }
  @if (unitless($context)) {
    $context: $context * 1px;
  }
  @return $pixels / $context * 1rem;
}

/**
  * can't use Tailwind variables
  * in the SASS function `scale` since
  * SASS runs before PostCSS
*/
.selector {
  font-size: scale(font-size, theme('fontSize.lg'), theme('fontSize.xl'));
}

/**
  * Interpolation was also a nightmare
  * I couldn't figure out how to make
  * something like this work
  * even with PostCSS Simple Vars
*/
.selector {
  $path: theme('path.theme');

  background-image: url($(path)/test.png);
} 
```

## 最终解决

最终的解决方案只涉及`PostCSS`，有`Tailwind`和其他补充`plugins`。

当我想到这一点时，我实际上是在用`SASS`来表示:

*   `imports`
*   `mixins`
*   `functions`
*   `variables`
*   `nested selectors`——<small>——*很少，但很好有，尤其是对于伪选择器*——</small>
*   `loops` - <small>*很少，但是*</small> 很不错

当我意识到他们的插件系统的强大时，他们有了所有这些问题的解决方案。此外，我发现一些插件，像`functions`和`mixins`，允许你将`Javascript`功能注入`PostCSS`。我被卖了。

### 进口

[PostCSS Imports](https://github.com/postcss/postcss-import) 提供了与`SASS Imports`完全相同的功能，所以这是一个简单的切换。

```
@import 'tailwindcss/base';

@import 'tailwindcss/components';
@import 'components/btn';

@import 'tailwindcss/utilities'; 
```

### 功能

[PostCSS 函数](https://github.com/andyjansson/postcss-functions)允许你编写函数`IN JAVASCRIPT`并注入到`PostCSS`中。`IN JAVASCRIPT. I LOVE JAVASCRIPT`。

```
// functions.js

let settings = require('./settings');

let rem = (pixels, context = settings.defaults.fontSizePxBase) => {
  pixels = parseFloat(pixels);

  let result = pixels / context;

  return `${result}rem`;
};

module.exports = {
  rem,
}; 
```

### Mixins

[PostCSS Mixins](https://github.com/postcss/postcss-mixins) 与`PostCSS Functions`的交易相同。

```
// mixins.js

let placeholder = (mixin, immediateSelector = true) => {
  let vendors = [
    '::-webkit-input-placeholder',
    ':-moz-placeholder',
    '::-moz-placeholder',
    ':-ms-input-placeholder',
  ];

  return vendors.reduce((prev, vendor) => {
    let selector = immediateSelector ? `&${vendor}` : vendor;

    prev[selector] = {
      '@mixin-content': {},
    };

    return prev;
  }, {});
};

module.exports = {
  placeholder,
}; 
```

### 其他值得注意的插件

*   [PostCSS 嵌套](https://github.com/postcss/postcss-nested) -优惠`SASS nesting`
*   [PostCSS 简单变量](https://github.com/postcss/postcss-simple-vars) -提供简单的`variables`用于`selectors, etc`
*   [PostCSS purge CSS](https://github.com/FullHuman/postcss-purgecss)——根据你传入的文件删除不必要的`CSS`
*   [CSSNano](https://github.com/cssnano/cssnano) - minify `CSS`
*   [自动修复](https://github.com/postcss/autoprefixer) -根据您的浏览器支持删除不必要的`prefixes`

## 示例 PostCSS 配置文件

```
// postcss.config.js

let functions = require('./functions');
let mixins = require('./mixins');

module.exports = function(context) {
  /**
   * context comes from what you pass
   * into `gulp-postcss`, omitting detailing here
   * since `gulp` is an implementation detail
   */
  let { options } = context;

  let plugins = [
    require('postcss-import')({
      path: [options.paths.components, options.paths.styles, 'node_modules'],
    }),
    require('tailwindcss')('./tailwind.config.js'),
    require('postcss-functions')({
      functions,
    }),
    require('postcss-mixins')({
      mixins,
    }),
    require('postcss-nested'),
    require('postcss-simple-vars'),
    require('autoprefixer'),
  ];

  return {
    plugins,
  };
}; 
```

我希望这至少让你对`PostCSS`和它强大的`plugin`系统能为你的下一个项目提供什么感兴趣。