# 有趣的飞镖技巧

> 原文：<https://dev.to/thinkdigitalsoftware/fun-dart-tips-3d5g>

**缩写变量声明:**
以下是在同一语句中声明多个变量的有效 dart 方式:

```
String managers = "Managers",
      teachers = "Teachers",
      families = "Families"; 
```

在只能使用 oneliner 的地方运行匿名函数。例如，构造函数内部的一个参数:

```
var status = "Arriving";
var greeting = (){
   switch(a) {
     case "Arriving":
       return "Hi!";
     case "Leaving":
       return "Bye!";
     case "Staying":
       return "Make yourself at home!";
     default:
       return "hmmmm";
    }
}() // <-- note. You have to include this ending parenthesis 
//or the function will never evaluate. You're calling the function here. 
//Just like any other function, you have to include the parenthesis in order to run it. 
```

给变量赋值会返回该值。这样你可以返回赋值，它会打印出赋值的值。

```
List a = [1, 0, 0];
print((a[1] = 2)); // prints 2 
```

下面也行。

```
String b;
print(b = "This is what I'm assigning to 'b'"); 
// prints "This is what I'm assigning to 'b' 
```

当您打印(一个..[2] = 3)，
它赋值并返回整个列表。这允许您在修改列表时返回列表...

```
print((a..[2] = 3)); // prints [1, 2, 3]

List c = a..[0] = 9999999;
print(c); // prints [9999999, 2, 3] 
```

这些只是我现在拥有的一些。我会补充更多，因为我想出了他们！