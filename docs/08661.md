# Ruby Regexp 第 5 部分-匹配部分

> 原文：<https://dev.to/learnbyexample/ruby-regexp-part-5-matched-portions-904>

# 处理匹配部分

已经看到了一些可以匹配不同文本的 regexp 特性，您将在本章中学习如何提取和处理那些匹配的部分。首先，您将了解`match`方法和产生的`MatchData`对象。然后您将了解`scan`方法以及捕获组如何影响`scan`和`split`方法。您还将学习如何使用与 regexp 相关的全局变量。

## 匹配方法

首先是类似于`match?`方法的`match`方法。这两种方法都接受一个 regexp 和一个可选的索引来指示起始位置。此外，这些方法将一个字符串参数视为一个正则表达式(其他字符串方法如`sub`、`split`等则不是这种情况)。`match`方法返回一个`MatchData`对象，从中可以提取各种细节，如字符串的匹配部分、匹配部分的位置等。如果给定的正则表达式没有匹配项，则返回`nil`。

```
# only the first matching portion is considered
>> 'abc ac adc abbbc'.match(/ab*c/)
=> #<MatchData "abc">
# string argument is treated the same as a regexp
>> 'abc ac adc abbbc'.match('a.*d')
=> #<MatchData "abc ac ad">

# second argument specifies starting location to search for a match
>> 'abc ac adc abbbc'.match(/ab*c/, 7)
=> #<MatchData "abbbc"> 
```

Enter fullscreen mode Exit fullscreen mode

`()`中的 regexp 分组也被称为**捕获组**。它有多种用途，其中之一是能够与这些组的匹配部分一起工作。当捕获组与`match`方法一起使用时，可以使用`MatchData`对象上的数组索引切片来检索它们。第一个元素始终是整个匹配部分，其余元素用于捕获组(如果存在的话)。最左边的`(`将得到组号`1`，最左边第二个`(`将得到组号`2`，以此类推。

```
# retrieving entire matched portion using [0] as index
>> 'abc ac adc abbbc'.match(/a.*d/)[0]
=> "abc ac ad"

# capture group example
>> m = 'abc ac adc abbbc'.match(/a(.*)d(.*a)/)
# entire matching portion and capture group portions
>> m.to_a
=> ["abc ac adc a", "bc ac a", "c a"]
# only the capture group portions
>> m.captures
=> ["bc ac a", "c a"]
# getting a specific capture group portion
>> m[1]
=> "bc ac a" 
```

Enter fullscreen mode Exit fullscreen mode

`offset`方法给出匹配部分的**开始**和**结束+ 1** 索引。它接受一个参数来指示整个匹配部分或特定的捕获组。您也可以使用`begin`和`end`方法来获取这些位置。

```
>> m = 'awesome'.match(/w(.*)me/)

>> m.offset(0)
=> [1, 7]
>> m.offset(1)
=> [2, 5]

>> m.begin(0)
=> 1
>> m.end(1)
=> 5 
```

Enter fullscreen mode Exit fullscreen mode

