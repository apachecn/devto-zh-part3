# Ruby Regexp 第 6 部分-字符类

> 原文：<https://dev.to/learnbyexample/ruby-regexp-part-6-character-class-24ig>

# 人物类

本章将讨论如何创建自己的自定义占位符来匹配有限的字符集和字符类中适用的各种元字符。您还将学习各种预定义的字符集。

## 自定义字符集

包含在`[]`元字符中的字符是一个字符类(或集合)。它将导致匹配任何一个字符一次。它类似于在分组中使用单个字符替换，但是没有捕获组的缺点。此外，字符类有自己的元字符版本，并为常见用例提供特殊的预定义集合。量词也适用于字符类。

```
# same as: /cot|cut/ or /c(o|u)t/
>> %w[cute cat cot coat cost scuttle].grep(/c[ou]t/)
=> ["cute", "cot", "scuttle"]

# same as: /(a|e|o)+t/
>> 'meeting cute boat site foot'.gsub(/[aeo]+t/, 'X')
=> "mXing cute bX site fX" 
```

Enter fullscreen mode Exit fullscreen mode

## 字符范围

字符类有自己的元字符来帮助简洁地定义集合。字符类之外的元字符，如`^`、`$`、`()`等，要么没有特殊含义，要么在字符类内部有完全不同的含义。首先是`-`元字符，它有助于定义一系列字符，而不是必须单独指定它们。

```
# all digits, same as: scan(/[0123456789]+/)
>> 'Sample123string42with777numbers'.scan(/[0-9]+/)
=> ["123", "42", "777"]

# whole words made up of lowercase alphabets only
>> 'coat Bin food tar12 best'.scan(/\b[a-z]+\b/)
=> ["coat", "food", "best"]

# whole words made up of lowercase alphabets and digits only
>> 'coat Bin food tar12 best'.scan(/\b[a-z0-9]+\b/)
=> ["coat", "food", "tar12", "best"]

# whole words made up of lowercase alphabets, but starting with 'p' to 'z'
>> 'coat tin food put stoop best'.scan(/\b[p-z][a-z]*\b/)
=> ["tin", "put", "stoop"]

# whole words made up of only 'a' to 'f' and 'p' to 't' lowercase alphabets
>> 'coat tin food put stoop best'.scan(/\b[a-fp-t]+\b/)
=> ["best"] 
```

Enter fullscreen mode Exit fullscreen mode

## 否定字符集

下一个元字符是`^`，它必须被指定为字符类的第一个字符。它对字符集求反，因此将匹配除指定字符之外的所有字符。正如前面所强调的，小心处理负逻辑，你可能会得到比你想要的更多的匹配。此外，下面这些例子都是使用所有格量词的好地方，因为不涉及回溯。

```
# all non-digits
>> 'Sample123string42with777numbers'.scan(/[^0-9]+/)
=> ["Sample", "string", "with", "numbers"]

# remove first two columns where : is delimiter
>> 'foo:123:bar:baz'.sub(/\A([^:]+:){2}/, '')
=> "bar:baz"

# deleting characters at end of string based on a delimiter
>> 'foo=42; baz=123'.sub(/=[^=]+\z/, '')
=> "foo=42; baz"

>> dates = '2020/04/25,1986/Mar/02,77/12/31'
# Note that the third character set negates comma as well
# and comma is matched optionally outside the capture groups
>> dates.scan(%r{([^/]+)/([^/]+)/([^/,]+),?})
=> [["2020", "04", "25"], ["1986", "Mar", "02"], ["77", "12", "31"]] 
```

Enter fullscreen mode Exit fullscreen mode

有时，使用正的字符类并反转布尔结果比否定字符类更容易。

```
>> words = %w[tryst fun glyph pity why]

# words not containing vowel characters
>> words.grep(/\A[^aeiou]+\z/)
=> ["tryst", "glyph", "why"]

# easier to write and maintain
# but this'll match empty strings too unlike the previous solution
>> words.grep_v(/[aeiou]/)
=> ["tryst", "glyph", "why"] 
```

