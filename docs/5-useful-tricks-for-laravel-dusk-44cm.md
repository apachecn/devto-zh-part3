# 拉勒维尔黄昏的 5 个有用技巧

> 原文：<https://dev.to/barmmie_/5-useful-tricks-for-laravel-dusk-44cm>

# 5 个对拉勒维尔黄昏有用的招数

Laravel Dusk 是一款富于表现力、易于使用、功能强大的浏览器自动化和测试工具。使用 Dusk，您可以通过编程测试 javascript 驱动的应用程序。偶尔在用 Laravel dusk 编写浏览器测试时，我经常会遇到一些限制。在这篇文章中，我将分享这些情况的实例，以及我是如何克服这些限制的。

### **1。填充隐藏字段**

当测试某些 JS 组件(例如自动完成、日期选择器)时，模拟 DOM 点击/与所述组件的交互可能是费力的编写动作。因为大多数组件最终会将最终值保存到一个隐藏字段中。更方便的做法是直接在隐藏字段中填入值。这可以防止不可靠的测试，并确保我们不会测试我们不拥有/控制的东西(第三方组件)。

虽然 Laravel Dusk 没有为我们提供类似于`$browser->fillHidden($field, $value)`的方法，但是我们可以使用 [Dusk 浏览器宏](https://laravel.com/docs/5.8/dusk#browser-macros)自己创建它