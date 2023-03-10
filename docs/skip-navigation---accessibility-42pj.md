# 跳过导航-可访问性

> 原文：<https://dev.to/muhnad/skip-navigation---accessibility-42pj>

# 什么是跳过导航？

跳过导航是一种向键盘和屏幕阅读器用户提供链接的方式，帮助他们跳转到网站的主要内容区域或任何特定区域。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [穆罕默德](https://github.com/Muhnad) / [跳转链接](https://github.com/Muhnad/skip-links)

### 帮助您添加跳过导航链接的 React 组件。https://skiplinks.surge.sh

<article class="markdown-body entry-content container-lg" itemprop="text">

# 跳过链接

> 帮助您添加跳过导航链接的 React 组件。

阅读跳过链接:

*   [跳到主要内容](https://www.w3.org/TR/WCAG20-TECHS/G1.html)
*   [跳过导航](https://webaim.org/techniques/skipnav/)

## 安装

```
  npm install -S skip-links
```

## 使用

1.  导入组件

```
  import SkipLinks from 'skip-links'
```

2.  调用组件并传递`props`

```
  render() {
    const links = [
      {title: 'Skip to main content', to: 'main'},
      {title: 'Skip to footer', to: 'footer'}
    ];
    return (
      <SkipLinks links={links}/>
    )
  }
```

## 小道具

| 支柱 | 类型 | 需要 | 描述 |
| --- | --- | --- | --- |
| `links` | 排列 | 真实的 | 添加需要显示为对象的链接有`title`和`to`。 |

阵列形状:

```
  [
    {
      title: String Required, // Text you need to show
      to: String Required //  Destination ID without hash '#'
    }
  ]
```

## 问与答(Question and Answer)

*   如何自定义跳过链接
    1.  您可以将样式属性直接传递给您的…

</article>

[View on GitHub](https://github.com/Muhnad/skip-links)