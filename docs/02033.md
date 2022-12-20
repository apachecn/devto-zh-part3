# 去高尔夫吧

> 原文：<https://dev.to/suganoo/go-gologger-3342>

现在我正在开发 gologger 作为 golang 的日志。
Github 仓库在这里。https://github.com/suganoo/gologger
T2

## 样本代码

这是如何使用 gologger 的示例代码。

```
go get -u github.com/suganoo/gologger 
```

gologgerSample.go

```
package main

import (
    "github.com/suganoo/gologger"
)

var glog *gologger.Gologger

func hogeFunc() {
    glog.Debug("this is debug hogeFunc")
    glog.Info("call hogeFunc")
}

func main() {
    glog = gologger.NewGologger(gologger.Configuration{Logfile : "./testlog.log"})
    defer glog.CloseFile()

    msg := "hogehoge"
    glog.Debug("this is debug")   // default debug is muted
    glog.Info("this is info")
    glog.Info("msg : " + msg)
    glog.Warning("this is warning")
    glog.Error("this is Error")

    glog.UnmuteDebug()
    hogeFunc()

    glog.Debug("this is debug xxx")
    glog.MuteDebug()
    glog.Debug("this is debug yyy")  // this debug message is muted
} 
```

```
go run gologgerSample.go

cat testlog.log
2018-02-21T10:07:44.277+09:00   INFO    hoge.sever  3892    gid:1   fuga-user   1.0.0   this is info    main    [gologgerSample.go:18]
2018-02-21T10:07:44.277+09:00   INFO    hoge.sever  3892    gid:1   fuga-user   1.0.0   msg : hogehoge  main    [gologgerSample.go:19]
2018-02-21T10:07:44.277+09:00   WARNING hoge.sever  3892    gid:1   fuga-user   1.0.0   this is warning main    [gologgerSample.go:20]
2018-02-21T10:07:44.277+09:00   ERROR   hoge.sever  3892    gid:1   fuga-user   1.0.0   this is Error   main    [gologgerSample.go:21]
2018-02-21T10:07:44.277+09:00   DEBUG   hoge.sever  3892    gid:1   fuga-user   1.0.0   this is debug hogeFunc  hogeFunc    [gologgerSample.go:8]
2018-02-21T10:07:44.277+09:00   INFO    hoge.sever  3892    gid:1   fuga-user   1.0.0   call hogeFunc   hogeFunc    [gologgerSample.go:9]
2018-02-21T10:07:44.277+09:00   DEBUG   hoge.sever  3892    gid:1   fuga-user   1.0.0   this is debug xxx   main    [gologgerSample.go:26] 
```

## 特性

*   您可以获得各种日志项，如文件的行号、goroutine 号等。
*   您可以在 gologger 函数(Info()、Warning()中编写任何类型的日志消息，比如 int、string 或 struct...).

如果你对 gologger 有什么兴趣的话，试试就太好了。你可以在 gologger 知识库中找到更多信息。

谢谢你。