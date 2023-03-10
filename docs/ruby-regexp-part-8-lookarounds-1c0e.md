# Ruby Regexp 第 8 部分-回看

> 原文：<https://dev.to/learnbyexample/ruby-regexp-part-8-lookarounds-1c0e>

# 四处张望

你已经看到了如何创建自定义角色类和各种特殊分组的化身。在这一章中，你将学习更多的分组，称为 lookarounds，它有助于创建自定义锚点和在 regexp 定义中添加条件。这些断言也被称为**零宽度模式**，因为它们添加了类似于锚点的限制，并且不是匹配部分的一部分。此外，您将学习如何否定类似于否定字符集的分组，以及`\G`锚的特殊之处。

## 条件表达式

在你习惯过多地环顾四周之前，最好记住 Ruby 是一种编程语言。您有控制结构，并且您可以使用逻辑操作符、像`all?`、`any?`等方法来组合多个条件。另外，不要忘记正则表达式只是字符串处理可用的工具之一。

```
>> items = ['1,2,3,4', 'a,b,c,d', '#foo 123']

# filter elements containing digit and '#' characters
>> items.filter { |s| s.match?(/\d/) && s.include?('#') }
=> ["#foo 123"]

# modify elements only if it doesn't start with '#'
>> items.filter_map { |s| s.sub(/,.+,/, ' ') if s[0] != '#' }
=> ["1 4", "a d"] 
```

Enter fullscreen mode Exit fullscreen mode

## 消极观望

可以通过两种方式添加环视断言— **后视**和**前视**。其中的每一个都可以是正**或负**断言。语法上，look back 比 lookahead 多了一个`<`。使用`!`可以识别消极的回看，而`=`用于积极的回看。这一节是关于否定回看的，其完整的语法如下所示。****

*   `(?!pat)`对于负前瞻断言
*   `(?<!pat)`对于否定的后视断言

如前所述，回看不是匹配部分的一部分，不会捕获匹配的文本。

```
# change 'foo' only if it is not followed by a digit character
# note that end of string satisfies the given assertion
# 'foofoo' has two matches as the assertion doesn't consume characters
>> 'hey food! foo42 foot5 foofoo'.gsub(/foo(?!\d)/, 'baz')
=> "hey bazd! foo42 bazt5 bazbaz"

# change 'foo' only if it is not preceded by _
# note how 'foo' at start of string is matched as well
>> 'foo _foo 42foofoo'.gsub(/(?<!_)foo/, 'baz')
=> "baz _foo 42bazbaz"

# overlap example
# the final _ was replaced as well as played a part in the assertion
>> 'food _fool 42foo_foot'.gsub(/(?<!_)foo./, 'baz')
=> "baz _fool 42bazfoot" 
```

Enter fullscreen mode Exit fullscreen mode

环视可以与现有的锚点和其他功能混合使用，以定义真正强大的限制。

```
# change whole word only if it is not preceded by : or --
>> ':cart apple --rest ;tea'.gsub(/(?<!:|--)\b\w+/, 'X')
=> ":cart X --rest ;X"

# extract whole words not surrounded by punctuation marks
>> 'tie. ink east;'.scan(/(?<![[:punct:]])\b\w+\b(?![[:punct:]])/)
=> ["ink"]

# add space to word boundaries, but not at start or end of string
# similar to: gsub(/\b/, ' ').strip
>> 'foo_baz=num1+35*42/num2'.gsub(/(?<!\A)\b(?!\z)/, ' ')
=> "foo_baz = num1 + 35 * 42 / num2" 
```

Enter fullscreen mode Exit fullscreen mode

在迄今为止的所有例子中，lookahead 分组作为后缀，look ahead 分组作为前缀。这是它们大多数时候的使用方式，但不是唯一的使用方式。环视可以放置在任何地方，并且多个环视可以以任何顺序组合。它们不消耗字符，也不在匹配部分中扮演角色。它们只是让您知道，从输入字符串的当前位置开始，您想要测试的条件是否得到满足。

