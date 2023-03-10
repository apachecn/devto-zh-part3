# 用 JavaScript 格式化日期

> 原文：<https://dev.to/jesseskinner/formatting-dates-with-javascript-1e39>

有很多流行的 JavaScript 日期格式库，比如 [Moment.js](https://momentjs.com/) 、 [Luxon](https://moment.github.io/luxon/) 和 [date-fns](https://date-fns.org/) 。这些库非常强大和有用，允许您使用特殊的语法请求各种日期格式，但它们也提供了比大多数人使用的更多的功能。这意味着，你的用户可能下载了比他们需要的更多的 JavaScript。

在本文中，我将向您展示如何使用内置的基本[日期对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)来格式化日期，而无需任何第三方库。换句话说，我们将用纯普通的 JavaScript 格式化日期。

请随意复制并粘贴下面的解决方案，作为您自己代码库的起点。我将演示如何生成一些常见的格式类型，但是您可能需要对下面的解决方案进行一点修改，以便按照您想要的方式来格式化日期和时间。

## 国际化 API 呢？

在开始之前，我应该提到 JavaScript 日期中内置了一些格式化功能，使用了[国际化 API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat) 。

使用国际化 API，您可以根据特定的区域设置格式化日期，这意味着根据用户的位置和语言的习惯进行格式化。如果您对日期和时间的显示方式不挑剔，这在许多情况下都可以很好地工作，但这取决于每个用户的操作系统，以及他们的设备上安装了哪些语言环境。换句话说，很难预测这种格式在任何给定的浏览器中会是什么样子。

如果您想以某种特定的方式格式化日期，并完全控制显示的内容，请继续阅读。

## 日期方法

我们需要的几乎所有信息都可以由 date 对象的一些内置方法提供:

```
const date = new Date; // current time & date

date.getFullYear(); // Year
date.getMonth(); // Month of the year 0-11 (0 = January)
date.getDate(); // Date of the month, 1-31
date.getDay(); // Day of the week, 0-6 (0 = Sunday)
date.getHours(); // Hours, 0-23
date.getMinutes(); // Minutes, 0-59
date.getSeconds(); // Seconds, 0-59 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能已经注意到，所有这些方法都返回数字。但是你怎么能把像“星期四”或“十一月”这样的词读出来呢？如果您希望月份或日期数字以零开头，该怎么办？没问题，我们可以用 JavaScript！

## 年

### 得到完整的年份

从 Date 对象中获取年份确实很容易，但是默认情况下是四位数的年份:

```
date.getFullYear(); // returns 2019 
```

Enter fullscreen mode Exit fullscreen mode

### 如果只想要两位数呢？

在 Date 对象中也有一个`getYear()`函数，有时我不小心用它代替了`getFullYear()`。然而，这或多或少是没用的。2019 年，它返回 119。为什么？？因为 JavaScript 里烤出了一个 Y2K bug，尽管 JavaScript 是 1995 年设计的！在 JavaScript 的前五年，人们可以调用 getYear()来表示两位数的年份，然后简单地加上 1900 就可以得到四位数的年份。而且我估计这还是行得通的，因为 1900 + 119 = 2019！

由于`getYear()`函数从 2000 年开始就被破坏了，我建议用这种方法得到一个两位数的年份:

```
function getTwoDigitYear(date) {
    return date.getFullYear() % 100; // returns 19
} 
```

Enter fullscreen mode Exit fullscreen mode

## 月份

### 以两位数显示月份

Date 对象的`getMonth()`函数返回一个介于 0 和 11 之间的数字。在 JavaScript 中处理日期时，这一定是最大的惊喜之一。如果不编写更多的代码，这也很可能使这个方法变得毫无用处。让我们看看如何做到这一点。

```
function getTwoDigitMonth(date) {
    // add one to month to make it 1-12 instead of 0-11
    const month = date.getMonth() + 1;

    if (month < 10) {
        // add a 0 to the start if necessary
        return `0${month}`;
    } else {
        // for 10, 11 and 12, just return the month
        return month.toString();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 将月份显示为字符串

如果我们想将月份显示为类似“二月”或“三月”的文本字符串，那么我们需要使用一个包含所有月份的 JavaScript 数组。事实上，这就是为什么`getMonth()`方法返回 0 到 11 之间的数字，因为数组也从 0 开始计数！

```
function getMonthName(date) {
    const months = [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December'
    ];

    return months[date.getMonth()];
} 
```

Enter fullscreen mode Exit fullscreen mode

如果要使用月份的短形式，或者只使用单个字符，或者使用另一种语言，则可以很容易地调整上面的代码，以使用您喜欢的任何内容来更改数组的内容。

## 一周的几天

如果要显示一周中的某一天，可能需要显示一些文本。您可以使用我们在上面格式化月份时使用的相同方法。基本上，您只需要定义一个文本数组，并使用`getDay()`结果作为索引来访问它。

```
function getWeekDayName(date) {
    // make sure you start with Sunday
    const weekDays = [
        'Sunday',
        'Monday',
        'Tuesday',
        'Wednesday',
        'Thursday',
        'Friday',
        'Saturday'
    ];

    return weekDays[date.getDay()];
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，如果您想返回不同的文本，如“Sun”或“S ”,只需将数组中的条目替换为您喜欢的内容。

## 一个月中的某一天

一个月中的某一天很简单。你只要打`getDate()`就可以得到号码，不用加一什么的。

### 以两位数显示一个月中的某一天

对于某些日期格式，您可能希望获得两位数的日期数字。这很简单:

```
function getTwoDigitDayOfTheMonth(date) {
    const dayOfTheMonth = date.getDate();

    if (dayOfTheMonth < 10) {
        // add a 0 to the start if necessary
        return `0${dayOfTheMonth}`;
    } else {
        // for 10 or greater, just return the dayOfTheMonth
        return dayOfTheMonth.toString();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 显示一个月中的第几天

如果你想在一个月中的某一天后面加上序号，比如 1 号、2 号、3 号、4 号、21 号等等。，您可以使用 switch 语句轻松地解决这个问题:

```
function getDayOfTheMonthWithOrdinal(date) {
    const dayOfTheMonth = date.getDate();
    const ordinal = getOrdinal(dayOfTheMonth);

    return `${dayOfTheMonth}${ordinal}`;
}

function getOrdinal(number) {
    // using the % modulo operator to get the last digit of the number
    const lastDigitOfNumber = number % 10;

    switch (lastDigitOfNumber) {
        case 1:
            return 'st';
        case 2:
            return 'nd';
        case 3:
            return 'rd';
        default:
            return 'th';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 次

根据你的需要，你也可以应用我们上面使用的技术。假设我们想用“am”或“pm”显示 12 小时制的时间格式，比如“9:45pm”。下面是方法:

```
function formatTwelveHourTime(date) {
    // call functions below to get the pieces we need
    const hour = getHourInTwelveHourClock(date);
    const minute = getTwoDigitMinute(date);
    const meridiem = getMeridiem(date);

    // put it all together
    return `${hour}:${minute}${meridiem}`;
}

function getHourInTwelveHourClock(date) {
    const hour = date.getHours();

    if (hour === 0 || hour === 12) {
        return 12;
    }

    // otherwise, return a number between 1-11
    return hour % 12;
}

function getTwoDigitMinute(date) {
    const minute = date.getMinutes();

    if (minute < 10) {
        // add a 0 to the start if necessary
        return `0${minute}`;
    } else {
        // for 10 or greater, just return the minute
        return minute.toString();
    }
}

function getMeridiem(date) {
    const hour = date.getHours();

    if (hour < 12) {
        return 'am';
    } else {
        return 'pm';
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 汇集一切

我们已经讨论了如何生成各种日期格式的所有不同部分。把所有不同的部分放在一起怎么样？

您可以使用任何您喜欢的方法，但是我建议使用如下的方法，使用模板字符串。

```
function shortDateFormat(date) {
    // use the built-in function here
    const year = date.getFullYear();

    // use the functions we wrote above
    const month = getTwoDigitMonth(date);
    const dayOfTheMonth = getTwoDigitDayOfTheMonth(date);

    // put it all together, eg. "YYYY-MM-DD"
    return `${year}-${month}-${dayOfTheMonth}`;
} 
```

Enter fullscreen mode Exit fullscreen mode

要生成多少格式，就可以创建多少格式函数。这里还有一个例子:

```
function longDateTimeFormat(date) {
    const weekDayName = getWeekDayName(date);
    const monthName = getMonthName(date); 
    const dayOfTheMonth = getDayOfTheMonthWithOrdinal(date);
    const year = date.getFullYear();
    const time = formatTwelveHourTime(date);

    // put it together, eg. "Friday, April 19th, 2019 at 9:45pm"
    return `${weekDayName}, ${monthName}  ${dayOfTheMonth}, ${year} at ${time}`;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我希望我已经为您提供了格式化日期和时间可能需要的所有工具和技术。您也可以在其他方面应用这些技术，比如格式化货币。更重要的是，通过一点点定制 JavaScript，您可以避免项目和用户下载的额外依赖。

* * *

对网页开发感兴趣？[订阅《与杰西一起编码》时事通讯！](https://www.codingwithjesse.com/newsletter)