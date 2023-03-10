# Ruby Regexp 第 4 部分——量词

> 原文：<https://dev.to/learnbyexample/ruby-regexp-part-4-quantifiers-8k4>

# 点元字符和量词

本章介绍点元字符和量词。顾名思义，量词允许您指定匹配一个字符或分组的次数。使用`*`字符串操作符，你可以做类似于`'no' * 5`的事情来得到`"nonononono"`。这可以节省您手动重复的时间，并且能够根据需要以编程方式多次重复 string 对象。量词支持这种简单的重复以及指定重复范围的方式。相对于起始值和结束值，该范围具有有界或无界的灵活性。与点元字符(如果需要，还有交替)结合使用，量词允许您在模式之间构造条件和逻辑。

## 点元字符

点元字符匹配除换行符以外的任何字符。

```
# matches character 'c', any character and then character 't'
>> 'tac tin c.t abc;tuv acute'.gsub(/c.t/, 'X')
=> "taXin X abXuv aXe"

# matches character 'r', any two characters and then character 'd'
>> 'breadth markedly reported overrides'.gsub(/r..d/) { |s| s.upcase }
=> "bREADth maRKEDly repoRTED oveRRIDes"

# matches character '2', any character and then character '3'
>> "42\t33".sub(/2.3/, '8')
=> "483" 
```

Enter fullscreen mode Exit fullscreen mode