> 还有更多可用的方法。详见 [ruby-doc: MatchData](https://ruby-doc.org/core-2.7.1/MatchData.html) 。

```
>> m = 'THIS is goodbye then'.match(/hi.*bye/i)

>> m.regexp
=> /hi.*bye/i

>> m.string
=> "THIS is goodbye then" 
```

Enter fullscreen mode Exit fullscreen mode

`named_captures`方法将在[命名捕获组](https://learnbyexample.github.io/Ruby_Regexp/groupings-and-backreferences.html#named-capture-groups)章节中介绍。

## 用块匹配法

`match`方法也支持块形式，只有当 regexp 匹配成功时才执行。

```
>> 'abc ac adc abbbc'.match(/a(.*)d(.*a)/) { |m| puts m[2], m[1] }
c a
bc ac a

>> 'abc ac adc abbbc'.match(/xyz/) { 2 * 3 }
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 regexp 作为字符串索引

如果你是代码高尔夫的爱好者，你可以在 string 对象上的`[]`中使用 regexp 来复制`match`和`sub!`方法的一些特性。

```
# same as: match(/c.*d/)[0]
>> 'abc ac adc abbbc'[/c.*d/]
=> "c ac ad"

# same as: match(/a(.*)d(.*a)/)[1]
>> 'abc ac adc abbbc'[/a(.*)d(.*a)/, 1]
=> "bc ac a"

# same as: match(/ab*c/, 7)[0]
>> 'abc ac adc abbbc'[7..][/ab*c/]
=> "abbbc"

>> word = 'elephant'
# same as: word.sub!(/e.*h/, 'w')
>> word[/e.*h/] = 'w'
=> "w"
>> word
=> "want" 
```

Enter fullscreen mode Exit fullscreen mode

## 扫描方法

`scan`方法将所有匹配的部分作为一个数组返回。使用`match`方法，你只能得到第一个匹配的部分。

```
>> 'abc ac adc abbbc'.scan(/ab*c/)
=> ["abc", "ac", "abbbc"]

>> 'abc ac adc abbbc'.scan(/ab+c/)
=> ["abc", "abbbc"]

>> 'par spar apparent spare part'.scan(/\bs?pare?\b/)
=> ["par", "spar", "spare"] 
```

Enter fullscreen mode Exit fullscreen mode

对于调试目的来说，这也是一个有用的方法，例如，在应用替换方法之前，看看到底发生了什么。

```
>> 'that is quite a fabricated tale'.scan(/t.*a/)
=> ["that is quite a fabricated ta"]

>> 'that is quite a fabricated tale'.scan(/t.*?a/)
=> ["tha", "t is quite a", "ted ta"] 
```

Enter fullscreen mode Exit fullscreen mode

如果使用捕获组，输出的每个元素将是所有捕获组的字符串数组。由捕获组之外的 regexp 匹配的文本不会出现在输出数组中。此外，如果特定的捕获组不匹配任何字符，您将得到一个空字符串。如果需要在不影响`scan`输出的情况下使用分组，请参见[非捕获组](https://learnbyexample.github.io/Ruby_Regexp/groupings-and-backreferences.html#non-capturing-groups)部分。

```
# without capture groups
>> 'abc ac adc abbc xabbbcz bbb bc abbbbbc'.scan(/ab*c/)
=> ["abc", "ac", "abbc", "abbbc", "abbbbbc"]
# with single capture group
>> 'abc ac adc abbc xabbbcz bbb bc abbbbbc'.scan(/a(b*)c/)
=> [["b"], [""], ["bb"], ["bbb"], ["bbbbb"]]

# multiple capture groups
# note that last date didn't match because there's no comma at the end
# you'll later learn better ways to match such patterns
>> '2020/04/25,1986/Mar/02,77/12/31'.scan(%r{(.*?)/(.*?)/(.*?),})
=> [["2020", "04", "25"], ["1986", "Mar", "02"]] 
```

Enter fullscreen mode Exit fullscreen mode

使用块形式迭代匹配的部分。

```
>> 'abc ac adc abbbc'.scan(/ab+c/) { |m| puts m.upcase }
ABC
ABBBC

>> 'xx:yyy x: x:yy :y'.scan(/(x*):(y*)/) { |a, b| puts a.size + b.size }
5
1
3
1 
```

Enter fullscreen mode Exit fullscreen mode

## 用捕获组分割

捕获组也影响`split`方法。如果用于分割的 regexp 包含捕获组，则由这些组匹配的部分也将是输出数组的一部分。

```
# without capture group
>> '31111111111251111426'.split(/1*4?2/)
=> ["3", "5", "6"]

# to include the matching portions of the regexp as well in the output
>> '31111111111251111426'.split(/(1*4?2)/)
=> ["3", "11111111112", "5", "111142", "6"] 
```

Enter fullscreen mode Exit fullscreen mode

如果 regexp 的一部分在捕获组之外，那么匹配的文本就不会出现在输出中。如果一个捕获组没有参与，那么该元素将完全不出现在输出中。

```
# here 4?2 is outside capture group, so that portion won't be in output
>> '31111111111251111426'.split(/(1*)4?2/)
=> ["3", "1111111111", "5", "1111", "6"]

# multiple capture groups example
# note that the portion matched by b+ isn't present in the output
>> '3.14aabccc42'.split(/(a+)b+(c+)/)
=> ["3.14", "aa", "ccc", "42"]
# here (4)? matches zero times on the first occasion, thus absent
>> '31111111111251111426'.split(/(1*)(4)?2/)
=> ["3", "1111111111", "5", "1111", "4", "6"] 
```

Enter fullscreen mode Exit fullscreen mode

使用捕获组和可选限制 as 2 给出了类似于`partition`方法的行为。

```
# same as: partition(/a+b+c+/)
>> '3.14aabccc42abc88'.split(/(a+b+c+)/, 2)
=> ["3.14", "aabccc", "42abc88"] 
```

Enter fullscreen mode Exit fullscreen mode

## regexp 全局变量

包含 regexp 的表达式还会设置与 regexp 相关的全局变量，除了`match?`方法。假设`m`是下面描述的四个与 regexp 相关的全局变量中的一个`MatchData`对象。

*   `$~`包含`MatchData`对象，同`m`
*   `$``匹配部分前的字符串，同`m.pre_match`
*   `$&`匹配部分，同`m[0]`
*   `$'`匹配部分后的字符串，同`m.post_match`

这里有一个例子:

```
>> sentence = 'that is quite a fabricated tale'
>> sentence =~ /q.*b/
=> 8

>> $~
=> #<MatchData "quite a fab">
>> $~[0]
=> "quite a fab"
>> $`
=> "that is "
>> $&
=> "quite a fab"
>> $'
=> "ricated tale" 
```

Enter fullscreen mode Exit fullscreen mode

对于匹配多次的方法，比如`scan`和`gsub`，全局变量将在每次匹配时更新。在后面的说明中参考它们会给你最后一场比赛的信息。

```
# same as: { |m| puts m.upcase }
>> 'abc ac adc abbbc'.scan(/ab+c/) { puts $&.upcase }
ABC
ABBBC

# using 'gsub' for illustration purpose, can also use 'scan'
>> 'abc ac adc abbbc'.gsub(/ab+c/) { puts $~.begin(0) }
0
11

# using global variables afterwards will give info only for the final match
>> $~
=> #<MatchData "abbbc">
>> $`
=> "abc ac adc " 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你需要应用像`map`这样的方法和使用 regexp 全局变量，使用`gsub`而不是`scan`。

```
>> sentence = 'that is quite a fabricated tale'

# you'll only get information for last match with 'scan'
>> sentence.scan(/t.*?a/).map { $~.begin(0) }
=> [23, 23, 23]
# 'gsub' will get you information for each match
>> sentence.gsub(/t.*?a/).map { $~.begin(0) }
=> [0, 3, 23] 
```

Enter fullscreen mode Exit fullscreen mode

除了使用`$~`之外，还可以使用`$N`，其中 N 是你想要的捕获组。`$1`将有第一组匹配的字符串，`$2`将有第二组匹配的字符串，依此类推。作为特例，`$+`将有最后一组匹配的字符串。如果 regexp 中没有使用这个特定的捕获组，默认值是`nil`。

```
>> sentence = 'that is quite a fabricated tale'

>> sentence =~ /a.*(q.*(f.*b).*c)(.*a)/
=> 2

>> $&
=> "at is quite a fabricated ta"
# same as $~[1]
>> $1
=> "quite a fabric"
>> $2
=> "fab"
>> $+
=> "ated ta"
>> $4
=> nil

# $~ is handy if array slicing, negative index, etc are needed
>> $~[-2]
=> "fab"
>> $~.values_at(1, 3)
=> ["quite a fabric", "ated ta"] 
```

Enter fullscreen mode Exit fullscreen mode

## 使用哈希

在块形式和全局变量的帮助下，您可以使用散列变量根据匹配的文本来确定替换字符串。如果需求简单到将整个匹配部分传递给 hash 变量，那么`sub`和`gsub`方法都接受替换部分中的 hash 而不是 string。

```
# one to one mappings
>> h = { '1' => 'one', '2' => 'two', '4' => 'four' }

# same as: '9234012'.gsub(/1|2|4/) { h[$&] }
>> '9234012'.gsub(/1|2|4/, h)
=> "9two3four0onetwo"

# if the matched text doesn't exist as a key, default value will be used
>> h.default = 'X'
>> '9234012'.gsub(/./, h)
=> "XtwoXfourXonetwo" 
```

Enter fullscreen mode Exit fullscreen mode

为了在不使用中间结果的情况下交换两个或多个字符串，建议使用哈希对象。

```
>> swap = { 'cat' => 'tiger', 'tiger' => 'cat' }

>> 'cat tiger dog tiger cat'.gsub(/cat|tiger/, swap)
=> "tiger cat dog cat tiger" 
```

Enter fullscreen mode Exit fullscreen mode

对于有许多条目并且可能在开发过程中发生变化的散列，手动构建替换列表不是一个好的选择。此外，请记住，根据优先级规则，最长的字符串应该排在最前面。

```
>> h = { 'hand' => 1, 'handy' => 2, 'handful' => 3, 'a^b' => 4 }

>> pat = Regexp.union(h.keys.sort_by { |w| -w.length })
>> pat
=> /handful|handy|hand|a\^b/

>> 'handful hand pin handy (a^b)'.gsub(pat, h)
=> "3 1 pin 2 (4)" 
```

Enter fullscreen mode Exit fullscreen mode

## 条件表达式中的替换

如果替换失败，`sub!`和`gsub!`方法返回`nil`。这使得它们可以作为条件表达式的一部分，从而产生更有创造性和更简洁的解决方案。

```
>> num = '4'
>> puts "#{num} apples" if num.sub!(/5/) { $&.to_i ** 2 }
=> nil
>> puts "#{num} apples" if num.sub!(/4/) { $&.to_i ** 2 }
16 apples

>> word, cnt = ['coffining', 0]
>> cnt += 1 while word.sub!(/fin/, '')
=> nil
>> [word, cnt]
=> ["cog", 2] 
```

Enter fullscreen mode Exit fullscreen mode

## 习题

对于练习问题，请访问 GitHub 上本书资源库中的 [Exercises.md](https://github.com/learnbyexample/Ruby_Regexp/blob/master/exercises/Exercises.md) 文件。