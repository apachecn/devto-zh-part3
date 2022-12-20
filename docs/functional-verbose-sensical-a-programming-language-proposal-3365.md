# 函数式的、冗长的、感性的:一个编程语言提案

> 原文：<https://dev.to/megazear7/functional-verbose-sensical-a-programming-language-proposal-3365>

不久前，我写了一些关于如果标识符可以包含空格，编程语言会是什么样子的快速想法。在我的示例程序中，我还用冒号来设置空格，用等号来比较。前几天，我对这些相同概念的纯功能版本有了一些想法。

这是目前还不存在的编程语言的特点:
唯一的对象是模块、全局变量、函数和参数。

1.  没有变数。这是一种纯粹的函数式语言。
2.  每个全局和参数都是一个流，因此可以有 0 个、1 个或更多的值。
3.  模块定义、导入和导出全局变量和函数。
4.  每个模块可以有一个单独的入口函数，它是在模块运行时运行的函数。
5.  标识符可以包含空格
6.  设置全局和参数默认值使用冒号
7.  条件等式表达式使用单个“=”。
8.  为了便于条件处理，If 语句被简化如下所示。
9.  每个函数只能包含一个表达式，这使得这种语言纯粹是函数性的
10.  错误处理是使用 if 语句完成的。
11.  您可以使用可能不存在的值，然后在 if 语句中进一步处理错误情况。

下面是具有这些特性的编程语言的样子。由于这种编程语言不存在，我不能添加语法高亮。然而，我在我的博客上发布了这个[相同的示例代码，并且能够在那里手动添加语法高亮。](https://www.alexlockhart.me/2019/02/functional-verbose-sensical-programming.html) 

```
module age groups {
  import print from command line output;
  import error from command line output;

  max baby age: 2;
  max kid age: 12;
  max teenager age: 17;

  function tell the user they are a baby {
    print('You are a baby')
  }

  function tell the user that they are a kid {
    print('You are a kid')
  }

  function tell the user that they are a teenager {
    print('You are a teenager')
  }

  function tell the user that they are an adult {
    print('You are an adult')
  }

  export function tell the user what age group they are in (age) {
    ? age <= max baby age :
      tell the user they are a baby
    ? age < max kid age :
      tell the user that they are a kid
    ? age < max teenager age :
      tell the user that they are a teenager
    ? has one value(max teenager age) :
      tell the user that they are an adult
    ? has multiple values(max teenager age) :
      error('Please provide only one age')
    ? no values(max teenager age) :
      error('No age was provided')
    : // this would be the else case, but based upon the above statements it should never be reached.
  }
}

module my program {
  import tell the user what age group they are in from age groups;
  import the users age from command line arguments;

  entry function run the program {
    tell the user what age group they are in(the users age)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

查看我的博客,了解更多我对技术和其他各种话题的思考。