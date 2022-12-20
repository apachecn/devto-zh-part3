# 为什么功能减速超越

> 原文：<https://dev.to/yashwanth2804/why-overriding-function-declaration---36n5>

```
 var myName = "Richard"; 

function myName () {
console.log ("Rich");
}

console.log(typeof myName)

////output is string 
```

但是当我以其他方式编写函数时，比如上面的代码，给出不同的输出

```
var myName = "Richard"; 

myName = function() {
console.log ("Rich");
}

console.log(typeof myName)

////output is  function 
```