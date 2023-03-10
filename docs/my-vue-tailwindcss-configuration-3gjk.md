# 我的 Vue + TailwindCSS 配置

> 原文：<https://dev.to/suciptoid/my-vue-tailwindcss-configuration-3gjk>

Tailwind 是“一个实用优先的 CSS 框架，用于快速构建定制用户界面”，它不是 UI 工具包。所以你需要基于 Tailwind css 类构建自己的 ui。

例如，在 bootstrap 中如果我们需要样式按钮，

```
<button class="btn btn-primary">
    Button
</button> 
```

Enter fullscreen mode Exit fullscreen mode

顺风

```
<button class="bg-blue hover:bg-blue-dark text-white font-bold py-2 px-4 rounded">
    Button
</button> 
```

Enter fullscreen mode Exit fullscreen mode

嗯，看起来 bootstrap 赢了。但是等等，如果你需要定制按钮主题，tailwind 会赢。或者你可以在 [tailwind 文档站点](https://tailwindcss.com/docs/examples/buttons)中看到按钮示例，以查看更多案例。

## 视图+尾风+吹扫 CSS

1.  `npm install tailwindcss @fullhuman/postcss-purgecss --save-dev`
2.  `npx tailwind init tailwind.js`
3.  `mkdir src/assets/css`
4.  `touch src/assets/css/tailwind.css`装满

```
@tailwind preflight;
@tailwind components;
@tailwind utilities; 
```

Enter fullscreen mode Exit fullscreen mode

1.  编辑`src/main.js`并导入您的 tailwindcss

```
// Tailwind CSS
import '@/assets/css/tailwind.css' 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，tailwind 文件的大小大于 bootstrap，你可以在这里看到解释。所以我们需要 **Purgecss** 来删除我们生产编译的 CSS 中未使用的 CSS 类。

1.  Edit postcss.config.js

```
const tailwindcss = require('tailwindcss')

const autoprefixer = require('autoprefixer')

const purgecss = require('@fullhuman/postcss-purgecss')

class TailwindExtractor {
  static extract (content) {
    return content.match(/[A-Za-z0-9-_:\/]+/g) || []
  }
}

module.exports = {
  plugins: [
    tailwindcss('./tailwind.js'),

    autoprefixer({
      add: true,
      grid: true
    }),

    purgecss({
      content: [
        './src/**/*.html',
        './src/**/*.vue',
        './src/**/*.js',
        './public/**/*.html'
      ],
      extractors: [
        {
          extractor: TailwindExtractor,
          extensions: ['html', 'vue', 'js']
        }
      ]
    })
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

最初发布于:[我的博客文章](https://sucipto.id/2019/05/01/tailwind-vue-js-postcss-configuration/)