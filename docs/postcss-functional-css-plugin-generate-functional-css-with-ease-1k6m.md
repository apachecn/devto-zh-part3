# 功能 CSS 插件。轻松生成功能性 CSS。

> 原文：<https://dev.to/iamfrntdv/postcss-functional-css-plugin-generate-functional-css-with-ease-1k6m>

[![Swiss Knife](img/3b6f83f97e5e792e9efba8c725b5b485.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9nfMSCDA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i009u3lu1uelbcu75a6r.jpg)

如果你喜欢函数式 css，如果你想轻松地生成它，如果你想控制输出，那么这个插件就是为你准备的。

## 为什么？

编写函数式 css 并不是最好的工作。有很多重复的任务，这可能会导致一些错误，甚至会使更新变得更加困难。生成功能性 css 要容易得多。

当然，用 less、sass、stylus 等工具生成功能性 css 是可能的。但是在这种情况下，源 css 将被限制在你喜欢的技术上。对于 PostCSS，没有这样的限制。

PostCSS 功能 CSS 插件帮助控制生成的 CSS 的输出。它提供了启用或禁用功能(如字体大小、行高、边距、填充等。).它提供了自定义类名的能力，所以你可以使用你喜欢的那些类名。它提供了轻松生成响应性 css 的能力。

## 支持的功能

*   显示
*   漂浮物
*   字体大小
*   字体粗细
*   行高
*   边距(上、右、下、左)
*   对象匹配
*   不透明
*   填充(顶部、右侧、底部、左侧)
*   位置
*   文本对齐
*   文本大小(字体大小和行高的组合)
*   能见度
*   z 指数

更多功能还在后面。

## 入门。

安装:

```
npm i postcss postcss-functional-css 
```

Enter fullscreen mode Exit fullscreen mode

配置:

```
postcss([
  require('postcss-functional-css')({
    mediaQueries: [
      {
        prefix: 'sm',
        prefixSeparator: '-',
        params: '(min-width: 640px)'
      }
    ],
    globalStyles: true,
    features: {
      display: true,
      float: true,
      fontSize: {
        className: 'fs',
        values: [12, 14, 16, 18, 24, 32, 48],
        unit: 'px'
      },
      fontWeight: {
        className: 'fw',
        values: [400, 500, 600, 700]
      },
      lineHeight: {
        className: 'ln',
        values: [16, 18, 20, 22, 28, 36, 52],
        unit: 'px'
      },
      margin: {
        top: {
          className: 'mt',
          values: [4, 8, 12, 16, 20, 24, 28, 36, 48, 72, 96, 120],
          unit: 'px'
        },
        right: {
          className: 'mr',
          values: [4, 8, 12, 16, 20, 24, 28, 36, 48, 72, 96, 120],
          unit: 'px'
        },
        bottom: {
          className: 'mb',
          values: [4, 8, 12, 16, 20, 24, 28, 36, 48, 72, 96, 120],
          unit: 'px'
        },
        left: {
          className: 'ml',
          values: [4, 8, 12, 16, 20, 24, 28, 36, 48, 72, 96, 120],
          unit: 'px'
        }
      },
      objectFit: true,
      opacity: {
        className: 'o',
        increment: 10
      },
      padding: {
        top: {
          className: 'pt',
          values: [4, 8, 12, 16, 20, 24, 28, 36, 48, 72, 96, 120],
          unit: 'px'
        },
        right: {
          className: 'pr',
          values: [4, 8, 12, 16, 20, 24, 28, 36, 48, 72, 96, 120],
          unit: 'px'
        },
        bottom: {
          className: 'pb',
          values: [4, 8, 12, 16, 20, 24, 28, 36, 48, 72, 96, 120],
          unit: 'px'
        },
        left: {
          className: 'pl',
          values: [4, 8, 12, 16, 20, 24, 28, 36, 48, 72, 96, 120],
          unit: 'px'
        }
      },
      position: true,
      textAlignment: true,
      textSize: {
        className: 'ts',
        fontSizeUnit: 'px',
        lineHeightUnit: 'px',
        values: [
          {
            fontSize: 14,
            lineHeight: 20
          },
          {
            fontSize: 18,
            lineHeight: 24
          }
        ]
      },
      visibility: true,
      zIndex: {
        className: 'z',
        increment: 1,
        limit: 10
      }
    }
  })
]) 
```

Enter fullscreen mode Exit fullscreen mode

给你的 css 文件添加注释:

```
/* postcss-functional-css */ 
```

Enter fullscreen mode Exit fullscreen mode

注释将防止在一些打包器中出现重复的 css，如 package 或 Webpack。只有存在注释时，才会追加 CSS。

请随意贡献或提出新功能。

## 链接

[PostCSS 功能 CSS](https://github.com/iamfrntdv/postcss-functional-css)