```
# these two are equivalent
# replace a character as long as it is not preceded by 'p' or 'r'
>> 'spare'.gsub(/(?<![pr])./, '*')
=> "**a*e"
>> 'spare'.gsub(/.(?<![pr].)/, '*')
=> "**a*e"

# replace 'par' as long as 's' is not present later in the input
# this assumes that the lookaround doesn't conflict with search pattern
# i.e. 's' will not conflict 'par' but would affect if it was 'r' and 'par'
>> 'par spare part party'.gsub(/par(?!.*s)/, 'X')
=> "par sXe Xt Xty"
>> 'par spare part party'.gsub(/(?!.*s)par/, 'X')
=> "par sXe Xt Xty"

# since the three assertions used here are all zero-width,
# all of the 6 possible combinations will be equivalent
>> 'foo_baz=num1+35*42/num2'.gsub(/(?!\z)\b(?<!\A)/, ' ')
=> "foo_baz = num1 + 35 * 42 / num2" 
```

Enter fullscreen mode Exit fullscreen mode

## 正面回眸

与消极的环视不同，缺少某些东西不会满足积极的环视。相反，为了满足条件，模式必须匹配实际字符和/或零宽度断言。通过在分组中使用`=`可以识别积极环视。完整的语法如下所示:

*   `(?=pat)`对于正向前瞻断言
*   `(?<=pat)`对于肯定的回顾断言

```
# extract digits only if it is followed by ,
# note that end of string doesn't qualify as this is positive assertion
>> '42 foo-5, baz3; x83, y-20; f12'.scan(/\d+(?=,)/)
=> ["5", "83"]

# extract digits only if it is preceded by - and followed by ; or :
>> '42 foo-5, baz3; x83, y-20; f12'.scan(/(?<=-)\d+(?=[;:])/)
=> ["20"]

# replace 'par' as long as 'part' occurs as a whole word later in the line
>> 'par spare part party'.gsub(/par(?=.*\bpart\b)/, 'X')
=> "X sXe part party" 
```

Enter fullscreen mode Exit fullscreen mode

在基于字段的处理中，回看可能非常方便。

```
# except first and last fields
>> '1,two,3,four,5'.scan(/(?<=,)[^,]+(?=,)/)
=> ["two", "3", "four"]

# replace empty fields with nil
# note that in this case, order of lookbehind and lookahead doesn't matter
# can also use: gsub(/(?<![^,])(?![^,])/, 'nil')
>> ',1,,,two,3,,'.gsub(/(?<=\A|,)(?=,|\z)/, 'nil')
=> "nil,1,nil,nil,two,3,nil,nil"

# surround all fields (which can be empty too) with {}
# there is an extra empty string match at end of non-empty columns
>> ',cat,tiger'.gsub(/[^,]*/, '{\0}')
=> "{},{cat}{},{tiger}{}"
# lookarounds to the rescue
>> ',cat,tiger'.gsub(/(?<=\A|,)[^,]*/, '{\0}')
=> "{},{cat},{tiger}" 
```

Enter fullscreen mode Exit fullscreen mode

## 捕捉正面环视内的人群

即使循环不是匹配部分的一部分，捕获组也可以用在正循环中。你能解释为什么它对消极的周围环境不起作用吗？

```
# note also the use of double quoted string in replacement section
>> puts 'a b c d e'.gsub(/(\S+\s+)(?=(\S+)\s)/, "\\1\\2\n")
a b
b c
c d
d e

# and of course, use non-capturing group where needed
>> 'pore42 car3 pare7 care5'.scan(/(?<=(po|ca)re)\d+/)
=> [["po"], ["ca"]]
>> 'pore42 car3 pare7 care5'.scan(/(?<=(?:po|ca)re)\d+/)
=> ["42", "5"] 
```

Enter fullscreen mode Exit fullscreen mode

## 与有条件的回望

正如前面所承诺的，这里是 lookarounds 如何使构造和条件变得更简单。

```
>> words = %w[sequoia subtle questionable exhibit equation]

# words containing 'b' and 'e' and 't' in any order
# same as: /b.*e.*t|b.*t.*e|e.*b.*t|e.*t.*b|t.*b.*e|t.*e.*b/
>> words.grep(/(?=.*b)(?=.*e).*t/)
=> ["subtle", "questionable", "exhibit"]

# words containing all vowels in any order
>> words.grep(/(?=.*a)(?=.*e)(?=.*i)(?=.*o).*u/)
=> ["sequoia", "questionable", "equation"]

# words containing 'a' and 'q' but not 'n' at the end of the element
>> words.grep(/(?=.*a)(?=.*q)(?!.*n\z)/)
=> ["sequoia", "questionable"] 
```

