# Ruby Regexp 第 2 部分-锚点

> 原文：<https://dev.to/learnbyexample/ruby-regexp-part-2---anchors-50pe>

现在您已经熟悉了 regexp 语法和一些方法，下一步是了解正则表达式的特殊特性。在这一章中，你将学习如何限定一个模式。可以指定限制，而不是匹配给定输入字符串中的任何位置。现在，您将看到已经是正则表达式特性的一部分。在后面的章节中，您将学习如何定义自己的限制规则。

这些限制是通过为某些字符和转义序列赋予特殊含义而实现的。在正则表达式中，具有特殊含义的字符被称为**元字符**。如果您需要逐字匹配这些字符，您需要用一个`\`字符对它们进行转义(在[转义元字符](https://learnbyexample.github.io/Ruby_Regexp/escaping-metacharacters.html#escaping-with-)一节中讨论)。

## 串锚

这个限制是关于限定 regexp 只在输入字符串的开头或结尾匹配。这些方法提供了类似于字符串方法`start_with?`和`end_with?`的功能。有三种不同的转义序列与字符串级别的正则表达式定位点相关。首先是`\A`，它将匹配限制在字符串的开头。

```
# \A is placed as a prefix to the search term
>> 'cater'.match?(/\Acat/)
=> true
>> 'concatenation'.match?(/\Acat/)
=> false

>> "hi hello\ntop spot".match?(/\Ahi/)
=> true
>> "hi hello\ntop spot".match?(/\Atop/)
=> false 
```

Enter fullscreen mode Exit fullscreen mode

为了将匹配限制在字符串的末尾，使用了`\z`。

```
# \z is placed as a suffix to the search term
>> 'spare'.match?(/are\z/)
=> true
>> 'nearest'.match?(/are\z/)
=> false

>> words = %w[surrender unicorn newer door empty eel pest]
>> words.grep(/er\z/)
=> ["surrender", "newer"]
>> words.grep(/t\z/)
=> ["pest"] 
```

Enter fullscreen mode Exit fullscreen mode

弦锚的另一端`\Z`。它类似于`\z`,但是如果换行符是最后一个字符，那么`\Z`允许在换行符之前进行匹配。

```
# same result for both \z and \Z
# as there is no newline character at the end of string
>> 'dare'.sub(/are\z/, 'X')
=> "dX"
>> 'dare'.sub(/are\Z/, 'X')
=> "dX"

# different results as there is a newline character at the end of string
>> "dare\n".sub(/are\z/, 'X')
=> "dare\n"
>> "dare\n".sub(/are\Z/, 'X')
=> "dX\n" 
```

Enter fullscreen mode Exit fullscreen mode

结合开始和结束字符串锚点，您可以将匹配限制为整个字符串。类似于使用`==`操作符比较字符串。

```
>> 'cat'.match?(/\Acat\z/)
=> true
>> 'cater'.match?(/\Acat\z/)
=> false
>> 'concatenation'.match?(/\Acat\z/)
=> false 
```

Enter fullscreen mode Exit fullscreen mode

锚可以单独用作模式。帮助在字符串的开头或结尾插入文本，模拟字符串连接操作。这些可能看起来不像有用的功能，但是与其他 regexp 特性结合起来，它们就变成了一个非常方便的工具。

```
>> 'live'.sub(/\A/, 're')
=> "relive"
>> 'send'.sub(/\A/, 're')
=> "resend"

>> 'cat'.sub(/\z/, 'er')
=> "cater"
>> 'hack'.sub(/\z/, 'er')
=> "hacker" 
```

Enter fullscreen mode Exit fullscreen mode

## 线锚

字符串输入可以包含单行或多行。换行符`\n`用作行分隔符。有两个行锚点，`^`元字符用于匹配行首，`$`用于匹配行尾。如果输入字符串中没有换行符，它们的行为将分别与`\A`和`\z`锚点相同。

```
>> pets = 'cat and dog'

>> pets.match?(/^cat/)
=> true
>> pets.match?(/^dog/)
=> false

>> pets.match?(/dog$/)
=> true

>> pets.match?(/^dog$/)
=> false 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些多行的例子来区分线锚和弦锚。

```
# check if any line in the string starts with 'top'
>> "hi hello\ntop spot".match?(/^top/)
=> true

# check if any line in the string ends with 'er'
>> "spare\npar\ndare".match?(/er$/)
=> false

# filter all lines ending with 'are'
>> "spare\npar\ndare".each_line.grep(/are$/)
=> ["spare\n", "dare"]

# check if any complete line in the string is 'par'
>> "spare\npar\ndare".match?(/^par$/)
=> true 
```

Enter fullscreen mode Exit fullscreen mode

就像字符串锚，你可以使用线锚本身作为一个模式。这里将使用`gsub`和`puts`来更好地说明转换。如果你没有指定一个替换字符串或者传递一个块，`gsub`方法返回一个枚举数。这为使用所有这些优秀的枚举器和可枚举方法铺平了道路。

