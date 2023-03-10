# 各种语言的嘶嘶声

> 原文：<https://dev.to/renegadecoder94/fizz-buzz-in-every-language-7o3>

我注意到这里的一些人正在分享一些编码挑战，我认为分享我自己的一些可能会很有趣(并在这个过程中无耻地推广我的[示例程序](https://github.com/TheRenegadeCoder/sample-programs/)项目)。

## 挑战

Fizz Buzz 是几乎所有业内人士都知道的臭名昭著的面试问题。具有讽刺意味的是，我不知道有谁被问过这个问题。不管怎样，这是一个有趣的问题，因为它涉及到所有的事情:循环、条件、数学等等。并且，有无数的方法可以解决。

本次挑战的规则如下:

> 写一个打印数字 1 到 100 的程序。然而，对于三的倍数，打印“Fizz”而不是数字。同时，对于五的倍数，打印“嗡嗡”而不是数字。对于 3 和 5 的倍数的数字，打印“FizzBuzz”

下面是一段输出:

```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz 
```

Enter fullscreen mode Exit fullscreen mode

如果你觉得有节日气氛，你可以把这些术语换成你喜欢的(例如 EasterEgg，AprilShowers 等)。).在任何情况下，解决方案都应该可以在您选择的任何语言中执行。语言越晦涩越好！

## 解

我将用我最喜欢的 Java 解决方案来开始这个挑战:

```
public class FizzBuzz {
  public static void main(String[] args) {
    for (int i = 1; i < 101; i++) {
      String output = "";
      if (i % 3 == 0) {
        output += "Fizz";
      }
      if (i % 5 == 0) {
        output += "Buzz";
      }
      if (output.isEmpty()) {
        output += i;
      }
      System.out.println(output);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道这个解决方案是如何工作的， [Stuart Irwin 写了一篇关于它的优秀文章](https://sample-programs.therenegadecoder.com/projects/fizz-buzz/java/)。除了这个解决方案，我还在一个名为[样本程序](https://github.com/TheRenegadeCoder/sample-programs/)的回购中收集了其他几个。将您的解决方案放到下面后，您应该会看到它是否从集合中消失了。我们喜欢你的贡献！