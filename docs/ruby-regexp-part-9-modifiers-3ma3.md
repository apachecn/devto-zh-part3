# Ruby Regexp 第 9 部分-修饰符

> 原文：<https://dev.to/learnbyexample/ruby-regexp-part-9-modifiers-3ma3>

# 修饰语

就像选项改变从终端使用的命令的默认行为一样，修饰符用于改变 regexp 的各个方面。它们可以应用于整个 regexp，也可以应用于 regexp 的特定部分，两种形式也可以混合使用。当其中一个参数是正则表达式时,`Regexp.union`的神秘输出也将在本章中解释。在正则表达式的说法中，修饰符也被称为**标志**。

为了完整起见，本章将再次讨论已经看到的修饰语。您还将看到如何组合多个修改器。

## 我修改

首先是`i`修饰符，它将在匹配字母时忽略大小写。

```
>> 'Cat' =~ /cat/
=> nil
>> 'Cat' =~ /cat/i
=> 0

>> 'Cat scat CATER cAts'.scan(/cat/i)
=> ["Cat", "cat", "CAT", "cAt"]

# same as: /[a-zA-Z]+/
# can also use: /[A-Z]+/i
>> 'Sample123string42with777numbers'.scan(/[a-z]+/i)
=> ["Sample", "string", "with", "numbers"] 
```

Enter fullscreen mode Exit fullscreen mode

## m 修改

使用`m`修饰符允许`.`元字符也匹配换行符。

```
# by default, the . metacharacter doesn't match newline
>> "Hi there\nHave a Nice Day".sub(/the.*ice/, 'X')
=> "Hi there\nHave a Nice Day"

# m modifier will allow newline character to be matched as well
>> "Hi there\nHave a Nice Day".sub(/the.*ice/m, 'X')
=> "Hi X Day"

# multiple modifiers can be specified next to each other
>> "Hi there\nHave a Nice Day".sub(/the.*day/im, 'Bye')
=> "Hi Bye" 
```

Enter fullscreen mode Exit fullscreen mode

## 或修改

`o`修饰符将 regexp 定义中的`#{}`插值限制为只执行一次，即使是在循环中。或者，您可以简单地用 regexp 定义分配一个变量，并在循环中使用它，而不需要使用`o`修饰符。

```
>> words = %w[car bike bus auto train plane]

# as 'o' modifier is used, expression inside #{} will be evaluated only once
# and not calculated again and again every iteration
>> n = 2
?> for w in words
?>     puts w if w.match?(/\A\w{#{2**n}}\z/o)
>> end
bike
auto

# here, expression result is not constant, so don't use 'o' modifier
# with 'o' modifier, there'll be no match because #{n} will be '1' always
>> n = 1
?> for w in words
?>     puts w if w.match?(/\A\w{#{n}}\z/)
?>     n += 1
>> end
bus
auto
train 
```

Enter fullscreen mode Exit fullscreen mode

## x 修改

`x`修饰符是另一个类似于命名捕获组的条款，有助于增加正则表达式定义的清晰度。这个修饰符允许使用文字空格进行对齐，并在`#`字符后添加注释，将复杂的 regexp 分解成多行注释。

```
# same as: pat = /\A((?:[^,]+,){3})([^,]+)/
>> pat = /\A(                 # group-1, captures first 3 columns
              (?:[^,]+,){3}   # non-capturing group to get the 3 columns
            )
            ([^,]+)           # group-2, captures 4th column
         /x

>> '1,2,3,4,5,6,7'.sub(pat, '\1(\2)')
=> "1,2,3,(4),5,6,7" 
```

Enter fullscreen mode Exit fullscreen mode

由于空格和`#`字符在使用`x`修饰符时有特殊的含义，所以必须对它们进行转义或者用反斜杠转义序列来表示，以便在字面上匹配它们。详见 [ruby-doc:自由间距模式及注释](https://ruby-doc.org/core-2.7.1/Regexp.html#class-Regexp-label-Free-Spacing+Mode+and+Comments)。

```
>> 'cat and dog'.match?(/t a/x)
=> false
>> 'cat and dog'.match?(/t\ a/x)
=> true
>> 'cat and dog'.match?(/t\x20a/x)
=> true

>> 'foo a#b 123'[/a#b/x]
=> "a"
>> 'foo a#b 123'[/a\#b/x]
=> "a#b" 
```

Enter fullscreen mode Exit fullscreen mode

## 行内注释

也可以使用独立于`x`修饰符的`(?#comment)`分组来添加注释。

```
>> pat = /\A((?:[^,]+,){3})(?#3-cols)([^,]+)(?#4th-col)/

>> '1,2,3,4,5,6,7'.sub(pat, '\1(\2)')
=> "1,2,3,(4),5,6,7" 
```

Enter fullscreen mode Exit fullscreen mode

## 行内修饰符

要将修饰符应用于 regexp 的特定部分，请在特殊的分组语法中指定它们。这将覆盖应用于整个 regexp 定义的修饰符(如果有的话)。语法变化如下:

*   `(?modifiers:pat)`将仅对该正则表达式部分应用修饰符
*   `(?-modifiers:pat)`将仅对该正则表达式部分否定修饰符
*   `(?modifiers-modifiers:pat)`将仅对该正则表达式部分应用和否定特定的修饰符
*   `(?modifiers)`当`:pat`没有在分组中使用时，修饰语(包括否定)将从这一点开始应用

通过这些方式，可以仅在需要的地方精确地指定修饰符。从下面的例子中可以看出，这些不像捕获组。

```
# case-insensitive only for 'cat' portion
>> 'Cat scatter CATER cAts'.scan(/(?i:cat)[a-z]*\b/)
=> ["Cat", "catter", "cAts"]
# same thing by overriding overall modifier
>> 'Cat scatter CATER cAts'.scan(/cat(?-i)[a-z]*\b/i)
=> ["Cat", "catter", "cAts"]

# case-sensitive only for 'Cat'
>> 'Cat SCatTeR CATER cAts'.scan(/(?-i:Cat)[a-z]*\b/i)
=> ["Cat", "CatTeR"]
# same thing without overall modifier
>> 'Cat SCatTeR CATER cAts'.scan(/Cat(?i)[a-z]*\b/)
=> ["Cat", "CatTeR"] 
```

Enter fullscreen mode Exit fullscreen mode

所以，现在当参数之一是 regexp 时，您应该能够解码`Regexp.union`的输出。

```
>> Regexp.union(/^cat/i, '123')
=> /(?i-mx:^cat)|123/

>> Regexp.union(/cat/, 'a^b', /the.*ice/im)
=> /(?-mix:cat)|a\^b|(?mi-x:the.*ice)/ 
```

Enter fullscreen mode Exit fullscreen mode

## 习题

对于练习问题，请访问 GitHub 上本书资源库中的 [Exercises.md](https://github.com/learnbyexample/Ruby_Regexp/blob/master/exercises/Exercises.md) 文件。