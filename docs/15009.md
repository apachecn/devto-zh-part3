# 一个好的反应日期和时间选择器

> 原文：<https://dev.to/_bigblind/a-good-react-date-and-time-picker--3o00>

有这么多约会对象，但我找不到一个

*   适用于桌面和移动设备
*   有很好的键盘输入，最好是只允许我一个接一个地输入数字，跳过任何格式化字符。

我真的很喜欢 Chrome 为 [`input type="datetime-local"`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/datetime-local) 提供的日期选择器控件，但是它对浏览器的支持很差。有什么建议吗？

# 我看了什么

*   AirBnB 的[datepicker](http://airbnb.io/react-dates/)[react-datetime](https://github.com/YouCanBookMe/react-datetime)和 [react-widgets](https://github.com/jquense/react-widgets) 中的 DateTimePicker 都不能很好地处理直接输入到文本输入中。他们不会轻易让你在设置月/日/年之间切换。在许多应用程序中，输入的行为类似于普通的文本字段。
*   [react-datetime-picker](http://projects.wojtekmaj.pl/react-datetime-picker/) 非常接近我想要的。我可以很容易地在日期、月份、年份等之间切换。使用 tab 或箭头键。我也可以使用向上/向下箭头来记忆数值。我唯一的吹毛求疵，我已经把这些作为一个问题贴了出来，就是当键入一个值时，焦点不会自动地从一个字段移动到另一个字段，并且键入以 0 开头的数字是不直观的(在一个字段中键入 05 会导致输入 005)。