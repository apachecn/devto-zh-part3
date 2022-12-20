# 理解数组。JavaScript 中的 From()

> 原文：<https://dev.to/skptricks/understanding-arrayfrom-in-javascript-1cp2>

帖子链接:[了解阵法。JavaScript 中的 from()](https://www.skptricks.com/2018/12/understanding-arrayfrom-in-javascript.html)

本教程解释了如何在 javascript 中使用 Array.from()函数。Array.from()方法从类似数组或可迭代的对象创建一个新的浅复制数组实例。

语法:
Array.from(object，mapFunction，thisValue)

参数说明
对象:必输。要转换为数组
的对象 mapFunction:可选。对数组
中的每一项调用的映射函数 thisValue:可选。执行 mapFunction 时用作此的值

理解 JavaScript 中的 Array.from()

将字符串转换为数组:
var a = Array . from(' Skptricks ')；
console.log(一)

## 输出:-

> 数组["S "，" k "，" p "，" t "，" r "，" I "，" c "，" k "，" S]]

[![](img/d11f3237422f9732cee23beb99e760cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HdAJSOQX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-w1rRcepJhV8/XAJvHsWAicI/AAAAAAAACME/_We9xb3Wo_cUzry8I6x58BHkSIeeNqEqgCLcBGAs/s400/from.png)