# 未捕获的范围错误:JavaScript 中的最大调用堆栈

> 原文：<https://dev.to/lambdatest/uncaught-rangeerror-maximum-call-stack-in-javascript-16n7>

错误发生在你最意想不到的地方，JS 开发人员每天都要面对这个问题。

[![](img/807e19fb06b4d61a28e89049a0ad649a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mJ6F_CiU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AsxrOlD2CgkHcO9lw.png)

有两种方法可以获得这些精彩的错误消息:

## 1)非终止递归函数

浏览器为所有数据类型分配内存。有时一遍又一遍地调用递归函数，会导致浏览器向您发送这条消息，因为可以分配给您使用的内存不是无限的。

对于一个程序员来说，没有什么比一个非终结函数或一种递归方法更痛苦的了，因为它往往会陷入无限循环。

在调用函数的时候要考虑周全，并且预演是防止它们的最佳实践。

最大调用堆栈溢出，冲走了您正确运行代码的希望。(XD)

```
var a = new Array(4294967295);  //OK
var b = new Array(-1); //range error

var num = 2.555555;
document.writeln(num.toExponential(4));  //OK
document.writeln(num.toExponential(-2)); //range error!

num = 2.9999;
document.writeln(num.toFixed(2));   //OK
document.writeln(num.toFixed(25));  //range error!

num = 2.3456;
document.writeln(num.toPrecision(1));   //OK
document.writeln(num.toPrecision(22));  //range error! 
```

Enter fullscreen mode Exit fullscreen mode

***嘿！你知道[随机小数生成器](https://www.lambdatest.com/free-online-tools/random-decimal-fraction-generator?utm_source=devto&utm_medium=organic&utm_campaign=jul18_sd&utm_term=sd&utm_content=free_tools)允许用户生成随机小数。该工具还配置您输入的每个数字的位数以及要生成的十进制数。***

## **2)超出范围**

如果有人问你叫什么名字？

你不会回答“2000 年”。

```
var num = 1;
try {
     num.toPrecision(500);  //no can't have 500 significant digits
    }
catch(err) {
     document.getElementById("mylife").innerHTML =err.name;
           } 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 中的某些函数有您可以给出的输入范围。始终小心范围。有时在编写脚本时，我们在执行任务时使用了最终超出范围的函数。如果在实现过程中跟踪所使用的变量类型的范围，这些错误很容易解决。

***看看这个:[随机 GUID 生成器](https://www.lambdatest.com/free-online-tools/random-guid-generator?utm_source=devto&utm_medium=organic&utm_campaign=jul18_sd&utm_term=sd&utm_content=free_tools)——这个在线工具让你通过 JavaScript 生成全球唯一标识符字符串，这意味着它们不会通过互联网发送。您可以使用 GUID 代码来序列化 Windows & Mac 软件或为您的在线商店创建优惠券代码。***

而 Chrome 这样的浏览器会给出错误通知，IE 则会崩溃。

检查有效的输入范围应该是重中之重。

***例子:***

***Number.toFixed(数字)0 到 20***

***number . top 精度(位数)1 到 21***

***【0 位数】到 20 位***

***Null 不为 0***

希望这个博客能对程序员们令人沮丧的艰苦工作有所帮助。

***你知道吗，[随机小数发生器](https://www.lambdatest.com/free-online-tools/random-decimal-generator?utm_source=devto&utm_medium=organic&utm_campaign=jul18_sd&utm_term=sd&utm_content=free_tools)在[0，1]区间或者任何其他想要的区间产生一个随机小数。该程序允许您设置起始分数、结束分数和每个分数的期望精度。***

让我们也从你的错误中学习，请评论。