Enter fullscreen mode Exit fullscreen mode

## 用\K 模拟正向后视

一些可变长度的正向后视情况可以通过使用`\K`作为后视断言所需的正则表达式的后缀来模拟。`\K`不是零宽度断言，因为匹配的字符被消耗。

```
# similar to: /(?<=\b\w)\w*\W*/
# text matched before \K won't be part of the matching portion
>> 'sea eat car rat eel tea'.gsub(/\b\w\K\w*\W*/, '')
=> "secret"

# replace only 3rd occurrence of 'cat'
>> 'cat scatter cater scat'.sub(/(cat.*?){2}\Kcat/, 'X')
=> "cat scatter Xer scat" 
```

Enter fullscreen mode Exit fullscreen mode

这里还有一个例子，如果用贪心量词代替所有格量词就不行了。

```
>> row = '421,foo,2425,42,5,foo,6,6,42'

# similar to: row.split(',').uniq.join(',')
# possessive quantifier used to ensure partial column is not captured
# if a column has same text as another column, the latter column is deleted
>> nil while row.gsub!(/(?<=\A|,)([^,]++).*\K,\1(?=,|\z)/, '')
=> nil
>> row
=> "421,foo,2425,42,5,6" 
```

Enter fullscreen mode Exit fullscreen mode

