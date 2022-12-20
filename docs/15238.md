# 再给一次机会:更加雄心勃勃的解析

> 原文：<https://dev.to/detunized/giving-go-another-chance-more-ambitious-parsing-5gej>

上次我能够添加一些简单的命令行开关，这很容易。现在我们来试试更难的。首先，我希望能够用用户指定的时间覆盖当前时间。我希望能够在特定时间记录任务。有时我非常渴望完成某件事，以至于我忘记了所有那些我正在努力养成的好习惯，并马上开始黑客行动。后来我意识到我忘了记录我正在做的一项任务。使用 [timetrap](https://github.com/samg/timetrap) 可以很容易地在 15 分钟前开始一项任务，而不是现在:

```
$ timetrap in --at '15 min ago' 'Implementing --at parsing' 
```

我希望能做同样的事情。我环顾四周，在的时候发现了一个名字[不可 Google 的库。实际上，围棋对谷歌来说也是艰难的。我想知道当人们寻找 Go 时，谷歌是否不得不黑掉他们的算法来推广他们的语言。](https://github.com/olebedev/when)

通过一些自述文件的拷贝和少量的谷歌搜索，我能够在 10 分钟内完成。一点也不差。

```
import  (  "github.com/olebedev/when"  "github.com/olebedev/when/rules/common"  "github.com/olebedev/when/rules/en"  )  func  parseAt(at  string)  time.Time  {  now  :=  time.Now()  if  len(at)  ==  0  {  return  now  }  w  :=  when.New(nil)  w.Add(en.All...)  w.Add(common.All...)  parsed,  err  :=  w.Parse(at,  now)  if  err  !=  nil  ||  parsed  ==  nil  {  fmt.Printf("Don't know how to parse '%s'\n",  at)  os.Exit(1)  }  return  parsed.Time  } 
```

接下来我想添加一些带有`--tag/-t`的标签。棘手的是，如果我愿意，我希望能够多次重复这个标志。

```
$ klk in -t coding -t tests "Writing some tests" 
```

事实证明，使用 Cobra 非常容易。我不需要为此写很多代码。不过，我通过大量的谷歌搜索进行了补偿。

```
// Declare a variable  var  tags  []string  // Add a flag  inCmd.Flags().StringSliceVarP(&tags,  "tag",  "t",  []string{},  "tags to use with this entry") 
```

我现在甚至可以做`-t 'tag1,tag2'`而不是`-t tag1 -t tag2`。保存击键很重要。没有人愿意在不必要的时候打字，我也不希望人们看到我的软件有多么需要打字就抛弃它。

这个东西现在真的成型了。它有自定义的时间戳和标签。已经比微软 Project 好太多了。

* * *

实现这一目标的谷歌搜索:

*   自然语言约会
*   去检查字符串是否为空
*   go cobra 重复标志*(导致 Github 上大量阅读)*
*   golang strings

* * *

耗时:20 分钟
总耗时:5:35 小时