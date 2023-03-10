# 如何在 JavaScript 中获得数组中的唯一值

> 原文：<https://dev.to/skptricks/how-to-get-unique-values-in-an-array-in-javascript-nnb>

帖子链接:[如何在 JavaScript 中获取数组中的唯一值](https://www.skptricks.com/2018/11/getting-unique-values-in-javascript.html)

在本教程中，我们将讨论如何在 javascript 中获取数组中的唯一值。让我们看看下面的例子，了解更多信息。

方法-1 :
在这个例子中，我们使用 Set 关键字和数组，来自 ES6 特性。集合是值的集合，其中每个值只能出现一次。

let values = ["Sumit "，" Amit "，" Neeraj "，" Alex "，" Skp "，" Sumit "，" Sam "，" Neeraj "，" Obama "]；

function unique(arr){
return array . from(new Set(arr))

}

console.log(唯一(值))

## 输出:

> Array ["Sumit "，" Amit "，" Neeraj "，" Alex "，" Skp "，" Sam "，" Obama"]

方法-2 :
在这个例子中，我们将 Set 关键字与 ES6 特性的 spread 操作符一起使用。集合是值的集合，其中每个值只能出现一次。

let values = ["Sumit "，" Amit "，" Neeraj "，" Alex "，" Skp "，" Sumit "，" Sam "，" Neeraj "，" Obama "]；

函数 unique(arr) {
return [...新设置(arr)]
}

console.log(唯一(值))

## 输出:

> Array ["Sumit "，" Amit "，" Neeraj "，" Alex "，" Skp "，" Sam "，" Obama"]

[![](img/936406765826b4d13a7ee382f809ef8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yvjlMDkQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-R-K35VCStxQ/W_9bRWiQ3aI/AAAAAAAACLU/CJY0yfUlhfM6H7BmDOHOM5Nj1xBKth3rwCLcBGAs/s400/unique.png)