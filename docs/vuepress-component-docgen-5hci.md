# vue press-插件-文档生成

> 原文：<https://dev.to/jsbaguette/vuepress-component-docgen-5hci>

今天我想向大家介绍第一版的 vuepress-plugin-docgen

## 当像 [vue-styleguidist](https://www.npmjs.com/package/vue-styleguidist) 这样的东西已经在做同样的工作时，我为什么还要创建这样一个插件呢？

### 总之:VUEPRESS

即使 vue-styleguidist 实际上做得很好，Vuepress 也非常容易使用，非常漂亮，所以我更喜欢使用它。

所以我在 Vuepress 中搜索了一个插件。

我找到了[vue press-plugin-component-docgen](https://github.com/youngtailors/vuepress-plugin-component-docgen)。
那是很久以前的事了。我试着通过提交公关来帮忙，但并不积极。

所以我完全重写了插件，然后创建了 [vuepress-plugin-docgen](https://github.com/f3ltron/vuepress-plugin-docgen) 。

你可以点击查看文档

它运行良好，但仍是一项进展中的工作。你可以点击查看我们的[项目，了解最新信息。](https://github.com/f3ltron/vuepress-plugin-docgen/projects)

简单配置示例:

```
yarn add -D vuepress-plugin-docgen 
```

Enter fullscreen mode Exit fullscreen mode

```
plugins: [
  // as we respect the naming convention vuepress-plugin-docgen you can juste docgen as name
  [ 'docgen',
    {
      componentsDir: path.join(__dirname, '../../components')
    }
  ]
] 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！您需要使用 jsdoc 约定，它会工作得很好:)。

这就是我选择 Vuepress 而不是 Vue-styleguidist [的原因](https://slides.com/florentgiraud/janvier#/)

感谢您的阅读和支持！我希望你觉得这个插件有用。敬请期待:)。

Ps:如果你喜欢 vuepress-plugin-docgen 请随意用 patreon 启动和支持:)