> 如果`\K`后要匹配的字符串可以为空，则不要将`\K`与`gsub`或`scan`一起使用。这就是 regexp 引擎的实现方式，其他库如 PCRE 没有这个限制。参见 [stackoverflow: \K in ruby](https://stackoverflow.com/questions/48375925/use-of-k-and-lookahead-not-working-as-expected) 了解更多关于这个主题的细节。

```
# [^,]*+ can match empty field, so use lookaround instead of \K
>> ',cat,tiger'.gsub(/(?<=\A|,)[^,]*+/, '{\0}')
=> "{},{cat},{tiger}"
>> ',cat,tiger'.gsub(/(?:\A|,)\K[^,]*+/, '{\0}')
=> "{},cat,{tiger}"

# another example with nothing to be matched after \K
>> 'abcd 123456'.gsub(/(?<=\w)/, ':')
=> "a:b:c:d: 1:2:3:4:5:6:"
>> 'abcd 123456'.gsub(/\w/, '\0:')
=> "a:b:c:d: 1:2:3:4:5:6:"
>> 'abcd 123456'.gsub(/\w\K/, ':')
=> "a:bc:d 1:23:45:6" 
```

Enter fullscreen mode Exit fullscreen mode

## 可变长度回望

用于后视断言的模式(正或负)不能*暗示*匹配可变长度的文本。允许使用固定长度量词或不同长度的交替(但每次交替都是固定长度)。由于某些原因，一个组内不同长度的交替是不允许的。这里有一些例子来阐明这些观点:

```
>> s = 'pore42 tar3 dare7 care5'

# allowed
>> s.scan(/(?<=(?:po|da)re)\d+/)
=> ["42", "7"]
>> s.scan(/(?<=\b[a-z]{4})\d+/)
=> ["42", "7", "5"]
>> s.scan(/(?<!tar|dare)\d+/)
=> ["42", "5"]

# not allowed
>> s.scan(/(?<=(?:o|ca)re)\d+/)
SyntaxError ((irb):4: invalid pattern in look-behind
>> s.scan(/(?<=\b[a-z]+)\d+/)
SyntaxError ((irb):5: invalid pattern in look-behind 
```

Enter fullscreen mode Exit fullscreen mode

根据使用情况，可能有各种解决方法。正如上一节所见，一些肯定的后视情况可以使用`\K`来解决，但是`\K`不是零宽度断言。在某些情况下，您可以完全跳过 look back，使用普通分组来解决问题。即使你不知道模式的长度，这也是可行的。

```
>> s = 'pore42 tar3 dare7 care5'

# examples where lookbehind won't give error
# same as: s.scan(/(?<=tar|dare)\d+/)
>> s.gsub(/(?:tar|dare)(\d+)/).map { $1 }
=> ["3", "7"]
# delete digits only if they are preceded by 'tar' or 'dare'
# same as: s.gsub(/(?<=tar|dare)\d+/, '')
>> s.gsub(/(tar|dare)\d+/, '\1')
=> "pore42 tar dare care5"

# examples where lookbehind will give error
# workaround for /(?<=\b[pd][a-z]*)\d+/
# get digits only if they are preceded by a word starting with 'p' or 'd'
>> s.gsub(/\b[pd][a-z]*(\d+)/).map { $1 }
=> ["42", "7"]
# delete digits only if they are preceded by a word starting with 'p' or 'd'
>> s.gsub(/(\b[pd][a-z]*)\d+/, '\1')
=> "pore tar3 dare care5" 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您不知道负后视的长度，就不能使用上面的解决方法。下一节将展示如何否定一个分组，这有助于一些可变的负回顾情况。

## 求反组和缺席运算符

可变长度负 look back 可以通过在分组中使用负 lookahead(对可变长度没有限制)并应用量词来逐个匹配字符来模拟。这也展示了在某些情况下分组是如何被否定的。请注意，只有在被求反的组之前定义好了条件，这才会起作用。

```
# match 'dog' only if it is not preceded by 'cat'
# note the use of \A anchor to force matching all characters up to 'dog'
# cannot use /(?<!cat.*)dog/ as variable length lookbehind is not allowed
>> 'fox,cat,dog,parrot'.match?(/\A((?!cat).)*dog/)
=> false
# match 'dog' only if it is not preceded by 'parrot'
>> 'fox,cat,dog,parrot'.match?(/\A((?!parrot).)*dog/)
=> true

# easier to understand by checking matched portion
>> 'fox,cat,dog,parrot'[/\A((?!cat).)*/]
=> "fox,"
>> 'fox,cat,dog,parrot'[/\A((?!parrot).)*/]
=> "fox,cat,dog,"
>> 'fox,cat,dog,parrot'[/\A(?:(?!(.)\1).)*/]
=> "fox,cat,dog,pa" 
```

Enter fullscreen mode Exit fullscreen mode

对于要求反的分组被另一个 regexp、anchor 等在两侧绑定的情况，有一种替代语法可以使用。它被称为**缺席运算符**，语法为`(?~pat)`。

```
# match if 'do' is not there between 'at' and 'par'
# note that quantifier is not used, absence operator takes care of it
# same as: /at((?!do).)*par/
>> 'fox,cat,dog,parrot'.match?(/at(?~do)par/)
=> false

# match if 'go' is not there between 'at' and 'par'
>> 'fox,cat,dog,parrot'.match?(/at(?~go)par/)
=> true
>> 'fox,cat,dog,parrot'[/at(?~go)par/]
=> "at,dog,par" 
```

Enter fullscreen mode Exit fullscreen mode

## \G 锚

像`\A`锚一样，`\G`锚限制从字符串开始匹配。此外，在匹配完成后，该匹配的结束被认为是新的锚位置。这个过程会重复进行，直到给定的正则表达式匹配失败(假设使用像`scan`和`gsub`这样的方法进行多次匹配)。

```
# all non-whitespace characters from start of string
>> '123-87-593 42 foo'.scan(/\G\S/)
=> ["1", "2", "3", "-", "8", "7", "-", "5", "9", "3"]
>> '123-87-593 42 foo'.gsub(/\G\S/, '*')
=> "********** 42 foo"

# all digits and optional hyphen combo from start of string
>> '123-87-593 42 foo'.scan(/\G\d+-?/)
=> ["123-", "87-", "593"]
>> '123-87-593 42 foo'.gsub(/\G(\d+)(-?)/, '(\1)\2')
=> "(123)-(87)-(593) 42 foo"

# all word characters from start of string
# only if it is followed by word character
>> 'cat12 bat pin'.gsub(/\G\w(?=\w)/, '\0:')
=> "c:a:t:1:2 bat pin"

# all lowercase alphabets or space from start of string
>> 'par tar-den hen-food mood'.gsub(/\G[a-z ]/, '(\0)')
=> "(p)(a)(r)( )(t)(a)(r)-den hen-food mood" 
```

Enter fullscreen mode Exit fullscreen mode

## 习题

对于练习问题，请访问 GitHub 上本书资源库中的 [Exercises.md](https://github.com/learnbyexample/Ruby_Regexp/blob/master/exercises/Exercises.md) 文件。