Enter fullscreen mode Exit fullscreen mode

## 设定交点

在两组字符之间使用`&&`将导致只匹配这两组字符的交集。为了有助于这样的定义，您可以以嵌套的方式使用`[]`。

```
# [^aeiou] will match any non-vowel character
# which means space is also a valid character to be matched
>> 'tryst glyph pity why'.scan(/\b[^aeiou]+\b/)
=> ["tryst glyph ", " why"]

# [a-z&&[^aeiou]] will be intersection of a-z and non-vowel characters
# this results in positive definition of characters to match
>> 'tryst glyph pity why'.scan(/\b[a-z&&[^aeiou]]+\b/)
=> ["tryst", "glyph", "why"] 
```

Enter fullscreen mode Exit fullscreen mode

## 逐字匹配元字符

与其他元字符类似，在字符类元字符前面加上前缀`\`,以便逐字匹配它们。其中一些也可以通过不同的放置来实现。

`-`应该是第一个或最后一个字符，或者使用`\`进行转义。

```
>> 'ab-cd gh-c 12-423'.scan(/\b[a-z-]{2,}\b/)
=> ["ab-cd", "gh-c"]
>> 'ab-cd gh-c 12-423'.scan(/\b[a-z\-0-9]{2,}\b/)
=> ["ab-cd", "gh-c", "12-423"] 
```

Enter fullscreen mode Exit fullscreen mode

`^`应该不是第一个字符，或者使用`\`进行转义。

```
>> 'f*(a^b) - 3*(a+b)'.scan(/a[+^]b/)
=> ["a^b", "a+b"]
>> 'f*(a^b) - 3*(a+b)'.scan(/a[\^+]b/)
=> ["a^b", "a+b"] 
```

Enter fullscreen mode Exit fullscreen mode

`[`、`]`、`\`要用`\`转义。

```
>> 'words[5] = tea'[/[a-z\[\]0-9]+/]
=> "words[5]"

>> puts '5ba\babc2'[/[a\\b]+/]
ba\bab 
```

Enter fullscreen mode Exit fullscreen mode

## 转义序列字符集

常用的字符集有预定义的转义序列:

*   `\w`相当于匹配单词字符的`[A-Za-z0-9_]`(回想一下单词边界的定义)
*   `\d`相当于匹配数字字符的`[0-9]`
*   `\s`相当于匹配空白字符的`[ \t\r\n\f\v]`
*   `\h`相当于`[0-9a-fA-F]`匹配十六进制字符

这些转义序列可以作为独立的模式使用，也可以在字符类中使用。

```
>> '128A foo1 fe32 34 bar'.scan(/\b\h+\b/)
=> ["128A", "fe32", "34"]
>> '128A foo1 fe32 34 bar'.scan(/\b\h+\b/).map(&:hex)
=> [4746, 65074, 52]

>> 'Sample123string42with777numbers'.split(/\d+/)
=> ["Sample", "string", "with", "numbers"]
>> 'foo=5, bar=3; x=83, y=120'.scan(/\d+/).map(&:to_i)
=> [5, 3, 83, 120]

>> 'sea eat car rat eel tea'.scan(/\b\w/).join
=> "secret"

>> 'tea sea-pit sit-lean bean'.scan(/[\w\s]+/)
=> ["tea sea", "pit sit", "lean bean"] 
```

Enter fullscreen mode Exit fullscreen mode

负面逻辑再次出现。分别使用`\W`、`\D`、`\S`和`\H`作为它们的否定集合。

```
>> 'Sample123string42with777numbers'.gsub(/\D+/, '-')
=> "-123-42-777-"

>> 'foo=5, bar=3; x=83, y=120'.gsub(/\W+/, '')
=> "foo5bar3x83y120"

