# Ruby Regexp 第 3 部分——替代和转义

> 原文：<https://dev.to/learnbyexample/ruby-regexp-part-3-alternatives-5cj9>

# 交替和分组

很多时候，您希望检查输入字符串是否匹配多个模式。例如，一个物体的颜色是*绿色*还是*蓝色*还是*红色*。用编程术语来说，你需要执行 OR 条件。本章将展示如何在这种情况下使用交替。这些模式之间可以有一些共同的元素，在这种情况下，分组有助于形成更简洁的表达式。本章还将讨论用于确定哪个选项胜出的优先规则。

## 或有条件

如果满足任何条件，与逻辑 OR 组合的条件表达式的计算结果为`true`。类似地，在正则表达式中，可以使用`|`元字符组合多个模式来表示逻辑 OR。如果在输入字符串中找到任何替代模式，匹配将会成功。这些替代项拥有正则表达式的全部功能，例如它们可以拥有自己的独立锚点。这里有一些例子。

```
# match either 'cat' or 'dog'
>> 'I like cats'.match?(/cat|dog/)
=> true
>> 'I like dogs'.match?(/cat|dog/)
=> true
>> 'I like parrots'.match?(/cat|dog/)
=> false

# replace either 'cat' at start of string or 'cat' at end of word
>> 'catapults concatenate cat scat'.gsub(/\Acat|cat\b/, 'X')
=> "Xapults concatenate X sX"

# replace either 'cat' or 'dog' or 'fox' with 'mammal'
>> 'cat dog bee parrot fox'.gsub(/cat|dog|fox/, 'mammal')
=> "mammal mammal bee parrot mammal" 
```

Enter fullscreen mode Exit fullscreen mode

## Regexp.union 方法

你可能会从上面的例子中推断出，有些情况下需要大量的交替。`Regexp.union`方法可以用来自动建立交替列表。它接受数组作为参数或逗号分隔的参数列表。

```
>> Regexp.union('car', 'jeep')
=> /car|jeep/

>> words = %w[cat dog fox]
>> pat = Regexp.union(words)
>> pat
=> /cat|dog|fox/
>> 'cat dog bee parrot fox'.gsub(pat, 'mammal')
=> "mammal mammal bee parrot mammal" 
```

Enter fullscreen mode Exit fullscreen mode

