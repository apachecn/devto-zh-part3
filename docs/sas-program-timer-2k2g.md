# SAS 程序定时器

> 原文：<https://dev.to/benjcorc/sas-program-timer-2k2g>

[![](img/51285f231d2498463a448999585340a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9RvNwC39--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AErzZrvFargjm132P8ptRFQ.jpeg)

SAS 很棒。SAS 很好。呃，70%的时间 SAS 是好的。SAS 的问题，或者更确切地说是其中一个问题，是它持续的、几乎顽固的拒绝只见树木不见森林的选择。让我们通过构建一个程序计时器来稍微改进一下。

从 [GitHub](https://gist.github.com/benjamincorcoran/17a97dd5485aa9997c43b8dc3c25d330) 中抓取这个

### 我们目前正在处理的是…

不要说 SAS 不努力。事实上，如果他们不在某个地方记录 SAS 对象的运行时间，这将是对常识的一个相当极端的背离。检查任何 SAS 日志都会显示产生以下输出的数据步骤和过程。

```
10 proc sort data=logsample; **[12]**
11 by LastName;
12

NOTE: There were 5 observations read from the dataset
 WORK.LOGSAMPLE. 
NOTE: The data set WORK.LOGSAMPLE has 5 observations and 10
 variables. **[13]**
NOTE: PROCEDURE SORT used:
 real time 0.16 seconds
 cpu time 0.03 seconds 
```

这里我们可以看到过程 SORT 使用了 0.16 秒的实时时间，这相当于 0.03 秒的 CPU 时间。对于我们的目的来说，这里的区别并不重要，但是这里讨论的是对这些笔记的更深入的研究。

知道这些非常好，但是在更常见的情况下，您正在处理 100 多个不同的过程、数据步骤，天知道还有什么，获得整个程序的总时间，而不仅仅是单个步骤，将是有用的。

### 一个 SAS 程序定时器

首先，让我们抓住程序开始的时间。没有你想象的那么简单。SAS 不随执行时间存储宏变量。SAS 确实有自动生成的宏变量，如[系统时间](https://support.sas.com/documentation/cdl/en/mcrolref/61885/HTML/default/viewer.htm#a000543691.htm)。然而，这些指的是**会话**开始的时间，而不是我们明确启动程序的时间。

因此，我们需要创建自己的宏变量。让我们称之为 launchTime，以避免与其他可能出现的宏变量混淆。

### 发射和着陆

```
%let launchTime = %sysfunc(time()); 
```

如果我们将上面的代码片段放在程序的开始，我们将会捕获我们的开始时间。接下来，我们来捕捉结束时间。幸运的是，这只是完全相同的片段！我们只需要重命名宏变量。

```
%let landTime = %sysfunc(time());

%let timeTaken = %sysevalf(&landTime.-&launchTime.); 
```

我们有两个时间，整个程序的总运行时间，仅仅是差。搞定了。我们已经成功地为我们的节目计时了。我们需要做的就是%把我们的 timeTaken 宏变量。唉，在目前的形式下，我们的 timeTaken 变量只是所用的秒数。如果能把这个格式化成小时，分钟，秒就好了。

### 格式化，格式化，格式化

为了做到这一点，我们需要将它从时间增量(两个时间之间的差异)转换成 SAS 的 DateTime 格式可以理解的东西。因此，我们可以为所有 SAS 日期时间对象添加参考点 1960 年 1 月 1 日。

```
%let timeTaken = %sysevalf(mdy(1,1,1960)+&landTime.-&launchTime.);

%let timeTakenFmt = %sysfunc(putn(&timeTaken.,e8601tm15.6));
%put &timeTakenFmt.; 
```

然后我们需要做的就是添加我们想要的格式。我选择了 e8601tm15.6，它将时间精确到毫秒，但可以随意使用 SAS 的任何一种内置格式。

这很好，但是如果我们必须从现在开始将这些行复制到每个值得使用的脚本中，这就有点麻烦了。

### 企业指南也不全是坏事。

企业救援指南！在 EG 中，我们能够设置在每个程序执行的开始和结束时执行的脚本。

[![](img/56d88addee13c71e7a8b76ce7ec9f9d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EGZGlFCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/695/1%2A0kggqpaf6WiQrTlSieLodA.png)

在选项> SAS 程序>附加 SAS 代码中，有两个复选框分别对应于*‘在提交的代码前插入自定义 SAS 代码’*和*‘在提交的代码后插入自定义 SAS 代码’*。将我们的 launchTime 宏变量定义放在部分之前的*中，将我们的 landTime 宏变量定义和%放在*部分之后的*中，从现在起每个程序都将被计时！*

### 额外学分

在日志中突出显示这些信息，添加一个 ASCII 框，将它从左侧移出，添加一个标题和上下文。

```
%let tab = %str( );

%put ;
%put &tab.############################### ;
%put &tab.# SAS CODE SUMMARY #;
%put &tab.# TIME TAKEN: &timeTakenFmt. #;
%PUT &TAB.# #; 
%PUT &TAB.###############################; 
```

最后，您的日志应该看起来像这样…

[![](img/f50f854fa706d5559617304d68609b73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jIFDHNMP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/531/1%2AvraDk7hbkPciwQTj-ZyAcQ.png)

从 [GitHub](https://gist.github.com/benjamincorcoran/17a97dd5485aa9997c43b8dc3c25d330)

网站抓取这个:【https://www.benjamincorcoran.com】T4