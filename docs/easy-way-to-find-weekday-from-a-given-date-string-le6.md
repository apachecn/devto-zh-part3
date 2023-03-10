# 从给定的日期字符串中查找星期几的简单方法

> 原文：<https://dev.to/theprivileges/easy-way-to-find-weekday-from-a-given-date-string-le6>

最近有人向我介绍了`Intl`对象，它被用作 [ECMAScript 国际化 API](https://norbertlindenberg.com/2012/12/ecmascript-internationalization-api/index.html) 的名称空间。它有一个名为`DateTimeFormat`的简洁对象，可以用来将日期值格式化成特定于地区的字符串。

我将向您展示使用`Intl.DateTimeFormat`生成给定日期字符串(例如`07/16/1945`)的星期几的典型用例。

## 问题

给定格式为`MM/DD/YYYY`的`dateString`，查找并返回该日期的星期几。

## 解

```
/**
 * Given a date string in the format MM/DD/YYY, find and return the 
 * weekday for that date in English.
 *
 * @param {string} dateString
 * @returns {string} - weekday for the given date [e.g: Wednesday]
 */
const getWeekDay = (dateString) => {
  const date = new Date(dateString);
  const dateTimeFormatOptions = { weekday: 'long' };
  const dateTimeFormat = new Intl.DateTimeFormat('en-US', dateTimeFormatOptions); 
  return dateTimeFormat.format(date);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 解释

1.  创建一个名为`getWeekDay`的函数表达式，它接受一个字符串(`dateString`)。
2.  用给定的`dateString`创建一个`Date`对象，这样我们就可以把它作为参数传递给`DateTimeFormat`的`format`方法。
3.  创建一个对象来保存`Intl.DateTimeFormat`的 [`options`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat#Parameters) 参数。设置`weekday`属性等于`long`，这样我们在调用`format`时得到本地化的工作日。
4.  创建一个 [`Intl.DateTimeFormat`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat) 的实例，将`locales`设置为`en-US`，并传入之前创建的`dateTimeFormatOptions`对象。
5.  返回对`Intl.DateTimeFormat`调用 [`format`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat/format) 方法的结果，找到给定`date`的工作日。

## 输出

```
console.log(getWeekDay("07/16/1945"));
// expected output: "Monday" 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们使用`Intl.DateTimeFormat`对象快速找到给定日期字符串的星期几。最棒的是 [`Intl`](https://caniuse.com/#feat=internationalization) 拥有跨现代浏览器的强大支持。

感谢您的阅读。