> 在上面的例子中，元素不包含任何特殊的正则表达式字符。具有元字符的字符串将在 [Regexp.escape 方法](https://learnbyexample.github.io/Ruby_Regexp/escaping-metacharacters.html#regexpescape-method)一节中讨论。

## 分组

通常，正则表达式的替代项之间有一些共同点。它可以是普通字符或 regexp 限定符，如锚点。在这种情况下，可以使用一对括号元字符将它们分组。类似于数学中的`a(b+c)d = abd+acd`，你在正则表达式中得到`a(b|c)d = abd|acd`。

```
# without grouping
>> 'red reform read arrest'.gsub(/reform|rest/, 'X')
=> "red X read arX"
# with grouping
>> 'red reform read arrest'.gsub(/re(form|st)/, 'X')
=> "red X read arX"

# without grouping
>> 'par spare part party'.gsub(/\bpar\b|\bpart\b/, 'X')
=> "X spare X party"
# taking out common anchors
>> 'par spare part party'.gsub(/\b(par|part)\b/, 'X')
=> "X spare X party"
# taking out common characters as well
# you'll later learn a better technique instead of using empty alternate
>> 'par spare part party'.gsub(/\bpar(|t)\b/, 'X')
=> "X spare X party" 
```

Enter fullscreen mode Exit fullscreen mode

> 分组不仅仅是形成更简洁的 regexp，还有更多功能。在接下来的章节中，我们将会讨论这些问题。

## Regexp.source 方法

`Regexp.source`方法有助于在另一个 regexp 中插入一个 regexp 文本。例如，向使用`Regexp.union`方法创建的交替列表添加锚。

```
>> words = %w[cat par]
>> alt = Regexp.union(words)
>> alt
=> /cat|par/
>> alt_w = /\b(#{alt.source})\b/
>> alt_w
=> /\b(cat|par)\b/

>> 'cater cat concatenate par spare'.gsub(alt, 'X')
=> "Xer X conXenate X sXe"
>> 'cater cat concatenate par spare'.gsub(alt_w, 'X')
=> "cater X concatenate X spare" 
```

Enter fullscreen mode Exit fullscreen mode

> 上面的例子没有`Regexp.source`方法也可以工作，但是你会看到`/\b(#{alt})\b/`给出了`/\b((?-mix:cat|par))\b/`而不是`/\b(cat|par)\b/`。它们的含义将在[修饰语](https://learnbyexample.github.io/Ruby_Regexp/modifiers.html)章节中解释。

## 优先规则

使用交替时会有一些棘手的情况。如果用它来测试一个字符串输入的匹配，就不会有歧义。然而，对于字符串替换之类的其他事情，它取决于几个因素。比方说，你想替换掉`are`或者`spared`——哪个应该优先？更大的单词`spared`或其中的子串`are`还是基于其他东西？

在 Ruby 中，与输入字符串中最早匹配的 regexp 替代项优先。Regexp 操作符`=~`有助于说明这个概念。

```
>> words = 'lion elephant are rope not'

>> words =~ /on/
=> 2
>> words =~ /ant/
=> 10

# starting index of 'on' < index of 'ant' for given string input
# so 'on' will be replaced irrespective of order of regexp
>> words.sub(/on|ant/, 'X')
=> "liX elephant are rope not"
>> words.sub(/ant|on/, 'X')
=> "liX elephant are rope not" 
```

Enter fullscreen mode Exit fullscreen mode

那么，如果两个或更多的选择在同一个索引上匹配，会发生什么呢？然后，优先级按照声明的顺序从左到右排列。

```
>> mood = 'best years'

>> mood =~ /year/
=> 5
>> mood =~ /years/
=> 5

# starting index for 'year' and 'years' will always be same
# so, which one gets replaced depends on the order of alternation
>> mood.sub(/year|years/, 'X')
=> "best Xs"
>> mood.sub(/years|year/, 'X')
=> "best X" 
```

Enter fullscreen mode Exit fullscreen mode

用`gsub`说明问题的另一个例子。

```
>> words = 'ear xerox at mare part learn eye'

# this is going to be same as: gsub(/ar/, 'X')
>> words.gsub(/ar|are|art/, 'X')
=> "eX xerox at mXe pXt leXn eye"

# this is going to be same as: gsub(/are|ar/, 'X')
>> words.gsub(/are|ar|art/, 'X')
=> "eX xerox at mX pXt leXn eye"

# phew, finally this one works as needed
>> words.gsub(/are|art|ar/, 'X')
=> "eX xerox at mX pX leXn eye" 
```

Enter fullscreen mode Exit fullscreen mode

如果您不希望子字符串破坏您的替换，一个可靠的解决方法是根据长度对替换进行排序，最长的优先。

```
>> words = %w[hand handy handful]

>> alt = Regexp.union(words.sort_by { |w| -w.length })
>> alt
=> /handful|handy|hand/

>> 'hands handful handed handy'.gsub(alt, 'X')
=> "Xs X Xed X"

# without sorting, order will come into play
>> 'hands handful handed handy'.gsub(Regexp.union(words), 'X')
=> "Xs Xful Xed Xy" 
```

Enter fullscreen mode Exit fullscreen mode

# 转义元字符

本章将展示如何逐字匹配元字符，无论是手动还是编程构造的模式。您还将了解 regexp 支持的转义序列以及它们与字符串的不同之处。

## 用\

您已经看到了一些元字符和转义序列，它们有助于组成 regexp 文本。要逐字匹配元字符，即删除它们的特殊含义，请在这些字符前加上一个`\`字符。要表示字面上的`\`字符，使用`\\`。

为了增加示例的趣味，下面使用了块的形式，用一个表达式修改字符串的匹配部分。在后面的章节中，您将看到更多直接处理匹配部分的方法。

```
# even though ^ is not being used as anchor, it won't be matched literally
>> 'a^2 + b^2 - C*3'.match?(/b^2/)
=> false
# escaping will work
>> 'a^2 + b^2 - C*3'.gsub(/(a|b)\^2/) { |m| m.upcase }
=> "A^2 + B^2 - C*3"

# match ( or ) literally
>> '(a*b) + c'.gsub(/\(|\)/, '')
=> "a*b + c"

>> '\learn\by\example'.gsub(/\\/, '/')
=> "/learn/by/example" 
```

Enter fullscreen mode Exit fullscreen mode

正如前面所强调的，正则表达式只是另一种处理文本的工具。本书中的一些例子和练习也可以用普通的字符串方法解决。对于真实世界的用例，首先问问自己是否需要 regexp？

```
>> eqn = 'f*(a^b) - 3*(a^b)'

# straightforward search and replace, no need regexp shenanigans
>> eqn.gsub('(a^b)', 'c')
=> "f*c - 3*c" 
```

Enter fullscreen mode Exit fullscreen mode

## Regexp.escape 方法

如何在动态构造 regexp 时转义所有元字符？放轻松，`Regexp.escape`方法已经覆盖了你。不需要手动处理所有的元字符，也不需要担心未来版本的变化。

```
>> eqn = 'f*(a^b) - 3*(a^b)'
>> expr = '(a^b)'

>> puts Regexp.escape(expr)
\(a\^b\)

# replace only at the end of string
>> eqn.sub(/#{Regexp.escape(expr)}\z/, 'c')
=> "f*(a^b) - 3*c" 
```

Enter fullscreen mode Exit fullscreen mode

`Regexp.union`方法自动为字符串参数应用转义。

```
# array of strings, assume alternation precedence sorting isn't needed
>> terms = %w[a_42 (a^b) 2|3]

>> pat = Regexp.union(terms)
>> pat
=> /a_42|\(a\^b\)|2\|3/

>> 'ba_423 (a^b)c 2|3 a^b'.gsub(pat, 'X')
=> "bX3 Xc X a^b" 
```

Enter fullscreen mode Exit fullscreen mode

`Regexp.union`也将正确处理混合字符串和正则表达式模式。下面输出中的`(?-mix:`将在[修饰符](https://learnbyexample.github.io/Ruby_Regexp/modifiers.html)一章中解释。

```
>> Regexp.union(/^cat|dog$/, 'a^b')
=> /(?-mix:^cat|dog$)|a\^b/ 
```

Enter fullscreen mode Exit fullscreen mode

## 转义分隔符

另一个跟踪转义的字符是用于定义 regexp 文字的分隔符。或者，您可以使用不同于`/`的分隔符来定义 regexp 文字，使用`%r`来避免转义。此外，你不必担心`#{}`插值中的非转义分隔符。

```
>> path = '/abc/123/foo/baz/ip.txt'

# \/ is also known as 'leaning toothpick syndrome'
>> path.sub(/\A\/abc\/123\//, '~/')
=> "~/foo/baz/ip.txt"

# a different delimiter improves readability and reduces typos
>> path.sub(%r#\A/abc/123/#, '~/')
=> "~/foo/baz/ip.txt" 
```

Enter fullscreen mode Exit fullscreen mode

## 转义序列

在 regexp 文本中，像 tab 和 newline 这样的字符可以分别用转义序列表示为`\t`和`\n`。这些类似于它们在普通字符串中的处理方式(详见 [ruby-doc: Strings](https://ruby-doc.org/core-2.7.1/doc/syntax/literals_rdoc.html#label-Strings) )。然而，像`\b`(字边界)和`\s`(参见[转义序列字符集](https://learnbyexample.github.io/Ruby_Regexp/character-class.html#escape-sequence-character-sets)部分)这样的转义对于正则表达式是不同的。八进制转义`\nnn`必须是三位数，以避免与[反向引用](https://learnbyexample.github.io/Ruby_Regexp/groupings-and-backreferences.html)冲突。

```
>> "a\tb\tc".gsub(/\t/, ':')
=> "a:b:c"

>> "1\n2\n3".gsub(/\n/, ' ')
=> "1 2 3" 
```

Enter fullscreen mode Exit fullscreen mode

如果没有定义转义序列，它将匹配被转义的字符。例如，`\%`将匹配`%`，而不是`\`后跟`%`。

```
>> 'h%x'.match?(/h\%x/)
=> true
>> 'h\%x'.match?(/h\%x/)
=> false

>> 'hello'.match?(/\l/)
=> true 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用转义符来表示元字符，它将被逐字处理，而不是它的元字符特性。

```
# \x20 is hexadecimal for space character
>> 'h e l l o'.gsub(/\x20/, '')
=> "hello"
# \053 is octal for + character
>> 'a+b'.match?(/a\053b/)
=> true

# \x7c is '|' character
>> '12|30'.gsub(/2\x7c3/, '5')
=> "150"
>> '12|30'.gsub(/2|3/, '5')
=> "15|50" 
```

Enter fullscreen mode Exit fullscreen mode

> 参见 [ASCII 码表](https://ascii.cl/)获取所有 ASCII 字符及其十六进制表示的简便备忘单。

[码点和 unicode 转义](https://learnbyexample.github.io/Ruby_Regexp/unicode.html#codepoints-and-unicode-escapes)部分将讨论 Unicode 字符的转义。

## 习题

对于练习问题，请访问 GitHub 上本书资源库中的 [Exercises.md](https://github.com/learnbyexample/Ruby_Regexp/blob/master/exercises/Exercises.md) 文件。