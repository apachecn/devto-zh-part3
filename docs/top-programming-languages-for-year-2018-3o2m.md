# 2018 年度顶级编程语言

> 原文：<https://dev.to/rlxdprogrammer/top-programming-languages-for-year-2018-3o2m>

**简介**

为一个新项目选择最好的编程语言从来都不是一件容易的事情。有成千上万种编程语言，每一种都有自己的优缺点。经常在程序员论坛上看到一些类似“Java 比 C++好太多”“Python 是有史以来最好的编程语言”的讨论。作为一名程序员，我认为:没有一种编程语言比另一种更好。没有真实的上下文，你不能在它们之间设置一个顺序。而真实的语境就是要做的项目。你可以说“对于这个项目来说，Java 是比 C++更好的选择”，老实说，在开始一个新项目之前，你总是需要决定哪种语言是这个项目的最佳选择。为了能够做出这样的决定，你需要分析项目需求。例如，如果它应该在 Android 手机上运行，你当然不会选择 PHP，或者如果它是一个 web 服务，那么很可能 C 不是最好的选择。您还需要考虑将从事该项目的团队的当前知识水平。因此，如果你的团队在 Java 上有很好的经验，但是他们从来没有使用过 C++，那么选择 Java 是有意义的，即使 C++更适合这个项目。

这些语言的流行程度在不断变化。有一个所谓的 TIOBE 指数，它反映了一种编程语言在某个时间点的流行程度。

我认为它在新年伊始提供了一个很好的概述，以检查 2018 年最受欢迎的编程语言是什么，并对它们进行简要概述。

1.  Java 语言(一种计算机语言，尤用于创建网站)

```

 public class HelloWorld {

    public static void main(String[] args) {        
        System.out.println("Hello, World");
    }

} 
```

3.  C

```

 #include

int main() {
 printf("Hello World\n");
 return 0;
} 
```

5.  C++

```

 #include 
using namespace std;

int main() 
{
    cout << "Hello, World!";
    return 0;
} 
```

7.  计算机编程语言

```

 print("Hello, World!") 
```

9.  C#

```

 using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
                Console.WriteLine("Hello, world!");
                Console.ReadLine();
        }
    }
} 
```

11.  Visual Basic。网

```

 Module Module1

    Sub Main()
        System.Console.WriteLine("Hello World.")
        System.Console.ReadLine()
        End
    End Sub

End Module 
```

13.  服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

```

 <php
  echo "Hello World!";  
?> 
```

15.  Java Script 语言

```

 console.log("Hello, World!"); 
```

17.  红宝石

```

 puts 'Hello, world!' 
```

19.  Delphi/Object Pascal

```

 program ObjectPascalExample;

type
  THelloWorld = class
  public
    procedure Greet;
  end;

procedure THelloWorld.Greet;
begin
  Writeln('Hello, World!');
end;

var
  HelloWorld: THelloWorld;
begin
  HelloWorld := THelloWorld.Create;
  try
    HelloWorld.Greet;
  finally
    HelloWorld.Free;
  end;
end. 
```