>> "   1..3 \v\f foo_baz 42\tzzz \r\n1-2-3  ".scan(/\S+/)
=> ["1..3", "foo_baz", "42", "zzz", "1-2-3"] 
```

Enter fullscreen mode Exit fullscreen mode

`\R`匹配换行符`\n`、`\v`、`\f`、`\r`、`\u0085`(下一行)、`\u2028`(行分隔符)、`\u2029`(段落分隔符)或`\r\n`。与其他转义不同，`\R`不能在字符类中使用。

```
>> "food\r\ngood"[/d\Rg/]
=> "d\r\ng" 
```

Enter fullscreen mode Exit fullscreen mode

## 命名字符集

Ruby 还提供了命名字符集，与仅限于 ASCII 字符的转义序列集不同，命名字符集支持 Unicode。命名字符集由包含在`[:`和`:]`之间的名称定义，并且必须在一个字符类`[]`中使用，以及任何其他需要的字符。使用`[:^`而不是`[:`将否定命名集。

上面给出的四个转义序列都有命名的等价集合。完整列表和详细信息见 [ruby-doc:字符类](https://ruby-doc.org/core-2.7.1/Regexp.html#class-Regexp-label-Character+Classes)。

```
# similar to: /\d+/ or /[0-9]+/
>> 'Sample123string42with777numbers'.split(/[[:digit:]]+/)
=> ["Sample", "string", "with", "numbers"]

# similar to: /\S+/
>> "   1..3 \v\f foo_baz 42\tzzz \r\n1-2-3  ".scan(/[[:^space:]]+/)
=> ["1..3", "foo_baz", "42", "zzz", "1-2-3"]

# similar to: /[\w\s]+/
>> 'tea sea-pit sit-lean bean'.scan(/[[:word:][:space:]]+/)
=> ["tea sea", "pit sit", "lean bean"] 
```

Enter fullscreen mode Exit fullscreen mode

下面是一些没有转义序列版本的命名字符集:

```
# similar to: /[a-zA-Z]+/
>> 'Sample123string42with777numbers'.scan(/[[:alpha:]]+/)
=> ["Sample", "string", "with", "numbers"]

# remove all punctuation characters
>> ip = '"Hi", there! How *are* you? All fine here.'
>> ip.gsub(/[[:punct:]]+/, '')
=> "Hi there How are you All fine here"
# remove all punctuation characters except . ! and ?
>> ip.gsub(/[[^.!?]&&[:punct:]]+/, '')
=> "Hi there! How are you? All fine here." 
```

Enter fullscreen mode Exit fullscreen mode

## 数值范围

字符类也可以用来构造数值范围。

```
# numbers between 10 to 29
>> '23 154 12 26 98234'.scan(/\b[12]\d\b/)
=> ["23", "12", "26"]

# numbers >= 100
>> '23 154 12 26 98234'.scan(/\b\d{3,}\b/)
=> ["154", "98234"]

# numbers >= 100 if there are leading zeros
>> '0501 035 154 12 26 98234'.scan(/\b0*+\d{3,}\b/)
=> ["0501", "154", "98234"] 
```

Enter fullscreen mode Exit fullscreen mode

然而，它很容易错过的角落情况和一些范围是复杂的设计。在这种情况下，最好先将匹配部分转换成适当的数字格式。

```
# numbers < 350
>> '45 349 651 593 4 204'.scan(/\d++/).filter { |n| n.to_i < 350 }
=> ["45", "349", "4", "204"]

# numbers between 200 and 650
>> '45 349 651 593 4 204'.gsub(/\d++/) { (200..650) === $&.to_i ? 0 : 1 }
=> "1 0 1 0 1 0" 
```

Enter fullscreen mode Exit fullscreen mode

## 习题

对于练习问题，请访问 GitHub 上本书资源库中的 [Exercises.md](https://github.com/learnbyexample/Ruby_Regexp/blob/master/exercises/Exercises.md) 文件。