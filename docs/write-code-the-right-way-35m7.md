# 以正确的方式编写代码

> 原文：<https://dev.to/rexben001/write-code-the-right-way-35m7>

[![Image](img/aaa30bca9e7c8c7d68d4dad476f3fad0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--285P-b8G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AZeDvhI2lKhC-AQWWeNnXXg.jpeg)

**来源:**[https://www . pexels . com/photo/man-in-white-shirt-using-macbook-pro-52608/](https://www.pexels.com/photo/man-in-white-shirt-using-macbook-pro-52608/)

正如我常说的，“编程是人类最伟大的发明之一。它帮助解决了许多问题，弥合了差距，跨越了障碍”。“编程的全部本质就是解决问题”。认为你周围没有需要解决的问题是错误的想法。

除了写代码，最好写一个*健康的*代码。你怎么能识别一个健康的代码呢？

为了让你的代码健康，它必须通过这三个测试。测试包括语义测试、句法测试和语用测试。

**语法测试**

[计算机语言的语法](https://en.wikipedia.org/wiki/Syntax_%28programming_languages%29)是定义符号组合的一组规则，这些组合被认为是该语言中结构正确的文档或片段。可以说是需要遵循的一套规则，才有工作代码。如果你不遵循特定编程语言的语法，你总是会得到一个语法错误。为了修复语法错误，最好的办法是阅读该编程语言的文档。

编程语言 A 的语法不同于编程语言 b。支配英语的规则不同于法语、德语或汉语，编程语言也是如此。

例如，用 JavaScript 打印 Hello world 不同于 Python 和 Elixir。

要打印" Hello world ":

Python = =>print(" Hello world ")

JavaScript = =>console . log(" Hello world ")；

仙丹== > IO.puts“你好世界”

所以，要想让你的代码*健康*，它首先必须通过语法测试。

**语义测试**
在程序设计语言理论中，[语义](https://en.wikipedia.org/wiki/Semantics_%28computer_science%29)是与程序设计语言意义的严格数学研究有关的领域。它通过评估由特定编程语言定义的语法上有效的字符串的含义，显示所涉及的计算来做到这一点。

简单地说，语义关注的是代码的含义。它在做你想让它做的事情吗？在分别用编译器或解释器编译或解释代码之前，您不知道您的代码在语义上是否正确。你的代码可以语法正确，语义正确。当您运行或测试您的代码时，它可能不会引发任何语法错误，并且可能仍然不会给您正确的输出。

例如，你想从用户那里收集两个输入并将它们相加

***Python***

```
a = input(‘Enter first value: ‘)  
b = input(‘Enter second value: ‘)  
c = a + b  
print(‘The sum total of’, a, ‘and’, b, ‘is’, c) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码不会给出语法错误，但是它不会给出正确的输出。

如果 a = 10，b = 5，输出将是 105 而不是 15。

所以，为了让你的代码健康，它必须通过语法测试和语义测试。

实用测试

[实用测试](https://www.todaysoftmag.com/article/1070/pragmatism-in-programming)通常是指编写干净的代码，并以尽可能高效的方式管理代码，使代码易于被编写代码的人和将来阅读代码的人理解。因此，你的代码可能在语法和语义上都是正确的，但在实用上却不正确。它是否遵循最佳实践？一些最佳实践包括避免代码重复、考虑单一责任原则、在命名变量、类、方法、接口时使用好的描述性名称、在代码中添加注释、必须适当缩进等等。

**举例**

***Python***

```
a = int(input(‘Enter first value: ‘))  
b = int(input(‘Enter second value: ‘))  
c = a + b  
print(‘The sum total of’, a, ‘and’, b, ‘is’, c) 
```

Enter fullscreen mode Exit fullscreen mode

为了使代码实际上正确

```
def sum():

 firstVal = int(input(‘Enter first value: ‘))  
 secondVal = int(input(‘Enter second value: ‘)) 

 total = firstVal + secondVal  
 print(‘The sum total of’, firstVal, ‘and’, secondVal, ‘is’, total)  
sum() 
```

Enter fullscreen mode Exit fullscreen mode

对于一个健康的代码来说，它必须在语法上(遵循规则)、语义上(做它应该做的)和实用上(遵循最佳实践)正确。