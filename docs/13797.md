# 如何获取网页上所有的输入标签元素？

> 原文：<https://dev.to/sunilc_/how-do-i-get-all-input-tag-elements-on-a-web-page-4i04>

我试图用下面的代码在网页上显示所有的输入元素。

```
document.onload = function() {
    var elements = document.getElementsByTagName("input");
    console.log(elements);
} 
```

但是我看到在一些网页上，我没有得到网页上的输入元素。例如本网页上的网页->[https://www . Wu foo . com/gallery/templates/forms/tufty-referral-form/](https://www.wufoo.com/gallery/templates/forms/tuition-reimbursement-form/)

我做错了什么？