```
>> str = "catapults\nconcatenate\ncat"

>> puts str.gsub(/^/, '1: ')
1: catapults
1: concatenate
1: cat
>> puts str.gsub(/^/).with_index(1) { |m, i| "#{i}: " }
1: catapults
2: concatenate
3: cat

>> puts str.gsub(/$/, '.')
catapults.
concatenate.
cat. 
```

Enter fullscreen mode Exit fullscreen mode

如果字符串末尾有一个换行符，则有一个额外的行尾匹配，但没有额外的行首匹配。

```
>> puts "1\n2\n".gsub(/^/, 'foo ')
foo 1
foo 2
>> puts "1\n\n".gsub(/^/, 'foo ')
foo 1
foo 

# note the number of lines in output
>> puts "1\n2\n".gsub(/$/, ' baz')
1 baz
2 baz
 baz
>> puts "1\n\n".gsub(/$/, ' baz')
1 baz
 baz
 baz 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你正在处理基于 Windows 操作系统的文本文件，你必须先将`\r\n`行尾转换成`\n`。这很容易由许多 Ruby 方法来处理。例如，您可以指定哪个行尾用于`File.open`方法，默认情况下`split`字符串方法处理所有空格，等等。或者，您可以用量词将`\r`作为可选字符处理(参见[贪婪量词](https://learnbyexample.github.io/Ruby_Regexp/dot-metacharacter-and-quantifiers.html#greedy-quantifiers)一节)。

## 字锚

第三种类型的限制是单词锚。字母(不区分大小写)、数字和下划线字符都可以作为单词字符。你可能想知道为什么还有数字和下划线，而不是只有字母？这来自于变量和函数的命名约定——通常允许使用字母、数字和下划线。所以，这个定义更倾向于编程语言，而不是自然语言。

转义序列`\b`表示单词边界。这对单词的开始和单词的结束锚定都有效。单词开头意味着单词前面的字符是非单词字符，或者没有字符(字符串开头)。同样，单词结束意味着单词后面的字符是非单词字符或者没有字符(字符串结束)。这意味着没有单词字符就不能有单词边界`\b`。

```
>> words = 'par spar apparent spare part'

# replace 'par' irrespective of where it occurs
>> words.gsub(/par/, 'X')
=> "X sX apXent sXe Xt"
# replace 'par' only at the start of word
>> words.gsub(/\bpar/, 'X')
=> "X spar apparent spare Xt"
# replace 'par' only at the end of word
>> words.gsub(/par\b/, 'X')
=> "X sX apparent spare part"
# replace 'par' only if it is not part of another word
>> words.gsub(/\bpar\b/, 'X')
=> "X spar apparent spare part" 
```

Enter fullscreen mode Exit fullscreen mode

使用单词边界本身作为一种模式，你会更有创造力

```
# space separated words to double quoted csv
# note the use of 'tr' string method
>> puts words.gsub(/\b/, '"').tr(' ', ',')
"par","spar","apparent","spare","part"

>> '-----hello-----'.gsub(/\b/, ' ')
=> "----- hello -----"

# make a programming statement more readable
# shown for illustration purpose only, won't work for all cases
>> 'foo_baz=num1+35*42/num2'.gsub(/\b/, ' ')
=> " foo_baz = num1 + 35 * 42 / num2 "
# excess space at start/end of string can be stripped off
# later you'll learn how to add a qualifier so that strip is not needed
>> 'foo_baz=num1+35*42/num2'.gsub(/\b/, ' ').strip
=> "foo_baz = num1 + 35 * 42 / num2" 
```

Enter fullscreen mode Exit fullscreen mode

边界这个词也有一个相反的锚。`\B`匹配`\b`不匹配的地方。这种二元性也会在其他一些逃脱序列中出现。负逻辑在许多文本处理情况下都很方便。但是小心使用它，你可能最终会得到你不想要的东西！

```
>> words = 'par spar apparent spare part'

# replace 'par' if it is not start of word
>> words.gsub(/\Bpar/, 'X')
=> "par sX apXent sXe part"
# replace 'par' at the end of word but not whole word 'par'
>> words.gsub(/\Bpar\b/, 'X')
=> "par sX apparent spare part"
# replace 'par' if it is not end of word
>> words.gsub(/par\B/, 'X')
=> "par spar apXent sXe Xt"
# replace 'par' if it is surrounded by word characters
>> words.gsub(/\Bpar\B/, 'X')
=> "par spar apXent sXe part" 
```

Enter fullscreen mode Exit fullscreen mode

下面是一些比较和对比这两个单词锚的独立模式用法。

```
>> 'copper'.gsub(/\b/, ':')
=> ":copper:"
>> 'copper'.gsub(/\B/, ':')
=> "c:o:p:p:e:r"

>> '-----hello-----'.gsub(/\b/, ' ')
=> "----- hello -----"
>> '-----hello-----'.gsub(/\B/, ' ')
=> " - - - - -h e l l o- - - - - " 
```

Enter fullscreen mode Exit fullscreen mode

## 习题

对于练习问题，请访问 GitHub 上本书资源库中的 [Exercises.md](https://github.com/learnbyexample/Ruby_Regexp/blob/master/exercises/Exercises.md) 文件。