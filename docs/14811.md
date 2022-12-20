# 给 Go 另一个机会:后端

> 原文：<https://dev.to/detunized/giving-go-another-chance-backend-4923>

这一次，我准备用抽象接口和工厂做一些严肃的企业级工作。我想最终存储我可以用`in`和`out`命令创建的日志条目。在做了一些研究并使用了 [go-sqlite3](https://github.com/mattn/go-sqlite3) 之后，我决定目前不去改动数据库。我还没有*那个*企业准备好呢。尽管我很钦佩 SQLite 背后的工程努力和*单文件*存储的想法，但我真的不希望在我的`~`中有二进制 blob。至少现在是这样。

我认为将条目存储在一个简单的文本文件中是一个好的开始，其中每一行都是一个 JSON 对象。我很久以前就开始使用这种格式，因为我发现它是一个巨大的 JSON 数组的更好的替代方案。

考虑每行 JSON:

```
{"id":  0,  "comment":  "writing tests",  "tags":[]}  {"id":  1,  "comment":  "writing more tests",  "tags":[]} 
```

与常规 JSON 的比较:

```
[  {"id":  0,  "comment":  "writing tests",  "tags":[]},  {"id":  1,  "comment":  "writing more tests",  "tags":[]}  ] 
```

是的，可以用一行代码加载常规的 JSON 文件，这很好。但是生成这些文件是很痛苦的。除了最后一行之外，你必须担心每一行的包围和尾随逗号。其中 *JSON-per-line* 格式要简单得多。只需添加一行 JSON 对象，就大功告成了。通常解析也更快。可能是由于 JSON 解析或重新分配中的一些非线性。基本上，解析一百万个小 JSON 对象比解析一个包含一百万个条目的 JSON 数组要快。

如果我写任何函数并直接调用它们，那将是完全不专业的。我首先需要一个接口。在用 VS 代码搜索了一些弯弯曲曲的红色和绿色线条之后，我能够在 Go 中编写我的第一个界面了。

```
// Backend ...  type  Backend  interface  {  OpenEntry(comment  string,  startTime  time.Time,  tags  []string)  error  CloseEntry(endTime  time.Time)  error  } 
```

请注意`// Backend ...`注释。它的存在是有原因的。它让棉绒高兴，因为我不想到处都是绿色的曲线。很明显，linter 对没有评论的输出类型不满意。顺便说一下，如果类型以大写字母开头，那么它就是导出的。一些有趣的设计决定。

在 Go 中，任何实现这两个功能的东西都被认为是后端。不需要显式的接口实现。整洁又吓人。所以最简单的形式是空后端:

```
// NullBackend ...  type  NullBackend  struct  {  }  // OpenEntry ...  func  (backend  *NullBackend)  OpenEntry(comment  string,  startTime  time.Time,  tags  []string)  error  {  return  nil  }  // CloseEntry ...  func  (backend  *NullBackend)  CloseEntry(endTime  time.Time)  error  {  return  nil  } 
```

下一步是编写实际的文件后备存储。那并不困难。我必须弄清楚如何将一个结构序列化成 JSON。围棋很简单:

```
encoded,  err  :=  json.Marshal(entry) 
```

然后将其附加到文件中:

```
f,  err  :=  os.OpenFile("entries.json",  os.O_RDWR|os.O_CREATE|os.O_APPEND,  0644)  if  err  !=  nil  {  return  err  }  defer  f.Close()  f.Write(encoded)  f.WriteString("\n") 
```

我们的手卷 NoSQL 数据库已经准备好了。甚至可能很快与 MongoDB 竞争，只需要增加一些功能和一些安全漏洞。装运它！🚢

* * *

实现这一目标的谷歌搜索:

*   go sqlite
*   去检查文件是否存在
*   执行接口
*   go 指针接收器
*   golang 指针接收器接口
*   go 界面
*   golang ISO8601 日期
*   golang 写文件
*   golang 序列化为 json
*   戈朗元帅未报告的领域
*   golang writeline

* * *

耗时:3:50 分钟
总耗时:11:00 小时