# 再给一次机会:hashtag 解析

> 原文：<https://dev.to/detunized/giving-go-another-chance-3814>

上次我加了`--tag/-t`旗。这次我想添加 hashtag 解析。我想在评论中简单地提到标签，然后用工具把它们找出来。例如:

```
$ klk in 'Writing some #tests to find a #bug' 
```

我的第一个冲动是去找图书馆。而有[一个](https://github.com/gernest/mention)正是为了这个目的。但是谁想在自己的代码库中引入一个[左键盘定时炸弹](https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/)？*(回答:几乎任何人)。*我宁愿重新发明轮子，[用正则表达式](https://blog.codinghorror.com/regular-expressions-now-you-have-two-problems/)解决问题。两个问题总比一个好。

Go 的标准库中有`regexp`包就是为了这个目的。所以这实际上是两句台词:

```
func  extractTags(text  string)  []string  {  re  :=  regexp.MustCompile("#\\S+")  // TODO: Strip out #  return  re.FindAllString(text,  -1)  } 
```

注意到待办事项了吗？在这种情况下，你必须从 Python 的高度跳入 C 语言的深度，并运行自己的循环。两行变成了六行。这就是我开始想念 Ruby，Python，C#，Scala，Kotlin，hell，甚至 C++的地方。

```
func  extractTags(comment  string)  []string  {  re  :=  regexp.MustCompile("#\\S+")  tags  :=  re.FindAllString(comment,  -1)  // Strip #s  for  i,  tag  :=  range  tags  {  tags[i]  =  strings.TrimLeft(tag,  "#")  }  return  tags  } 
```

C 完了？回到 Python。现在我们必须将来自`--tag`开关的标签与我们从评论中找到的标签连接起来。这非常简单:

```
tags  :=  append(tagFlag,  extractTags(comment)...) 
```

正如先前所承诺的，我们现在有另一个问题。当同一个标签来自不同的地方或者同一个标签被多次提及时，标签就会重复。在一种运行时如此惊人的语言中，应该很快就能解决这个问题，对吗？不，不是真的。其中一个解决方案是创建一个 map，将所有的标签放入其中，然后遍历它的键，并将它们放入一个数组中。

```
tagSet  :=  map[string]bool{}  for  _,  tag  :=  range  tags  {  tagSet[tag]  =  true  }  uniqueTags  :=  []string{}  for  tag  :=  range  tagSet  {  uniqueTags  =  append(uniqueTags,  tag)  } 
```

与 Ruby 相比:

```
uniqueTags = tags.uniq 
```

不管是谁，最终的代码看起来像这样:

```
func  getTags(comment  string)  []string  {  tags  :=  map[string]bool{}  // Add all flags from --tag  for  _,  tag  :=  range  tagFlag  {  tags[tag]  =  true  }  // Add all flags from #tag  for  _,  tag  :=  range  extractTags(comment)  {  tags[tag]  =  true  }  // Deduplicate  uniqueTags  :=  []string{}  for  tag  :=  range  tags  {  uniqueTags  =  append(uniqueTags,  tag)  }  return  uniqueTags  } 
```

在完成所有这些艰苦的工作后，我可以在终端中这样写:

```
$ klk in --at '15 min ago' --tag coding "Writing some #tests"
Adding an entry: Writing some #tests at Fri Jan 25 01:53:05 CET 2019 with tags #coding #tests 
```

梦境时间追踪工具即将问世。我在重新学习一些 C 循环。双赢！

* * *

实现这一目标的谷歌搜索:

*   golang 标签解析器
*   golang 标签解析器
*   转到正则表达式
*   golang map
*   golang 独特的弦片
*   golang 合并切片唯一
*   golang 合并切片
*   golang set
*   作为切片的 golang 映射键
*   golang 地图获取关键点作为切片
*   被认为
*   往前走
*   转到范围修改
*   进行范围更新

* * *

耗时:60 分钟
总耗时:6:35 小时