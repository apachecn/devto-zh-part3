# 检测到冗余的“return ”- Ruby 编程。

> 原文：<https://dev.to/chintukarthi/redundant-return-detected---ruby-programming-gac>

作为一名软件开发人员，我们被期望从事新技术的工作，而不管我们以前的工作经验是什么语言。只要我们熟悉新语言的语法，这没什么大不了的。

我最近换了工作，被期望在 ruby on rails 上工作。

我正在处理一个代码，并从一个函数返回一个值，在我使用 lint 检查我的代码质量之前，我没有看到任何错误或警告。它给了我警告，

`Redundant 'return' detected`

我对此一无所知，因为我确信我的代码是好的。在激烈的谷歌搜索之后，我发现默认情况下 ruby 不期望函数返回值。

您可以在 stack overflow:[https://stack overflow . com/questions/1023146/is-it-good-style-to-explicitly-return-in-ruby](https://stackoverflow.com/questions/1023146/is-it-good-style-to-explicitly-return-in-ruby)中查看该问题

修改后，我的函数看起来像这样:

```
def some_function(x)
  y = 33
  unless x.blank?
    y = if x == 3
          10
        elsif x == 5
          16
        else
          nil
        end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

驱动代码:

```
x = ''
z = some_function(x)
print z 
```

Enter fullscreen mode Exit fullscreen mode

即使在修改之后，我的函数也没有返回预期的值。一旦进入该函数，就应该返回默认设置的`y value as '33'`。

它返回了`nil`而不是预期的`output : '33'`
我被弄糊涂了，开始调试代码。后来我发现了问题。
问题是，为了让函数返回值，我们应该返回最终变量。

因此，我们可以简单地在末尾使用变量名，而不是使用关键字`'return'`，就像这样:

```
def some_function(x)
  y = 33
  unless x.blank?
    y = if x == 3
          10
        elsif x == 5
          16
        else
          nil
        end
  end
  y #change made here to return the final value of y
end 
```

Enter fullscreen mode Exit fullscreen mode

所以不管变量的最终值是多少，它都会被返回，这就解决了问题。