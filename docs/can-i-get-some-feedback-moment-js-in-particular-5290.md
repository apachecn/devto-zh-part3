# 我能得到一些反馈吗？(特别是 Moment.js)

> 原文：<https://dev.to/jackharner/can-i-get-some-feedback-moment-js-in-particular-5290>

我正在为工作设置这个东西，如果客户的订单将在今天或下一个可用的工作日运出，我们将在我们的网站上显示。我们的截止时间是山地时间中午 12 点，我们只装运 M-F。

下面是相关代码:

```
var now = moment().tz("America/Denver");
var availableDays = [1, 2, 3, 4, 5];
var todaysCutoff = now.clone().hour(12);
var expectedProcessing = "";

// If today is after 12 Noon, Expected Processing goes to tomorrow.
if (now > todaysCutoff) {
    expectedProcessing = now.clone().add(1, 'day');
} else {
    expectedProcessing = now.clone();
};

// If Expected Processing is not during the week, set to Monday of next week
if (!availableDays.includes(expectedProcessing.day())) {
    expectedProcessing =  expectedProcessing.add(1, 'week').day(1);
};

$("#processing").html("Expected Ship Date: " + 
    expectedProcessing.format("MMMM Do YYYY hh:mm")
); 
```

Enter fullscreen mode Exit fullscreen mode

基本上，如果是中午以后，就把发货日期定在第二天。如果发货日期是周末，就推到下周一。

据我所知，这很有效，我只是好奇一些比我更了解 Moment.js/moment-timezone.的人的反馈

谢谢！