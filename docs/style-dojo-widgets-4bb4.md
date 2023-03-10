# 样式 Dojo 小部件

> 原文：<https://dev.to/odoenet/style-dojo-widgets-4bb4>

有一个关于[创建定制主题](https://dojo.io/tutorials/007_theming/)的完整 Dojo 教程，如果你看任何关于创建小部件的指南，你将学习如何创建 css 模块来使用你的小部件和应用程序。

但是如果您想在 Dojo 中使用一些开箱即用的小部件呢？现在，您可以开始为您的应用构建一个完整的随时可用的部件库。我们在创建[日期选择器](https://learn-dojo.com/creating-a-datepicker-with-dojo/)时看到了这一点，甚至还有像[选择](https://github.com/dojo/widgets/tree/master/src/select)和[按钮](https://github.com/dojo/widgets/tree/master/src/button)这样的小部件。查看[小部件展示](https://dojo.github.io/examples/widget-showcase/)了解更多信息！

如果你只是想快速开始一个好看的主题，你可以使用 [Dojo 主题](https://github.com/dojo/themes)。我将在另一个时间讨论如何创建自己的主题，但是现在，让我们假设您的应用程序中有一个 Dojo 小部件，并且您确实喜欢 Dojo 主题，但是您想稍微做一些调整。不足以保证一个自定义的主题，但也许会添加一些斜体字体，改变这里和那里的背景。

Dojo 提供了一种方式，以一种 [CSS 模块](https://css-tricks.com/css-modules-part-1-need/)友好的方式做到这一点。每个小部件的文档中都有一个关于 CSS 类的部分，您可以覆盖这些类，比如那些在[选择小部件](https://github.com/dojo/widgets/tree/master/src/select#theming)中的类。首先，让我们向这个小部件添加 Dojo 主题。

```
import theme from "@dojo/themes/dojo";
...

// in your render method
<Select
  theme={theme} // apply the Dojo theme
  options={names}
  value={this.selectedValue}
  placeholder="Pick a name"
  onChange={this.onSelectChange}
/> 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以使用 Dojo 提供的好看的主题。然而，也许我希望占位符文本是斜体，我想改变按钮的背景颜色和其他一些颜色。我不想改变太多，就几件事。

```
.arrow {
  background: #959595;
  color: #fff;
}

.focused {
  color: #005e95;
}

.placeholder {
  font-style: italic;
}

.inputWrapper {
  color: #6e6e6e;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了一些简单的 CSS 来匹配[文档](https://github.com/dojo/widgets/tree/master/src/select#theming)中的类名。现在，您可以使用一个普通的旧 JavaScript 对象将这些类名应用到您的 Select 小部件，该对象会将额外的类映射到小部件键。你可以在这里看到更多细节。

```
const SelectClasses = {
  "@dojo/widgets/select": {
    arrow: \[css.arrow\],
    focused: \[css.focused\],
    placeholder: \[css.placeholder\],
    inputWrapper: \[css.inputWrapper\]
  }
};

// in your render method
<Select
  theme={theme} // apply the Dojo theme
  classes={SelectClasses}
  options={names}
  value={this.selectedValue}
  placeholder="Pick a name"
  onChange={this.onSelectChange}
/> 
```

Enter fullscreen mode Exit fullscreen mode

这将保持您已经在使用的 Dojo 主题，但是也应用您正在添加的小部件的类。您最终会得到一个如下所示的应用程序。

正如您所看到的，在您的应用程序中使用所提供的 Dojo 主题并把您自己的小 CSS 糖应用到开箱即用的 Dojo 小部件上并不需要做很多工作。你还可以更进一步，为你的应用程序创建你自己的[自定义主题](https://dojo.io/tutorials/007_theming/)，甚至为一套应用程序创建一个[可重用主题](https://github.com/dojo/cli-create-theme)！快乐开发！

请务必[订阅时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！