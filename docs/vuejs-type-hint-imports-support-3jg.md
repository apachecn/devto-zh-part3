# VueJs 类型提示导入支持

> 原文：<https://dev.to/costicaaa/vuejs-type-hint-imports-support-3jg>

从长远来看，在比一个附带的/有趣的项目更大的应用程序中，正确地组织您的项目对于了解项目的来龙去脉的老开发人员和尝试加入的新开发人员来说都是一个救命稻草。

但是正确构建项目并使用各种不同的导入/语法会让您的 IDE 感到困惑。我们在 PhpStorm 上遇到了这个问题——但它存在于所有 JetBrains 产品上，因为它们的核心或多或少是相同的。其他“较轻”的 ide 也有同样的问题，但是 JetBrains 和 VS 代码支持在下面的修复中得到了改进，所以我想这将在更多尊重“规范”的 ide 上工作。

考虑一个用 VueJS 编写的漂亮的现代 SPA，这是一个我非常喜欢的库，尽管它有一些警告(看看你，数组和对象变化)。
我们也在使用 VueRouter 和 Vuex，因此我们需要一些用于页面的组件，一些用于页面实际渲染的组件，
一些用于共享变量/功能的 mixins，一些用于 VueX 商店的模块，等等。事情开始变得混乱。

现在，当到处导入一个组件时，我们希望 IDE 能够键入提示它的位置，不管我们如何构建我们的应用程序。默认情况下，PhpStorm / WebStorm 支持类似这样的导入:

```
import MyComp from "../../components/shopping-cart/myComp.vue"; 
```

这很好，但前提是你知道你现在在哪里(你试图修改的文件的目录)。我猜你现在只是笑了笑:你从来没有笑过，你只是依靠你酷的快捷键和选择的 IDE 在你的项目中的文件和文件夹中跳来跳去。

那么如果我们能做这样的事情呢？

```
import MyComp from "COMPONENTS_ROOT/shopping-cart/myComp.vue"; 
```

如果使用 Webpack，您不仅可以为文件夹定义别名，而且在手动编写导入时，您还可以获得 IDE 的帮助来跳过文件夹和键入提示。

那么如何才能做到这一点呢？其实很简单:

```
config = {
    ............
    resolve: {
        alias: {
            "COMPONENTS_ROOT": path.resolve(__dirname, "/components"),
            "MIXINS_ROOT": path.resolve(__dirname, "/mixins"),
        },
    }; 
```

唯一的问题是…它不起作用。

我在 PhpStorm 和 VS 代码上进行了测试。这些 ide 简单地忽略了来自`webpack.config.js`的`alias`字段。

因此...我们能做什么？在 webpack 文档中，我发现根据一些规范(你不知道的东西存在的经典隐藏文档类型的东西)，如果我们希望 ide 实际上考虑到我们在`alias`中写的东西，我们还必须定义一个`aliasFields`的数组，就像这个

```
 aliasFields: [ "COMPONENTS_ROOT", "MIXINS_ROOT" ] 
```

`aliasFields`数组让 IDE 知道它应该解析声明的别名，并在解析和类型提示文件时考虑它们。在我看来，这是没有用的，也是不必要的，我找不到任何理由不希望我们所有的别名都被解析。我很好奇是否有人知道这背后的心态，为什么这是一件事。

对于`laravel-mix`用户:`laravel.mix.config.js`文件结构不同，不支持 atm。
解？创建一个只包含`alias`和`aliasFields`的哑文件，并将其设置为 webpack 根配置文件。
这里的问题是，在添加/删除别名时，您必须更新这两个文件。

TL；DR 所需的配置(通过 IDE)必须在解析键
中同时具有`alias`和`aliasFields`

```
config = {
    ............
    resolve: {
        alias: {
            "COMPONENTS_ROOT": path.resolve(__dirname, "/components"),
            "MIXINS_ROOT": path.resolve(__dirname, "/mixins"),
        },
        aliasFields: [ "COMPONENTS_ROOT", "MIXINS_ROOT" ]
    }; 
```

很高兴听到你们在大型 VueJs 项目中使用的其他工具！

干杯: )