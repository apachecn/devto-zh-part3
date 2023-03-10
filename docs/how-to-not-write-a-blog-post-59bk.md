# 如何不写博文

> 原文：<https://dev.to/theinfradev/how-to-not-write-a-blog-post-59bk>

# **写博客的想法**

我的手机嗡嗡作响，这是一个每两周一次的星期五闹钟。

> "你有 48 小时写一篇博客文章."

这本来是一个激励性的提醒，但收到的更像是绑匪的赎金要求。一个强有力的提醒，是时候把两周的随机想法压缩到页面上了，准备让我美丽的妻子审阅(确保我没有犯任何可怕的错误，比如用 there 代替 their，或者试图弄清楚它是 accept 还是 except)。准备好向全世界发表，准备好接受我自己狂妄自大的耀眼光芒，最迟在下周三。

我打开我的标准博客模板，markdown 已经准备好了。

...光标凄凉地闪烁着。如果我真的看到了，那就是行动的召唤。

"这是你自找的"，我小声嘀咕道。

# 我有一个标题想法，但是有很多愚蠢的问题阻碍着我...

### 我以为这是一个关于写垃圾剧本的博客？我认为你的情况很糟糕

事情是这样的。

对于我的下一个博客，我有一个宏伟的想法，如何使用 PowerShell 中的类来帮助构建一个伟大的脚本。但是我不喜欢极长的博文。我想保持在 600-1000 字左右。考虑到这一点，你如何将封装、组合和多态的伟大之处浓缩到那个规模——对于可能知道也可能不知道这些东西是什么的观众来说？怎么不从头开始呢？我不想从头开始，比我更好的开发人员已经这样做了，我应该只参考他们尊敬的工作，而不是被它绊倒。

但同时我确实有一些关于类的重要事情要说，关于 POPO 的(普通的老式 PowerShell 对象。明白了吗？好吧，如果你不是一个开发者-可能不是。哈哈的笑...咳咳。).还有关于使用类来隐藏你的方法实现的东西，以及最后你如何使用那些构建块来制作真正酷的函数，每次都在 95%的时间里工作。妈的，你怎么解释接口...？

我只有 5 篇博文，前几篇是关于任务的，也就是我为什么要这么做。而这东西，这是好东西。这是人们真正想学的，也是我必须付出的。然而，第六篇博文可以作为博客的元描述吗？大家都有这种感觉吗？所有那些怀着良好意愿开始写一点他们所知道和喜爱的东西的人，是否偶然发现了这条路，在这条路上，可能性是无限的，听起来像胡言乱语的可能性是很高的...真的，请原谅我，我是好意...哦亲爱的。

# **让我们后退一步**

### 为此，你不需要先向前迈一步吗？

光标凄凉地闪烁着。我抬起头，我有一个想法。我会把它做成一个三部分的系列。对，就是这样。第一部分是关于创建简单的类来传递数据，然后是方法，最后我将把它们都包装在一个漂亮的函数中。太棒了，拍拍背。现在，我用什么来举例呢？...啊坚果...光标凄凉地闪烁着。它是怎么做到的？光标怎么会被遗弃？

混蛋。

# 让我给你留下一些有用的东西！

### 在这场混乱之后，这真的是你能做的最起码的事情了

因为假期已经基本结束了，而且(你可能已经猜到了)我已经推迟了第一个！重要！三！部分！系列！直到一月，让我给你一份礼物。一些我现在可以用心写的东西。如何在 PowerShell 中编写一个简单而有效的日志功能？我保证，在你有趣的小脚本开始时实现它总是一个好主意。

首先在脚本的开头声明一个环境变量，日志位置如下:

> $env:Logfile = "$AppPath\logs\$(get-date -format 'dd-MM-yyyy') - Application log"
> 
> #这里的 get-date 临时变量将确保将日期添加到文件名中，这样您就不会以一个巨大的 100MB 日志结束。

然后我们将编写函数本身。

> Function Write-Log
> 
> {
> 
> $ DateTime = Get-Date-format ' DD/MM/yyyy HH:MM:ss '；if($ Color-not match " \ w "){ $ Color = ' Gray ' }
> 
> $log = "$env:Computername : $DateTime : $LogEntry"
> 
> out-File-input object＄log-File path＄log path-Append-no clobber
> 
> write-host $ Log-foreground Color $ Color
> 
> }

So what have we created? Well if we run the function like so:

> Write-Log -LogEntry“我写了一个不错的日志文件”-Log path $ env:Log file-color ' blue '

We will likely now have a new log at the desired location and a nice message on the console in blue like this:

> computer 01:19/12/2018 06:48:02:我写了一个不错的日志文件

I hope this helps, and if you use it - that you enjoy having nicely formatted logs! Have a Merry Christmas and/or Happy Holidays.
See you next year for my amazing three-part series on PowerShell Classes and Functions and why they are so awesome together...

InfraDev

*要获得我的新文章的更新，请在 Twitter 上关注我[@ theinfradev](https://twitter.com/theinfradev)T3】*