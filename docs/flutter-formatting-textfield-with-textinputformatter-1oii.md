# Flutter:用 TextInputFormatter 格式化文本字段

> 原文：<https://dev.to/rubensdemelo/flutter-formatting-textfield-with-textinputformatter-1oii>

移动应用程序中的一个常见任务是格式化字段，如信用卡、电话号码、邮政编码等。在 Flutter 中，我们使用 [TextInputFormatter](https://docs.flutter.io/flutter/services/TextInputFormatter-class.html) 来实现这一点。

在 TextFormField 和 TextField 中，属性 inputFormatters 允许您传递一个 TextInputFormatter 列表来定义该字段的行为。

有 3 种有用的内置实现:

[LengthLimitingTextInputFormatter:](https://docs.flutter.io/flutter/services/LengthLimitingTextInputFormatter-class.html)防止插入超过限制的字符；

[whitelingtextinputformatter:](https://docs.flutter.io/flutter/services/WhitelistingTextInputFormatter-class.html)只允许插入白名单中的字符模式；

[黑名单文本输入格式器:](https://docs.flutter.io/flutter/services/BlacklistingTextInputFormatter-class.html)防止插入黑名单字符模式。

它们有一些静态属性来帮助我们:

**白名单 TextInputFormatter** 。*digit only*:只接受数字[0–9]。

**黑名单 TextInputFormatter** 。 *singleLineFormatter* :强制输入为单行；

您可以组合格式化程序: