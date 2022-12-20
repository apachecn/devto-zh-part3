# 用普通 JavaScript 在日期中添加天数

> 原文：<https://dev.to/hugo__df/add-days-to-a-date-in-vanilla-javascript-4p2l>

尽管 JavaScript 有缺点，但在 JavaScript 中给日期添加天数还是很简单的。

虽然使用 moment.js 或其他日期操作库(date-fns、luxon、dayjs)来做像用 JavaScript 给日期添加天数这样简单的事情非常容易，但是编写一个简短的助手函数可能更容易。

## 创建日期副本的实用函数

```
function addDays(date, days) {
  const copy = new Date(Number(date))
  copy.setDate(date.getDate() + days)
  return copy
}

const date = new Date();
const newDate = addDays(date, 10); 
```

## 直接变异日期

```
const date = new Date();
date.setDate(date.getDate() + 10); 
```

## 陷阱和示例

这实际上像预期的那样工作，例如，月份滚动。

```
const d = new Date('2019-04-14');

const monthRollsOver = addDays(myDate, 31);
console.log(monthsRollOver)
// 2019-05-15 
```

在[observablehq.com/@hugodf/add-days-to-a-javascript-date](https://observablehq.com/@hugodf/add-days-to-a-javascript-date)找到真实的例子

当然，如果您已经在代码中的其他地方使用了日期操作库，您应该利用它，因为您已经支付了下载/解析成本。

[unsplash-logo
维姆·范因德](https://unsplash.com/@wimvanteinde?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)