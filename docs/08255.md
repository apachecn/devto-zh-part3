# Ruby Regexp 第 7 部分——分组和反向引用

> 原文：<https://dev.to/learnbyexample/ruby-regexp-part-7-groupings-and-backreferences-2kfp>

# 分组和反向引用

本章将展示如何通过正则表达式定义和替换部分中的反向引用来重用捕获组匹配的部分。您还将学习一些特殊的分组语法，用于普通的捕获组还不够的情况。

## 反向引用

反向引用就像编程语言中的变量。您已经看到了如何使用`MatchData`对象和 regexp 全局变量来引用捕获组匹配的文本。反向引用提供了相同的功能，其优点是可以在 regexp 定义以及替换部分中直接使用。另一个优点是您可以将量词应用于反向引用。

**替换部分**的语法是`\N`，其中`N`是您想要的捕获组。

*   使用`\1`、`\2`到`\9`来指代相应的捕获组
    *   如果在替换部分需要不止`9`个反向引用，使用带有全局变量的块形式
*   使用`\0`或`&amp;`获取整个匹配部分(与`$&`相同)
    *   类似地，`\``和`\'`分别对应于`$``和`$'`

```
# remove square brackets that surround digit characters
>> '[52] apples [and] [31] mangoes'.gsub(/\[(\d+)\]/, '\1')
=> "52 apples [and] 31 mangoes"

# replace __ with _ and delete _ if it is alone
>> '_foo_ __123__ _baz_'.gsub(/(_)?_/, '\1')
=> "foo _123_ baz"

# add something around the matched strings
>> '52 apples and 31 mangoes'.gsub(/\d+/, '(\0)')
=> "(52) apples and (31) mangoes"
>> 'Hello world'.sub(/.*/, 'Hi. \0\. Have a nice day')
=> "Hi. Hello world. Have a nice day"

# duplicate first field and add it as last field
>> 'fork,42,nice,3.14'.sub(/,.+/, '\0,\`')
=> "fork,42,nice,3.14,fork"

# swap words that are separated by a comma
>> 'good,bad 42,24'.gsub(/(\w+),(\w+)/, '\2,\1')
=> "bad,good 24,42" 
```

Enter fullscreen mode Exit fullscreen mode

> 如果在替换部分使用双引号，你将不得不使用`\\1`、`\\2`、`\\3`等等。

在 **regexp 定义**中，语法是`\N`或`\k<N>`来指代第* N *个捕获组。与替换段不同，`9`没有上限。下面是一些在 regexp 定义中使用反向引用的例子。

```
# elements that have at least one consecutive repeated character
>> %w[effort flee facade oddball rat tool].grep(/(\w)\1/)
=> ["effort", "flee", "oddball", "tool"]

# remove any number of consecutive duplicate words separated by space
# note the use of quantifier on backreferences
# use \W+ instead of space to cover cases like 'a;a<-;a'
>> 'aa a a a 42 f_1 f_1 f_13.14'.gsub(/\b(\w+)( \1)+\b/, '\1')
=> "aa a 42 f_1 f_13.14" 
```

Enter fullscreen mode Exit fullscreen mode

使用`\k<N>`或转义序列来避免正常数字字符和反向引用之间的歧义。

```
# \12 is treated as 12th backreference even though there's only one capture group
>> 'two one 5 one2 three'.match?(/([a-z]+).*\12/)
=> false
# there's no ambiguity here as \k<1> can only mean 1st backreference
>> 'two one 5 one2 three'.match?(/([a-z]+).*\k<1>2/)
=> true

>> s = 'abcdefghijklmna1d'
# there are 12 capture groups here
# requirement is \1 as backreference and 1 as normal digit
# using escapes is another way to avoid ambiguity
>> s.sub(/(.)(.)(.)(.)(.)(.)(.)(.)(.)(.)(.)(.).*\1\x31/, 'X')
=> "Xd" 
```

