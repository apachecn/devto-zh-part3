# 用 SCSS 覆盖元素 UI 中的默认主题

> 原文：<https://dev.to/sirtimbly/overriding-the-default-theme-in-element-ui-with-scss-20bl>

如果你想为 Vue 使用[元素 UI 库(你应该这样做)，那么你会很快发现自己覆盖了特定的样式，以便让它看起来恰到好处。他们曾经提供了一个复制和生成自定义主题的工具。要包含在项目中的 css 文件。这是一个痛苦，因为它需要另一个工具依赖来生成它，并且这个主题与您可能使用的任何其他样式预处理程序是完全隔离的。](http://element.eleme.io/#/en-US)

元素过去使用 CSS 自定义属性和 PostCSS 进行预处理，但它们现在已经迁移到 SCSS，这使我们有可能利用他们的变量，如果我们在我们的项目中也使用 SCSS/萨斯。

我在`src/styles/_settings.scss`有一个充满变量的大文件——这个文件包含在我的所有组件 scss 文件的顶部——并被注入到任何使用`lang="scss"`属性的单个文件组件中的每个 Vue scss 样式块中。这个设置文件是我设置调色板和创建所有全局变量的地方，这些变量将在项目的其余部分使用。

### 指令

首先需要注意的是，你不应该像指令中显示的那样直接导入元素-ui 主题 css 文件。

```
// DON'T ADD THIS LINE
import 'element-ui/lib/theme-chalk/index.css'; 
```

Enter fullscreen mode Exit fullscreen mode

相反，当您在自己的组件中需要 scss 文件时，您可以一次导入一个。

将以下变量定义添加到设置文件中。

```
$__color-primary: $my-primary-color; // primary color override for Element-UI (underscores are interchangeable with hyphens) 
```

Enter fullscreen mode Exit fullscreen mode

假设您正在编写一个定制的“选项卡”组件。您首先要在文件的顶部导入变量。

```
@import "../../../styles/settings"; 
```

Enter fullscreen mode Exit fullscreen mode

然后导入 element-ui scss 文件。

```
@import "~element-ui/packages/theme-chalk/src/tabs";
@import "~element-ui/packages/theme-chalk/src/tab-pane"; 
```

Enter fullscreen mode Exit fullscreen mode

然后，您需要编写自定义选项卡选择器，并将其绑定到 element-ui vue 组件将在内部使用的选项卡的类名。

```
.tabs, .el-tabs__nav {
   // ...
   .tab, .el-tabs__item {
    // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

每当您导入 element-ui scss 文件时，您的变量将覆盖它们的变量。你可以在这里看到[所有主题变量的列表](https://github.com/ElemeFE/element/blob/dev/packages/theme-chalk/src/common/var.scss)。如果您安装了 scss-lint，当您覆盖它们的变量时，您可能会从 scss-lint 得到一个解析错误。您需要将前面的两个破折号(在`$`之后)改为下划线。以两个破折号开始变量名是 css 自定义属性的语法，但它会在一些 scss 解析器中引发错误。幸运的是，下划线和破折号在 Sass 变量名中是可以互换的，原因现在已经湮没在时间的长河中了。