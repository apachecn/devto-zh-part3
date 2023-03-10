# C++中的异常

> 原文：<https://dev.to/bhupesh/exceptions-in-c-296h>

异常是可怕的，但是当它们试图破坏我们的程序流时，我们必须知道如何处理它们。在这篇文章中，我将从 A 走向 E(双关语；)处理 C++ 中的**异常。**

## 目录

*   [基础知识](#basics)
*   [试试](#try)
*   [投掷](#throw)
*   [接住](#catch)
*   [自定义异常](#customexceptions)
*   [std::cerr](#stdcerr)

## 基础知识

让我们先学习基础知识

> 异常是程序在执行过程中可能遇到的运行时异常或异常情况。

C++为我们提供了 3 个特殊的关键字**尝试**、**接住** & **抛出**。让我们试着一个一个地拜访他们。

## 试试

关键字 try 用于引导您想要测试的代码，即所有要测试异常的代码都放在 try 块
中，例如

```
try {
    /* code to be checked */
} 
```

Enter fullscreen mode Exit fullscreen mode

## 扔

在 try 块内部使用 *throw* 关键字抛出一个异常。

```
try {
    throw exception;
} 
```

Enter fullscreen mode Exit fullscreen mode

一个`throw`表达式触发一个异常条件(错误)出现在一个 try 块中。您可以使用任何类型的对象作为抛出表达式的操作数，即`exception`可以是任何对象类型或变量。通常，此对象用于传达有关错误的信息。

## 接住

所以你抛出了一个异常，你想让它被正确处理吗？？这就是第一名*和第二名*比赛的地方。catch 只是一个类似于 try
的代码块

```
catch(type obj) {
    /* code to handle exception */
} 
```

Enter fullscreen mode Exit fullscreen mode

[![exception1](img/14a5313ba7b53a9a8ecee51bae13f8a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p6oF9i_d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.tutorialdost.com/Cpp-Programming-Tutoriimg/CPP-Exception-Handling.png)

若要处理可能引发的异常，请在 try 块之后立即实现一个或多个 catch 块。每个 catch 块指定了它可以处理的异常类型，也就是说，如果你抛出了不止一种类型的`exception`，你将不得不实现多个 catch 块来处理它们。

```
catch(type obj1) {
    /* code to handle exception of type 1*/
}
catch(type obj2) {
    /* code to handle exception of type 2*/
}
catch(type ob3) {
    /* code to handle exception of type 3*/
} 
```

Enter fullscreen mode Exit fullscreen mode

如果由`try`块抛出的对象类型与任何 catch 语句中的任何 *obj* 类型相匹配，则执行该 catch 块。
用于处理任何类型的异常，而不是由您抛出的
省略号(...)用于 catch 块参数中。

```
catch(...) {
    /* Catches any kind of exception */
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，代码结构看起来像

```
try {
    throw obj1;
    throw obj3;
}

catch(type obj1) {
    std::cout<<"Caught Exception of type obj1 ";
}
catch(type obj3) {
    std::cout<<"Caught Exception of type obj3 ";
}
catch(...){
    std::cout<<"Last catch block ";
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，一旦抛出异常，它将与 catch 块逐一匹配，如果找到匹配，则执行该块，否则继续检查匹配。
如果没有匹配的 catch 块，则执行最后一个`catch(...)`块。

## 举例

下面是我们目前所学内容的一个简单实现。程序做两个数的除法，如果分母是 0，它抛出一个异常。

```
#include <iostream>  
int main(){
    int a, b, c;
    std::cout<<"\nEnter Numerator & Denominator : ";
    std::cin >> a >> b;
    // try block to handle exception handling
    try {
        if(b == 0){
            throw "Division by Zero Error";     // throw custom exception
        }
        c = a/b;
        std::cout<<"\nDivision Result = " << c; 
    }
    catch(char* exception){ // catches exception
        std::cout << exception;
    }
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的程序打印

```
Enter Numerator & Denominator : 2 0
Division by Zero Error 
```

Enter fullscreen mode Exit fullscreen mode

## 自定义异常

我们可以通过从类`Exception`继承并覆盖它的`what()`函数来定义我们自己的 C++异常。
这里是一个示例代码

```
#include <iostream> #include <exception>  
class MyException : public exception {
 public:
    const char * what () {
      return "Custom C++ Exception";
   }
};

int main() {
   try {
      throw MyException();
   }catch(MyException e) {
      std::cout << "MyException caught" <<endl;
      std::cout << e.what() <<endl;
   }
   return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的程序会打印

```
MyException caught
Custom C++ Exception 
```

Enter fullscreen mode Exit fullscreen mode

`what()`是`<exception>`头中的一个预定义函数，它只返回一个关于异常的解释字符串。

## [T1】STD::cerr](#stdcerr)

就像我们使用`std::cout`将数据写入标准*输出流*和`std::cin`进行输入一样。
`std::cerr`用于将数据写入标准错误流。

> 名字中的‘c’指的是‘性格’；cerr 的意思是“字符错误(流)”

例如

```
#include <iostream>  int main(){
  std::cerr << "Error Stream !";
} 
```

Enter fullscreen mode Exit fullscreen mode

希望你今天学到了新东西！！:)