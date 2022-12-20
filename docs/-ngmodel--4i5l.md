# 是的，[(ngModel)]

> 原文：<https://dev.to/slumboy/-ngmodel--4i5l>

## 【你好，你好，你好，你好，在这么长的时间内(工作范围很大，很抱歉 _ _ _ _ _ _ _ 大家)

## 好了，这样就不会浪费时间表了！我的天啊！我的天啊！希克

现在，我们每天都在用他，还在做双路飞行，然后呢？

* * *

事实上，[ngModel]当我们这样写的时候，就是一条路

*   我们可以使用我们自己编写的 pipe
*   我们可以转换所有的价值
*   我们可以让它按我们的意愿呈现
*   还有其他一些，比如说，

```
<input [ngModel]="item.value | useMyPipeToFormatThatValue" 
      (ngModelChange)="item.value=$event" name="inputField" type="text" /> 
```

* * *

* *如果有任何错误，请在此处道歉。