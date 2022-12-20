# 与 Gatsby JS 一起使用顺风

> 原文：<https://dev.to/jakedohm_34/using-tailwind-with-gatsby-js-10fj>

我开始用 Gatsby JS 重建我的个人网站，当然也要用我最喜欢的 CSS 框架，Tailwind CSS！我四处寻找如何一起使用 Gatsby 和 Tailwind 的指南，但没有找到任何完整的解决方案，所以我决定写这篇文章作为如何用 Gatsby 设置 Tailwind 的权威指南😄。

仅供参考:如果你遵循这个指南，热重装仍然可以工作，当你改变你的顺风配置文件时，你也可以重装！

## 第一步:安装 gatsby-plugin-postcss

[gatsby-plugin-postcss](%5Bhttps://www.gatsbyjs.org/packages/gatsby-plugin-postcss/?=gatsby-plugin-postcss%5D(https://www.gatsbyjs.org/packages/gatsby-plugin-postcss/?=gatsby-plugin-postcss)) 是一个 gatsby 插件，允许您在导入到页面/组件的 css 文件中使用 postcss 特性。Tailwind 是建立在 PostCSS 之上的，所以这是一个很好的起点！

要安装插件，请使用您最喜欢的软件包管理器。

```
# Using NPM
npm install --save gatsby-plugin-postcss

# Using Yarn
yarn add gatsby-plugin-postcss 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:配置 Gatsby 使用 PostCSS 插件

现在我们已经安装了一个插件，我们需要配置 Gatsby 来使用它！

打开你的`gatsby-config.js`，将‘Gatsby-plugin-postcss’添加到插件数组中。

```
// gatsby-config.js

module.exports = {
    siteMetadata: { ... },
    plugins: [
        'gatsby-plugin-postcss',
        // ...
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步:添加 PostCSS 配置文件

我们的 PostCSS 插件已经安装，Gatsby 正在使用它，所以剩下的就是配置 PostCSS 插件了！要配置 PostCSS，在项目的根目录下创建一个空的`postcss.config.js`文件(与`gatsby-config.js`文件在同一个文件夹中)。

## 第四步:安装顺风

现在，在我们配置 PostCSS 使用 Tailwind 之前，我们需要安装它。

```
# Using NPM
npm install tailwindcss --save-dev

# Using Yarn
yarn add tailwindcss --dev 
```

Enter fullscreen mode Exit fullscreen mode

## 第五步:生成顺风配置文件

要配置顺风，我们需要添加一个顺风配置文件。幸运的是，Tailwind 有一个内置的脚本来做这件事。只需运行下面的命令(同样，在项目的根目录下)。

```
./node_modules/.bin/tailwind init 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个包含 Tailwind 默认配置的`tailwind.js`文件。

## 第六步:配置 PostCSS

好了，我们已经安装并配置了 Tailwind，现在回到我们的 PostCSS 配置。我们需要将 Tailwind 添加到 PostCSS 的插件列表中。

```
// postcss.config.js

const tailwind = require('tailwindcss')

module.exports = () => ({
    plugins: [tailwind('./tailwind.js')],
}) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，你会看到我们传入了一个文件路径。这是我们的配置文件的路径。因此，如果你想移动或重命名顺风配置文件，只需记住在你的`postcss.config.js`文件中改变文件路径。

注意:你可以在插件数组中的 Tailwind 之前或之后添加任何你想使用的 PostCSS 插件，比如 autoprefixer。

## 第七步:添加带有顺风指令的 CSS 文件

一切都应该准备好了，我们现在需要做的就是实际上*在我们的 CSS 中使用*顺风！首先，创建一个`global.css`文件。我把我的放在`src/css/global.css`。然后，将下面的顺风指令添加到新文件中:

```
// global.css

@tailwind preflight;

@tailwind components;

@tailwind utilities; 
```

Enter fullscreen mode Exit fullscreen mode

对于这一步，我选择创建一个新的`global.css`文件，但是您也可以很容易地将顺风指令放在现有的 CSS 文件中。

## 第八步:导入我们的顺风 CSS

我们需要做的最后一件事是将我们的新 CSS 文件导入到页面或布局中，以便我们的顺风 CSS 实际上注入到我们的页面中。在`layout.js`中，或者任何你想让你的顺风 CSS 出现的地方，添加下面的导入:

```
// layout.js

import '../css/global.css' 
```

Enter fullscreen mode Exit fullscreen mode

## 你完了！

就是这样，你应该有一个功能齐全的顺风+盖茨比设置，有顺风配置，还有热重装！

如果你在这个过程中遇到任何麻烦，请在评论中告诉我，我会很乐意帮助你和/或修改这篇文章。

* * *

我在一家很棒的公司工作，叫做好工作。我们是一个网络开发专家团队，帮助机构、品牌和创业公司的设计团队为网络和移动设备开发产品。

如果你想找人帮忙《盖茨比》、《Vue》、《顺风》或其他项目，请不要犹豫[联系](https://simplygoodwork.com/contact)！