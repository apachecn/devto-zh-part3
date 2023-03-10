# AngularJS，IE11 和研究的重要性

> 原文：<https://dev.to/thehanna/angularjs-ie11-and-the-importance-of-research-9ee>

昨天，在我维护的一个应用程序中发现了一个错误:IE11 禁用了一个不应该禁用的文本输入。我立即跳了进去，认为这是一场带有`ng-disabled`声明的误射。将近两个小时后，毫无进展。乍一看，这似乎是一个简单的解决办法！

这毫无意义。`placeholder`文本被设置为`value`。文本输入时控制器中的`ng-model`绑定没有更新。当我按下 enter 键时,`submit`动作什么也没做，但是点击了表单的 submit 按钮。输入从`ng-click`触发了一个测试功能，但没有从`ng-focus`或`ng-blur`触发。我决定减少损失，把它留到早上。

今天，在睡了一觉之后，我又看了一眼。我扩大了搜索范围，希望能找到解决办法的线索。经过多次搜索，这个秘密单词最终被确定为: **`angularjs strange form behavior ie11`** 。

令人尴尬的是，这句(事后看来)显而易见的话把我带到了官方的 AngularJS 开发者指南，它准确地描述了我在应用中看到的*和*:

> 在具有子代表单控件的元素上使用`disabled`属性会导致 Internet Explorer 11 中的意外行为。例如，带有`ng-model`的后代输入元素的值将不会反映模型(或者对模型的更改)，并且`placeholder`属性的值将作为输入的值被插入。后代选择元素也是不可操作的，就好像它们应用了`disabled`属性，这可能不是预期的效果。要解决此意外行为，1)避免将标识符`disabled`用于带有子代表单控件的元素上的自定义属性指令，2)避免将`disabled`用作传递给带有子代表单控件的自定义指令的属性的标识符。
> 
> <cite>— [AngularJS:开发者指南:ie 浏览器兼容性](https://docs.angularjs.org/guide/ie)T3】</cite>

通过检查父组件，我找到了痛苦的根源。我团队中的某人使用`disabled`作为父组件绑定的标识符，这属于拥有子表单控件的自定义指令的范畴！通过简单地将绑定重命名为`isDisabled`并使用属性`is-disabled`，整个问题就消失了。

让我的过分自信成为对别人的警示: ***阅读。的。该死的。手动。*T3】**