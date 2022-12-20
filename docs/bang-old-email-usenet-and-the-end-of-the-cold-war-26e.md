# 砰！旧邮件、新闻组和冷战的结束

> 原文：<https://dev.to/flaque/bang-old-email-usenet-and-the-end-of-the-cold-war-26e>

在 1981 年，发送一封电子邮件可能需要*天。*

一个未经证实的维基百科来源是这么说的。当你想发送一封电子邮件时，你需要知道你发送的每一跳。

如果我想把我的邮件从斯坦福发到麻省理工学院，我可能需要指定它必须通过🚀美国宇航局艾姆斯，莫菲特场，加州大学伯克利分校，~~51 区~~和伊利诺伊大学。

我会用一个叫做 a 的特殊地址💥 **bangpath** 💥这将列出我们正在快速通过的步骤:

```
!ames!moffett!berkeley!illinois!theactualpersonimtryingtogettowtf 
```

Enter fullscreen mode Exit fullscreen mode

当邮件到达一个节点时，转发器将剥离一个 bang 段，并将其发送到下一个地址。

# 太烂了，让我们把社交网络的基础建立在它上面

bangpath 是 UUCP 内部的一个系统(Unix 到 Unix 的拷贝)。在 20 世纪 70 年代末，UUCP 主要用于发送邮件，但它的工具套件包括发送和接收在其他计算机上运行的命令的方法。

又过了 10 到 15 年，蒂姆·伯纳斯·李才开始研究 HTTP。

因此，当汤姆·特拉斯科特和吉姆·埃利斯于 1979 年开始设计后来成为新闻组的建筑时，他们的想法与 UUCP 紧密相连。甚至“新闻组”这个名字也与 Unix 用户组“Usenix”相似

在幕后，新闻组会把 bangpath 从用户手中拿走。取而代之的是，用户可以向他们所属的服务器发布信息，然后服务器之间可以共享这些信息。(编辑:注意，您仍然需要服务器的 bangpath 例如 hplaps！hpftc！econrad)

# 很难不对新闻组持不合理的乐观态度

像互联网的许多部分一样，新闻组是冷战中期西方世界的产物。对许多人来说，生活在铁幕后面的人们永远不会卷入任何早期的火焰战争是理所当然的。

人们理所当然地认为，世界大部分地区会以一场更加真实的火焰战争告终。

但是 1990 年，奇怪的事情发生了。来自莫斯科核能研究所的程序员创造了他们自己的操作系统，设法访问互联网，并悄悄地注册了新闻组*苏域名。 [src](https://www.opendemocracy.net/od-russia/natalia-konradova/usenet-coup)

一年后，柏林墙倒塌了。你仍然可以在 eunet.politics 上找到关于它的帖子 :

```
Unbelievable!
Incredible!
Historic! 
```

Enter fullscreen mode Exit fullscreen mode

# 其实很多历史事件都可以在新闻组上找到

1991 年，Linus Torvalds [在新闻组用一句话
介绍了 Linux](https://groups.google.com/forum/#!msg/comp.os.minix/4995SivOl9o/GwqLJlPSlCEJ)

```
Are you finding it frustrating when everything works on minix? No more all-nighters to get a nifty program working? 
```

Enter fullscreen mode Exit fullscreen mode

在`net.space`，[挑战者号爆炸被宣布](https://groups.google.com/forum/#!msg/net.space/3DzaFsYa-Lw/TceGl7gg-EUJ) :

```
At 8:39 AM PST today, the shuttle Challenger exploded at about a
minute into the flight.  NASA is searching for survivors now.  It appeared
that the orbiter and external tank exploded completely: television pictures
showed the SRBs moving away from a cloud of debris.  Thus it appears that
the first inflight disaster of the NASA space program has claimed the
lives of six astronauts and NASA's first passenger.

The disaster occured 17 years and 1 day after the Apollo I tragedy.

-- Rick. 
```

Enter fullscreen mode Exit fullscreen mode

在`mi.jobs`日，杰夫·贝索斯[为一家“资本雄厚的西雅图初创企业”寻找 Unix 开发人员](https://groups.google.com/forum/#!msg/mi.jobs/poXLCW8udK4/_GHzqB9sG9gJ)T3】

```
Well-capitalized start-up seeks extremely talented C/C++/Unix
developers to help pioneer commerce on the Internet.  You must have
experience designing and building large and complex (yet maintainable)
systems, and you should be able to do so in about one-third the time
that most competent people think possible.  You should have a BS, MS,
or PhD in Computer Science or the equivalent.  Top-notch communication
skills are essential.  Familiarity with web servers and HTML would be
helpful but is not necessary.
Expect talented, motivated, intense, and interesting co-workers.  Must
be willing to relocate to the Seattle area (we will help cover moving
costs).

Your compensation will include meaningful equity ownership.

Send resume and cover letter to Jeff Bezos:

mail:    be...@netcom.com
fax:     206/828-0951
US mail: Cadabra, Inc.
         10704 N.E. 28th St.
         Bellevue, WA  98004

We are an equal opportunity employer.

------------------------------------------------------------------------
"It's easier to invent the future than to predict it."  -- Alan Kay 
```

Enter fullscreen mode Exit fullscreen mode

In 1989, we get eye witness accounts of [the Tiananmen Square Massacre.](https://groups.google.com/forum/#!msg/soc.politics/xd12OolfyVM/fExftuxAOzcJ)

```
The situation is Beijing is MUCH WORSE that what is reported by the
media. A friend of mine just made a phone call to her brother at
Beijing Normal Univ. Her brother said that thousands have been killed,
many of them run over by tanks. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我不知道你们是怎么想的，但我开始觉得这个“互联网”的东西可能会是一个很大的进步。