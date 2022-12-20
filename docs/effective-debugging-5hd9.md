# 有效调试

> 原文：<https://dev.to/ayelishaamoah/effective-debugging-5hd9>

### 什么是 bug？

bug 是程序中导致意外或错误结果的错误或故障。错误可能有很多原因，包括代码本身的问题，例如打字错误、语法错误和未定义的变量，或者意外行为的问题，这意味着代码运行起来很有趣，但没有产生预期的结果——单元测试擅长捕捉这些类型的错误，例如下面将显示您的程序是否产生意外的结果:

```
fizzbuzz
  prints Fizz if the number is 3 (FAILED - 1)

Failures:

  1) FizzBuzz prints Fizz if the number is 3
     Failure/Error: expect(fizzbuzz(3)).to eq 'Fizz'

       expected: "Fizz"
            got: 3

1 example, 1 failure 
```

Enter fullscreen mode Exit fullscreen mode

要提到的重要一点是，不管你的测试方法如何，如果你遇到一个错误，还没有编写测试用例，你应该编写一个重现错误的测试用例，这将有助于你专注于修复该错误，并突出显示该错误是否再次发生。

### 好的调试流程是什么样子的？

#### 1。收紧环

第一步是找到错误的来源。

```
 2) RandomHappyEmoji::new returns a :D emoji as a string
     Failure/Error: super(HAPPY_EMOJI.sample)

     NameError:
       uninitialized constant HAPPY_EMOJI
     # ./lib/random_happy_emoji.rb:10:in `initialize'
     # ./spec/units/random_happy_emoji_spec.rb:11:in `new'
     # ./spec/units/random_happy_emoji_spec.rb:11:in `block (3 levels) in <top (required)>' 
```

Enter fullscreen mode Exit fullscreen mode

通过阅读错误消息中的堆栈跟踪，您可以找到文件和行号。

```
# ./lib/random_happy_emoji.rb:10:in `initialize' 
```

Enter fullscreen mode Exit fullscreen mode

上面一行突出显示了产生错误信息的文件和行。错误信息包含了很多有用的信息，可以帮助你找到正确的方向——尽管有些语言可能比其他语言更有用。

#### 2。获得错误的可见性

下一步是读取错误所在的代码，看看是否能跟踪代码中发生的事情。有没有什么工具可以帮助你看到你的代码在每一步输出了什么？Ruby 有 p 命令，可以用来返回特定行、变量或方法调用的值。
JavaScript 在你的 chrome 开发工具中有 console.log()和调试器

#### 3。做出改变并运行测试

只有当你对正在发生的事情有一个清晰的概念时，才需要对代码进行修改。检查您的测试是否对错误消息进行了更改——如果没有，您可以撤销该更改。

有一件事我怎么强调都不为过，那就是确保您阅读了您的错误消息，在许多情况下，我都坐在那里挠头，然后才注意到控制台正在喊着类似‘意外令牌；在第五行。如果你注意到这种情况，可能是时候休息一下了。