参见 [m 修饰符](https://learnbyexample.github.io/Ruby_Regexp/modifiers.html#m-modifier)一节，了解`.`如何匹配换行符。第[章字符类](https://learnbyexample.github.io/Ruby_Regexp/character-class.html)将讨论如何为有限的字符集定义你自己的自定义占位符。

## 拆分方法

本章将另外使用`split`方法来举例说明。`split`方法根据给定的 regexp(或 string)分隔字符串，并返回一个字符串数组。

```
# same as: 'apple-85-mango-70'.split('-')
>> 'apple-85-mango-70'.split(/-/)
=> ["apple", "85", "mango", "70"]

>> 'bus:3:car:5:van'.split(/:.:/)
=> ["bus", "car", "van"]

# optional limit can be specified as second argument
# when limit is positive, you get maximum of limit-1 splits
>> 'apple-85-mango-70'.split(/-/, 2)
=> ["apple", "85-mango-70"] 
```

Enter fullscreen mode Exit fullscreen mode

有关捕获组如何影响`split`方法输出的详细信息，请参见[用捕获组分割](https://learnbyexample.github.io/Ruby_Regexp/working-with-matched-portions.html#split-with-capture-groups)部分。

## 贪婪量词

量词的功能类似于字符串重复操作符和 range 方法。它们既可以应用于字符，也可以应用于分组(还有更多，你将在后面的章节中看到)。除了指定精确数量和有界范围的能力之外，它们还可以匹配无限变化的数量。如果输入字符串能够以多种方式满足不同数量的模式，您可以在三种类型的量词中进行选择，以缩小可能性。在本节中，**贪婪的**类型的量词被涵盖。

首先是`?`元字符，它量化一个字符或组来匹配`0`或`1`时间。换句话说，您将该字符或组作为可选匹配的对象。与交替和分组相比，这导致了更简洁的正则表达式。

```
# same as: /ear|ar/
>> 'far feat flare fear'.gsub(/e?ar/, 'X')
=> "fX feat flXe fX"

# same as: /\bpar(t|)\b/
>> 'par spare part party'.gsub(/\bpart?\b/, 'X')
=> "X spare X party"

# same as: /\b(re.d|red)\b/
>> words = %w[red read ready re;d road redo reed rod]
>> words.grep(/\bre.?d\b/)
=> ["red", "read", "re;d", "reed"]

# same as: /part|parrot/
>> 'par part parrot parent'.gsub(/par(ro)?t/, 'X')
=> "par X X parent"
# same as: /part|parrot|parent/
>> 'par part parrot parent'.gsub(/par(en|ro)?t/, 'X')
=> "par X X X" 
```

Enter fullscreen mode Exit fullscreen mode

`*`元字符量化一个字符或组，以匹配`0`或更多次。没有上限，更多细节将在本章后面讨论。

```
# match 't' followed by zero or more of 'a' followed by 'r'
>> 'tr tear tare steer sitaara'.gsub(/ta*r/, 'X')
=> "X tear Xe steer siXa"

# match 't' followed by zero or more of 'e' or 'a' followed by 'r'
>> 'tr tear tare steer sitaara'.gsub(/t(e|a)*r/, 'X')
=> "X X Xe sX siXa"

# match zero or more of '1' followed by '2'
>> '3111111111125111142'.gsub(/1*2/, 'X')
=> "3X511114X" 
```

Enter fullscreen mode Exit fullscreen mode

下面是一些关于`split`和相关方法的例子。`partition`在第一次匹配时分割输入字符串，由 regexp 匹配的文本也出现在输出中。`rpartition`类似于`partition`，但在最后一场比赛中分裂。

```
# note how '25' and '42' gets split, there is '1' zero times in between them
>> '3111111111125111142'.split(/1*/)
=> ["3", "2", "5", "4", "2"]
# there is '1' zero times at end of string as well, note the use of -1 for limit
>> '3111111111125111142'.split(/1*/, -1)
=> ["3", "2", "5", "4", "2", ""]

>> '3111111111125111142'.partition(/1*2/)
=> ["3", "11111111112", "5111142"]

# last element is empty because there is nothing after 2 at the end of string
>> '3111111111125111142'.rpartition(/1*2/)
=> ["311111111112511114", "2", ""] 
```

Enter fullscreen mode Exit fullscreen mode

`+`元字符量化一个字符或组，以匹配`1`或更多次。类似于`*`的量词，没有上限。更重要的是，这不会出现意外，比如在模式之间或在字符串末尾匹配空字符串。

```
>> 'tr tear tare steer sitaara'.gsub(/ta+r/, 'X')
=> "tr tear Xe steer siXa"
>> 'tr tear tare steer sitaara'.gsub(/t(e|a)+r/, 'X')
=> "tr X Xe sX siXa"

>> '3111111111125111142'.gsub(/1+2/, 'X')
=> "3X5111142"
>> '3111111111125111142'.split(/1+/)
=> ["3", "25", "42"] 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`{}`元字符指定一个整数范围，包括有界和无界。下面列出了使用这个量词的四种方法:

| 模式 | 描述 |
| --- | --- |
| `{m,n}` | 匹配`m`到`n`次 |
| `{m,}` | 匹配至少`m`次 |
| `{,n}` | 最多匹配`n`次(包括`0`次) |
| `{n}` | 精确匹配`n`次 |

```
>> demo = %w[abc ac adc abbc xabbbcz bbb bc abbbbbc]

>> demo.grep(/ab{1,4}c/)
=> ["abc", "abbc", "xabbbcz"]
>> demo.grep(/ab{3,}c/)
=> ["xabbbcz", "abbbbbc"]
>> demo.grep(/ab{,2}c/)
=> ["abc", "ac", "abbc"]
>> demo.grep(/ab{3}c/)
=> ["xabbbcz"] 
```

Enter fullscreen mode Exit fullscreen mode

> 必须对`{}`元字符进行转义，以便逐字匹配它们。然而，与`()`元字符不同，这些元字符有更多的回旋余地。例如，仅逃离`{`就足够了，或者如果它不严格符合上面列出的四种形式中的任何一种，则根本不需要逃离。此外，如果您正在将`{}`量词应用到`#`字符，您需要对`#`进行转义以覆盖插值。

## 还有条件

接下来，如何使用点元字符和量词构造 AND 条件。

```
# match 'Error' followed by zero or more characters followed by 'valid'
>> 'Error: not a valid input'.match?(/Error.*valid/)
=> true

>> 'Error: key not found'.match?(/Error.*valid/)
=> false 
```

Enter fullscreen mode Exit fullscreen mode

为了允许任何顺序的匹配，你也必须引入交替。这对于 2 或 3 个模式来说是可以管理的。有关更简单的方法，请参见[和带回看的条件](https://learnbyexample.github.io/Ruby_Regexp/lookarounds.html#and-conditional-with-lookarounds)部分。

```
>> seq1, seq2 = ['cat and dog', 'dog and cat']
>> seq1.match?(/cat.*dog|dog.*cat/)
=> true
>> seq2.match?(/cat.*dog|dog.*cat/)
=> true

# if you just need true/false result, this would be a scalable approach
>> patterns = [/cat/, /dog/]
>> patterns.all? { |re| seq1.match?(re) }
=> true
>> patterns.all? { |re| seq2.match?(re) }
=> true 
```

Enter fullscreen mode Exit fullscreen mode

## 贪婪是什么意思？

当你使用量词`?`时，如果两个量都满足正则表达式，Ruby 如何决定匹配`0`或`1`时间？例如，考虑这个替换表达式`'foot'.sub(/f.?o/, 'X')`——应该替换`foo`还是`fo`？它总是会替换掉`foo`,因为这些是**贪婪的**量词，意味着它们试图尽可能地匹配。

```
>> 'foot'.sub(/f.?o/, 'X')
=> "Xt"

# a more practical example
# prefix '<' with '\' if it is not already prefixed
# both '<' and '\<' will get replaced with '\<'
>> puts 'blah \< foo < bar \< blah < baz'.gsub(/\\?</, '\<')
blah \< foo \< bar \< blah \< baz

# say goodbye to /handful|handy|hand/ shenanigans
>> 'hand handy handful'.gsub(/hand(y|ful)?/, 'X')
=> "X X X" 
```

Enter fullscreen mode Exit fullscreen mode

但是等等，那么`/Error.*valid/`示例是如何工作的呢？难道`.*`不应该消耗掉`Error`之后的所有角色吗？好问题。正则表达式引擎实际上消耗了所有的字符。然后意识到 regexp 失败，它从字符串末尾返回一个字符，并再次检查是否满足整个 regexp。重复该过程，直到找到匹配或确认失败。用正则表达式的说法，这叫做**回溯**。

```
>> sentence = 'that is quite a fabricated tale'

# /t.*a/ will always match from first 't' to last 'a'
# also, note that 'sub' is being used here, not 'gsub'
>> sentence.sub(/t.*a/, 'X')
=> "Xle"
>> 'star'.sub(/t.*a/, 'X')
=> "sXr"

# matching first 't' to last 'a' for t.*a won't work for these cases
# the regexp engine backtracks until .*q matches and so on
>> sentence.sub(/t.*a.*q.*f/, 'X')
=> "Xabricated tale"
>> sentence.sub(/t.*a.*u/, 'X')
=> "Xite a fabricated tale" 
```

Enter fullscreen mode Exit fullscreen mode

> 对于某些极端情况，回溯可能相当耗时(参见[ruby-doc:Regexp Performance](https://ruby-doc.org/core-2.7.1/Regexp.html#class-Regexp-label-Performance))。甚至是灾难性的(见[cloudflare:2019 年 7 月 2 日 cloud flare 停运详情](https://blog.cloudflare.com/details-of-the-cloudflare-outage-on-july-2-2019/))。

## 非贪婪量词

顾名思义，这些量词会尽可能少地匹配。又称**懒惰的**或**不情愿的**量词。将`?`附加到贪婪量词后，它们就不是贪婪的了。

```
>> 'foot'.sub(/f.??o/, 'X')
=> "Xot"
>> 'frost'.sub(/f.??o/, 'X')
=> "Xst"

>> '123456789'.sub(/.{2,5}?/, 'X')
=> "X3456789"

>> 'green:3.14:teal::brown:oh!:blue'.split(/:.*?:/)
=> ["green", "teal", "brown", "blue"] 
```

Enter fullscreen mode Exit fullscreen mode

和贪婪量词一样，懒惰量词会试图满足整体的 regexp。

```
>> sentence = 'that is quite a fabricated tale'

# /t.*?a/ will always match from first 't' to first 'a'
>> sentence.sub(/t.*?a/, 'X')
=> "Xt is quite a fabricated tale"

# matching first 't' to first 'a' for t.*?a won't work for this case
# so, regexp engine will move forward until .*?f matches and so on
>> sentence.sub(/t.*?a.*?f/, 'X')
=> "Xabricated tale"
# this matches last 'e' after 'q' to satisfy the anchor requirement
>> sentence.sub(/q.*?e$/, 'X')
=> "that is X" 
```

Enter fullscreen mode Exit fullscreen mode

## 所有格量词

在贪婪量词后面加上一个`+`就成了所有格量词。这些就像贪婪的量词，但是没有回溯。所以，像`/Error.*+valid/`这样的东西永远不会匹配，因为`.*+`会消耗所有剩余的字符。如果贪婪和所有格量词版本在功能上是等价的，那么所有格是首选的，因为对于不匹配的情况，它会更快地失败。

```
# functionally equivalent greedy and possessive versions
>> %w[abc ac adc abbc xabbbcz bbb bc abbbbbc].grep(/ab*c/)
=> ["abc", "ac", "abbc", "xabbbcz", "abbbbbc"]
>> %w[abc ac adc abbc xabbbcz bbb bc abbbbbc].grep(/ab*+c/)
=> ["abc", "ac", "abbc", "xabbbcz", "abbbbbc"]

# different results
# numbers >= 100 if there are leading zeros
# \d will be discussed in a later chapter, it matches all digit characters
>> '0501 035 154 12 26 98234'.gsub(/\b0*\d{3,}\b/, 'X')
=> "X X X 12 26 X"
>> '0501 035 154 12 26 98234'.gsub(/\b0*+\d{3,}\b/, 'X')
=> "X 035 X 12 26 X" 
```

Enter fullscreen mode Exit fullscreen mode

所有格量词的作用也可以用**原子分组**来表示。语法是`(?>pat)`，其中`pat`是正则表达式模式的一部分的缩写。在后面的章节中，你会看到更多这样的特殊分组。

```
# same as: /(b|o)++/
>> 'abbbc foooooot'.gsub(/(?>(b|o)+)/, 'X')
=> "aXc fXt"

# same as: /\b0*+\d{3,}\b/
>> '0501 035 154 12 26 98234'.gsub(/\b(?>0*)\d{3,}\b/, 'X')
=> "X 035 X 12 26 X" 
```

Enter fullscreen mode Exit fullscreen mode

## 习题

对于练习问题，请访问 GitHub 上本书资源库中的 [Exercises.md](https://github.com/learnbyexample/Ruby_Regexp/blob/master/exercises/Exercises.md) 文件。