Enter fullscreen mode Exit fullscreen mode

> 关于`\k<N>`反向引用语法的另一种用法，参见[负反向引用](https://learnbyexample.github.io/Ruby_Regexp/groupings-and-backreferences.html#negative-backreferences)部分。

## 非捕获组

分组有很多用途，比如在 regexp 部分应用量词，通过分解公共部分创建简洁的 regexp 等等。它还会影响`scan`和`split`方法的输出，如[使用匹配部分](https://learnbyexample.github.io/Ruby_Regexp/working-with-matched-portions.html)一章所示。

当不需要反向引用时，可以使用非捕获组来避免`scan`和`split`方法的行为改变。这也有助于避免在不需要特定组进行反向引用时跟踪捕获组号。语法是`(?:pat)`来定义一个非捕获组。回想一下`(?>pat)`是用来定义原子组的。更多以`(?`语法开始的特殊组将在后面讨论。

```
# normal capture group will hinder ability to get whole match
# non-capturing group to the rescue
>> 'cost akin more east run against'.scan(/\b\w*(?:st|in)\b/)
=> ["cost", "akin", "east", "against"]

# capturing wasn't needed here, only common grouping and quantifier
>> '123hand42handy777handful500'.split(/hand(?:y|ful)?/)
=> ["123", "42", "777", "500"]

# with normal grouping, need to keep track of all the groups
>> '1,2,3,4,5,6,7'.sub(/\A(([^,]+,){3})([^,]+)/, '\1(\3)')
=> "1,2,3,(4),5,6,7"
# using non-capturing groups, only relevant groups have to be tracked
>> '1,2,3,4,5,6,7'.sub(/\A((?:[^,]+,){3})([^,]+)/, '\1(\2)')
=> "1,2,3,(4),5,6,7" 
```

Enter fullscreen mode Exit fullscreen mode

用量词引用捕获组匹配的文本将只给出最后一个匹配，而不是整个匹配。在分组和量词周围使用一个捕获组来获得整个匹配部分。在这种情况下，内部分组是使用非捕获组的理想候选。

```
>> s = 'hi 123123123 bye 456123456'
>> s.scan(/(123)+/)
=> [["123"], ["123"]]
>> s.scan(/(?:123)+/)
=> ["123123123", "123"]
# note that this issue doesn't affect substitutions
>> s.gsub(/(123)+/, 'X')
=> "hi X bye 456X456"

>> row = 'one,2,3.14,42,five'
# surround only fourth column with double quotes
# note the loss of columns in the first case
>> puts row.sub(/\A([^,]+,){3}([^,]+)/, '\1"\2"')
3.14,"42",five
>> puts row.sub(/\A((?:[^,]+,){3})([^,]+)/, '\1"\2"')
one,2,3.14,"42",five 
```

Enter fullscreen mode Exit fullscreen mode

但是，有些情况下捕获组是不可避免的。在这种情况下，可以用`gsub`代替`scan`。回想一下，`gsub`可以返回一个枚举器，这个枚举器可以被黑客攻击来模拟类似`scan`的行为。

```
# same as: scan(/\b\w*(?:st|in)\b/) but using capture group for gsub
>> 'cost akin more east run against'.gsub(/\b\w*(st|in)\b/).to_a
=> ["cost", "akin", "east", "against"]
# same as: scan(/\b\w*(?:st|in)\b/).map(&:upcase)
>> 'cost akin more east run against'.gsub(/\b\w*(st|in)\b/).map(&:upcase)
=> ["COST", "AKIN", "EAST", "AGAINST"]

# now for an example that is not possible with scan
# get whole words containing at least one consecutive repeated character
>> 'effort flee facade oddball rat tool'.gsub(/\b\w*(\w)\1\w*\b/).to_a
=> ["effort", "flee", "oddball", "tool"] 
```

Enter fullscreen mode Exit fullscreen mode

## 子表达式调用

这可能是显而易见的，但是应该注意，backreference 将提供匹配的字符串，而不是捕获组中的 regexp。例如，如果`(\d[a-f])`匹配`3b`，那么反向引用只会给出`3b`，而不会给出任何其他有效的正则表达式匹配，如`8f`、`0a`等。这类似于变量在编程中的行为，只有表达式的结果在变量赋值后保留，而不是表达式本身。

要引用正则表达式本身，请使用`\g<0>`、`\g<1>`、`\g<2>`等。这仅适用于 regexp 定义，不适用于替换部分。这种类似于函数调用的行为在正则表达式中被称为**子表达式调用**。递归将在下一节讨论。

```
>> row = 'today,2008-03-24,food,2012-08-12,nice,5632'

# same as: /\d{4}-\d{2}-\d{2}.*\d{4}-\d{2}-\d{2}/
>> row[/(\d{4}-\d{2}-\d{2}).*\g<1>/]
=> "2008-03-24,food,2012-08-12" 
```

Enter fullscreen mode Exit fullscreen mode

`\g`影响在`scan`和`split`方法和正常反向引用的上下文中，原始捕获组代表什么文本。您将仅获得最新匹配的文本。

```
>> d = '2008-03-24,2012-08-12 2017-06-27,2018-03-25 1999-12-23,2001-05-08'

# output has the value matched by \g<1> and not the original capture group
>> d.scan(/(\d{4}-\d{2}-\d{2}),\g<1>/)
=> [["2012-08-12"], ["2018-03-25"], ["2001-05-08"]]

# this will retain the second date of each pair
>> d.gsub(/(\d{4}-\d{2}-\d{2}),\g<1>/, '\1')
=> "2012-08-12 2018-03-25 2001-05-08"
# to retain the first date of each pair, use another capture group
# and adjust the backreference numbers
>> d.gsub(/((\d{4}-\d{2}-\d{2})),\g<2>/, '\1')
=> "2008-03-24 2017-06-27 1999-12-23" 
```

Enter fullscreen mode Exit fullscreen mode

## 递归匹配

`\g<N>`子表达式调用也支持递归。用于匹配嵌套模式，通常不建议用正则表达式来完成。事实上，如果您希望解析 html、xml、json、csv 等文件格式，请使用合适的解析器库。但是在某些情况下，解析器可能不可用，使用 regexp 可能比从头开始编写解析器更简单。

首先，匹配一组没有嵌套的括号(作为参考，称为**一级** regexp)。

```
# note the use of possessive quantifier
>> eqn0 = 'a + (b * c) - (d / e)'
>> eqn0.scan(/\([^()]++\)/)
=> ["(b * c)", "(d / e)"]

>> eqn1 = '((f+x)^y-42)*((3-g)^z+2)'
>> eqn1.scan(/\([^()]++\)/)
=> ["(f+x)", "(3-g)"] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，匹配一组括号，这组括号可以有选择地包含任意数量的非嵌套括号组(作为参考，称为 **level-two** regexp)。铁路图见 [debuggex](https://www.debuggex.com/r/47pQc0dSqXtWVlJ9) ，它直观地展示了这个正则表达式的递归性质。

```
>> eqn1 = '((f+x)^y-42)*((3-g)^z+2)'
# note the use of non-capturing group
>> eqn1.scan(/\((?:[^()]++|\([^()]++\))++\)/)
=> ["((f+x)^y-42)", "((3-g)^z+2)"]

>> eqn2 = 'a + (b) + ((c)) + (((d)))'
>> eqn2.scan(/\((?:[^()]++|\([^()]++\))++\)/)
=> ["(b)", "((c))", "((d))"] 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很神秘。为了清晰起见，你可以使用 [x 修饰符](https://learnbyexample.github.io/Ruby_Regexp/modifiers.html#x-modifier),以便于和递归版本进行比较。分解正则表达式，可以看到`(`和`)`必须完全匹配。其中，有效字符串由非括号字符或非嵌套括号序列组成(**一级** regexp)。

```
>> lvl2 = /\(               #literal (
             (?:            #start of non-capturing group
               [^()]++      #non-parentheses characters
               |            #OR
               \([^()]++\)  #level-one regexp
             )++            #end of non-capturing group, 1 or more times
           \)               #literal )
          /x

>> eqn1.scan(lvl2)
=> ["((f+x)^y-42)", "((3-g)^z+2)"]

>> eqn2.scan(lvl2)
=> ["(b)", "((c))", "((d))"] 
```

Enter fullscreen mode Exit fullscreen mode

要递归匹配任意数量的嵌套括号组，请使用捕获组并在捕获组内部调用它。因为这里需要调用整个 regexp，所以可以使用默认的 zeroth 捕获组(这也有助于避免使用`gsub+to_a`技巧)。与**二级** regexp 相比，唯一的变化是在第二次替换中使用了`\g<0>`而不是**一级** regexp。

```
>> lvln = /\(               #literal (
             (?:            #start of non-capturing group
               [^()]++      #non-parentheses characters
               |            #OR
               \g<0>        #recursive call
             )++            #end of non-capturing group, 1 or more times
           \)               #literal )
          /x

>> eqn0.scan(lvln)
=> ["(b * c)", "(d / e)"]

>> eqn1.scan(lvln)
=> ["((f+x)^y-42)", "((3-g)^z+2)"]

>> eqn2.scan(lvln)
=> ["(b)", "((c))", "(((d)))"]

>> eqn3 = '(3+a) * ((r-2)*(t+2)/6) + 42 * (a(b(c(d(e)))))'
>> eqn3.scan(lvln)
=> ["(3+a)", "((r-2)*(t+2)/6)", "(a(b(c(d(e)))))"] 
```

Enter fullscreen mode Exit fullscreen mode

## 命名捕获组

即使对于经验丰富的程序员来说，正则表达式也可能变得晦涩难懂，难以维护。有一些结构有助于增加清晰度。其中之一是命名捕获组，并使用该名称进行反向引用，而不是普通数字。语法是用`(?<name>pat)`或`(?'name'pat)`命名捕获组，用`\k<name>`进行反向引用。任何其他正常捕获组都将被视为非捕获组。

```
# giving names to first and second captured words
>> 'good,bad 42,24'.gsub(/(?<fw>\w+),(?<sw>\w+)/, '\k<sw>,\k<fw>')
=> "bad,good 24,42"

# alternate syntax
>> 'good,bad 42,24'.gsub(/(?'fw'\w+),(?'sw'\w+)/, '\k<sw>,\k<fw>')
=> "bad,good 24,42" 
```

Enter fullscreen mode Exit fullscreen mode

使用`\g<name>`语法，命名捕获组可用于子表达式调用。

```
>> row = 'today,2008-03-24,food,2012-08-12,nice,5632'

>> row[/(?<date>\d{4}-\d{2}-\d{2}).*\g<date>/]
=> "2008-03-24,food,2012-08-12" 
```

Enter fullscreen mode Exit fullscreen mode

回想一下，`/pat/ =~ string`和`string =~ /pat/`两种形式都可以。first 语法的一个优点是，命名捕获组也将创建具有这些名称的变量，并且可以用来代替依赖全局变量。

```
>> details = '2018-10-25,car'

>> /(?<date>[^,]+),(?<product>[^,]+)/ =~ details
=> 0

# same as: $1
>> date
=> "2018-10-25"
# same as: $2
>> product
=> "car" 
```

Enter fullscreen mode Exit fullscreen mode

您可以在`MatchData`对象上使用`named_captures`方法，将命名捕获组匹配的部分提取为一个`hash`对象。捕获组名称将是**键**，该组匹配的部分将是**值**。

```
# single match
>> details = '2018-10-25,car,2346'
>> details.match(/(?<date>[^,]+),(?<product>[^,]+)/).named_captures
=> {"date"=>"2018-10-25", "product"=>"car"}

# normal groups won't be part of the output
>> details.match(/(?<date>[^,]+),([^,]+)/).named_captures
=> {"date"=>"2018-10-25"}

# multiple matches
>> s = 'good,bad 42,24'
>> s.gsub(/(?<fw>\w+),(?<sw>\w+)/).map { $~.named_captures }
=> [{"fw"=>"good", "sw"=>"bad"}, {"fw"=>"42", "sw"=>"24"}] 
```

Enter fullscreen mode Exit fullscreen mode

## 反面引用

当有多个捕获组时，另一种方法是使用**负反向参考**。负编号从`-1`开始，指的是在使用负反向引用之前定义的最接近反向引用的捕获组。换句话说，负反向引用之前编号最高的捕获组将是`-1`，第二高的将是`-2`，依此类推。带有负的`N`的`\k<N>`语法变成了一个负的反向引用。这只能在 regexp 定义部分使用，因为替换部分的`\k`是为命名引用保留的。

```
# check if third and fourth columns have same data
# same as: match?(/\A([^,]+,){2}([^,]+),\2,/)
>> '1,2,3,3,5'.match?(/\A([^,]+,){2}([^,]+),\k<-1>,/)
=> true 
```

Enter fullscreen mode Exit fullscreen mode

## 条件组

这种特殊的分组允许您添加一个取决于捕获组是否匹配成功的条件。您还可以添加可选的 else 条件。根据 gmo 文档上的[的语法如下所示。](https://github.com/k-takata/Onigmo/blob/master/doc/RE)

> `(?(cond)yes-subexp|no-subexp)`

这里的`cond`是普通反向引用的`N`和命名反向引用的`<name>`或`'name'`。 **no-subexp** 是可选的。这里有一个单独使用 **yes-subexp** 的例子。任务是匹配只包含单词字符的元素，或者如果它另外以一个`<`字符开始，它必须以一个`>`字符结束。

```
>> words = %w[<hi> bye bad> <good> 42 <3]
>> words.grep(/\A(<)?\w+(?(1)>)\z/)
=> ["<hi>", "bye", "<good>", "42"]

# for this simple case, you can also expand it manually
# but for complex patterns, it is better to use conditional groups
# as it will avoid repeating the complex pattern
>> words.grep(/\A(?:<\w+>|\w+)\z/)
=> ["<hi>", "bye", "<good>", "42"]

# cannot simply use ? quantifier as they are independent, not constrained
>> words.grep(/\A(?:<?\w+>?)\z/)
=> ["<hi>", "bye", "bad>", "<good>", "42", "<3"] 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个关于 **no-subexp** 的例子。

```
# filter elements containing word characters surrounded by ()
# or, containing word characters separated by a hyphen
>> words = ['(hi)', 'good-bye', 'bad', '(42)', '-oh', 'i-j', '(-)']

# same as: /\A(?:\(\w+\)|\w+-\w+)\z/ or /\A(?:\((\w+)\)|\g<1>-\g<1>)\z/
>> words.grep(/\A(?:(\()?\w+(?(1)\)|-\w+))\z/)
=> ["(hi)", "good-bye", "(42)", "i-j"] 
```

Enter fullscreen mode Exit fullscreen mode

条件组有一个非常具体的用例，它通常对那些用例有帮助。主要优点是它防止了模式复制，尽管这也可以通过使用[子表达式调用](https://learnbyexample.github.io/Ruby_Regexp/groupings-and-backreferences.html#subexpression-calls)来实现。另一个优点是，如果公共模式使用捕获组，那么与重复交替方法不同，后向引用号将不需要调整。

## 习题

对于练习问题，请访问 GitHub 上本书资源库中的 [Exercises.md](https://github.com/learnbyexample/Ruby_Regexp/blob/master/exercises/Exercises.md) 文件。