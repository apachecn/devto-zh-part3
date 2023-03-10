# 穿越 Ruby 标准库的怪异而奇妙的旅程

> 原文：<https://dev.to/mctaylorpants/a-weird-and-wonderful-trip-through-rubys-standard-library-2ko1>

*[原本贴在介质上](https://medium.com/@mctaylorpants/a-weird-and-wonderful-trip-through-rubys-standard-library-762ddcf7a908)*

[![keep ruby's standard library weird](img/e48ca5393996bb069ca1872ff839c924.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zL-qZSRh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hokc6yihnholomf4jirr.png)

你现在可能已经听到这个消息了- [Bundler 将被并入 Ruby core](http://engineering.appfolio.com/appfolio-engineering/2018/11/26/bundler-is-built-into-ruby-260preview3) ！很高兴看到像 Bundler 这样对 Ruby 体验如此重要的项目，正在以一种深入的方式成为 Ruby 的一部分。

这也让我思考:里面还有什么？我使用 Ruby 主要是为了编写 web 应用程序，但是 Ruby 作为脚本语言的丰富历史意味着有很多功能我并不是每天都在使用，可能还有很多我甚至不知道存在的功能。

所以，我决定一探究竟。我花了一些时间浏览[标准库文档](https://ruby-doc.org/stdlib-2.5.3/)，留意我不认识的东西。我发现了一些非常奇怪和奇妙的东西，我想分享一些我最喜欢的。

准备好一些人为的例子了吗？我们走吧！

# Shellwords

首先:Shellwords 模块。它提供了一些不错的方法，使得在 Ruby 中构建和解析 shell 命令变得更加容易。

例如:假设您有一个带撇号的文件名，您想使用 cat 来获取文件的内容。(我确实说过这些是做作的，不是吗？😉)

你可以这样做:

```
$> filename = "Alex's Notes.txt"
$> `cat #{filename}` 
```

但是 bash 不喜欢未转义的单引号，所以最终会出现一个错误:

```
sh: -c: line 0: unexpected EOF while looking for matching `''
sh: -c: line 1: syntax error: unexpected end of file
=> "" 
```

不要害怕！#脱壳在这里！

```
$> `cat #{filename.shellescape}`
=> "Apostrophes in a filename? 🤔" 
```

万岁，你的问题解决了！尽管你可能想和那个把撇号放在第一位的人聊聊...

# 英文:当你想要更少的美元时用

突击测验:`$$`返回什么？

如果你的回答是“为什么，当然是当前进程 ID！”，那么我想你可以跳过这一节。

对于我们其他人来说，Ruby 的`$$`是对 Perl 的致敬，它返回当前系统进程的 ID。但是这不是对开发者最友好的名字，所以 Ruby 的英文模块提供了一些有用的别名:`$PROCESS_ID`和`$PID`。

这是一件非常小的事情，但我认为这是 Matz 最初的 Ruby 目标的完美例子，即创建一种首先被人类理解，然后被计算机理解的语言。

英语提供了一些别名。另一个有用的是`$CHILD_STATUS`，它将返回最后一个 shell 命令的退出代码:

```
$> `exit 42`
=> ""

$> $CHILD_STATUS   # or $? for the purists
=> #<Process::Status: pid 25566 exit 42> 
```

# 质数

如果你需要质数模块，Ruby 可以告诉你一个数是否是质数:

```
$> 5.prime?
=> true 
```

好吧，酷。

但是您知道吗，Ruby 有不止一个，不是两个，而是两个半*实现来确定一个数的素性？

首先是`TrialDivision`，这是一种蛮力方法，将有问题的数字除以其他更小的数字，直到得到确定的答案。

还有`EratosthenesSieve`，你可能从它的名字就能知道，它是 2000 多年前由一位希腊数学家发明的。真不敢相信

```
$> Prime::EratosthenesGenerator.new.take(10)
=> [2, 3, 5, 7, 11, 13, 17, 19, 23, 29]

$> Prime.take(10)   # uses Eratosthenes under the hood, by default
=> [2, 3, 5, 7, 11, 13, 17, 19, 23, 29] 
```

*最后，也可能是最少的——这就是为什么只有两个半——我们有`Generator23`。这是一个特殊的例子，因为它实际上并不产生质数，而是产生不能被 2 或 3 整除的数。这是数学家发明的一个聪明的优化方法，使验证素数更加节省内存。因此，`#prime?`使用这个生成器和一些额外的计算来检查素性。

# Abbrev

这可能是我在洞穴探险中发现的最怪异、最奇妙的模块。根据文献记载，`Abbrev`:

> 计算给定字符串集的唯一缩写集。

有趣的...让我们来看看它的作用:

```
$> require 'abbrev'
=> true

$> %w(ruby rules).abbrev
=> {
     "ruby"=>"ruby",
     "rub"=>"ruby",
     "rules"=>"rules",
     "rule"=>"rules",
     "rul"=>"rules"
   } 
```

给`Abbrev`一个字符串数组，它会给你一个散列，其中那些单词变成了值，每个键都是明确引用那个单词的一种方式。在上面的例子中，因为两个单词都以“ru”开头，所以如果我们想要指代其中一个，我们必须变得更具体。

可以说，这个模块的用例是有限的，但尽管如此，它仍然是优雅而精彩的。我很喜欢这种数据结构:利用散列的唯一键，让它指向原来的单词？👌👌

我能找到的`Abbrev`的唯一用法是在 [RDoc](https://docs.ruby-lang.org/en/2.1.0/RDoc/RI/Driver.html#method-i-expand_class) 和[Ruby core](https://github.com/ruby/ruby/blob/trunk/tool/redmine-backporter.rb#L578)的一个杂七杂八的脚本中，但是我想人们可以在需要命令行自动完成的事情中很好地使用它。

或者，您可以使用它来编写您自己的明确的昵称生成器！

```
$> names = %w(Alex Amy Ayla Amanda)
$> names.abbrev.keys.select { |n| n.length > 2 }
=> ["Alex", "Ale", "Amy", "Ayla", "Ayl", "Amanda", "Amand", "Aman", "Ama"] 
```

从现在开始，叫我 Ale。🍻

# 最后，但并非最不重要...

您可能已经在某个时候从 Ruby 程序发出了 HTTP 请求。你可能用过`Net::HTTP`(或者可能是另一个在引擎盖下使用它的宝石)。

但是让我问你一个问题——你曾经和 Ruby 一起检查过你的电子邮件吗？

🥁 🥁 🥁

介绍`Net::POP3`！

没错，你可以不用离开 IRB 就可以查看你的电子邮件:

```
$> inbox = Net::POP3.new('pop.gmail.com')
=> #<Net::POP3 pop.gmail.com: open=false>

$> inbox.start('your-email-here@gmail.com', 'supersecret')
=> #<Net::POP3 pop.gmail.com: open=true>

$> inbox.each_mail { |m| puts m.pop.split("\n").grep(/Subject/) }
Subject: Hello IRB!

$> pop.finish
=> "+OK Farewell." 
```

[![i ❤ ruby](img/1b58348a8e9e443e4a20e29885c5c0fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--14HBEKAJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1z981ve69x30b7wq3w47.png)

# 结论

多好的旅程啊！我希望我已经用你所知道和喜爱的语言打开了你的思维，让你有了一些新的可能性。我确实学到了很多，挖掘 Ruby 的这些怪异、布满灰尘的角落只会让我更加热爱它。❤️

有没有发现其他好玩的 Ruby 模块，或者能想到`Abbrev`的另一种用途？让我知道！