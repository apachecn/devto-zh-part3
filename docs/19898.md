# rcctl:如何在 OpenBSD 6.4 中调试

> 原文：<https://dev.to/nabbisen/debugging-rcctl-in-openbsd-3e70>

## 总结

这篇文章是关于围绕 [OpenBSD](https://www.openbsd.org/) 的 [`rcctl`](https://man.openbsd.org/rcctl) 的单个选项，实用程序“配置和控制守护进程和服务”。

总之，当运行`rcctl start %service%`失败时，**使用`-d`选项**提供了关于失败原因的良好信息。

### 环境

*   操作系统:OpenBSD 6.4 amd64

<center>✿ ✿ ✿</center>

## 一个问题

我试图建立一个 Gitea 服务器。
我应该只用 4 个多一点的步骤就完成了:

```
#  pkg_add gitea
#  rcctl enable gitea
#  nvim /etc/gitea/conf/app.ini
#  rcctl start gitea 
```

Enter fullscreen mode Exit fullscreen mode

但是现实一点:

```
#  rcctl start gitea # the same to the last step in the above
gitea(failed) 
```

Enter fullscreen mode Exit fullscreen mode

我拼了老命查日志和手册像这样:

```
#  rcctl check gitea
$  nvim -R /var/www/logs/%some-log%
$  nvim -R /var/log/%some-log%
$  nvim -R /usr/local/share/doc/pkg-manuals/gitea 
```

Enter fullscreen mode Exit fullscreen mode

但是现实仍然是苦涩的😖信息太少，找不到路。

## 解

**`-d`** 选项是。

### 之前

```
#  rcctl restart gitea 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
gitea(failed) 
```

Enter fullscreen mode Exit fullscreen mode

### [后](#after)

```
#  rcctl -d restart gitea 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
doing _rc_parse_conf
doing _rc_quirks
gitea_flags empty, using default >web<
doing _rc_parse_conf /var/run/rc.d/gitea
doing _rc_quirks
doing _rc_parse_conf
doing _rc_quirks
gitea_flags empty, using default >web<
doing _rc_parse_conf /var/run/rc.d/gitea
doing _rc_quirks
doing rc_check
doing _rc_parse_conf
doing _rc_quirks
gitea_flags empty, using default >web<
doing _rc_parse_conf /var/run/rc.d/gitea
doing _rc_quirks
doing rc_check
gitea
doing rc_start
doing _rc_wait start
No home directory /nonexistent!
doing rc_check
Logging in with home = "/".
panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=... addr=... pc=...]

goroutine 1 [running]:
code.gitea.io/gitea/vendor/gopkg.in/ini%2ev1.(*Key).transformValue(...
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/vendor/gopkg.in/ini.v1/key.go:123 +0x...
code.gitea.io/gitea/vendor/gopkg.in/ini%2ev1.(*Key).String(...
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/vendor/gopkg.in/ini.v1/key.go:130 +0x...
code.gitea.io/gitea/vendor/gopkg.in/ini%2ev1.(*Key).MustString(...
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/vendor/gopkg.in/ini.v1/key.go:202 +0x...
code.gitea.io/gitea/modules/setting.NewContext()
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/modules/setting/setting.go:1151 +0x...
code.gitea.io/gitea/routers.GlobalInit()
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/routers/init.go:47 +0x...
code.gitea.io/gitea/cmd.runWeb(...
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/cmd/web.go:83 +0x...
code.gitea.io/gitea/vendor/github.com/urfave/cli.HandleAction(...
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/vendor/github.com/urfave/cli/app.go:471 +0x...
code.gitea.io/gitea/vendor/github.com/urfave/cli.Command.Run(...
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/vendor/github.com/urfave/cli/command.go:191 +0x...
code.gitea.io/gitea/vendor/github.com/urfave/cli.(*App).Run(...
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/vendor/github.com/urfave/cli/app.go:241 +0x...
main.main()
        /usr/obj/ports/gitea-1.5.0/go/src/code.gitea.io/gitea/main.go:52 +0x...
doing _rc_rm_runfile
(failed) 
```

Enter fullscreen mode Exit fullscreen mode

信息多么丰富啊！
毕竟多亏了 Gitea 的 Github 问题: [#4692](https://github.com/go-gitea/gitea/issues/4692) 、 [#4260](https://github.com/go-gitea/gitea/issues/4260) ，我发现是 Gitea 的`app.ini`中`[U2F]`段设置错误造成的。

```
#  rcctl restart gitea
gitea(ok) 
```

Enter fullscreen mode Exit fullscreen mode

好😆

* * *

## 引用

`rcctl`还有一个强制选项`-f`来启动残疾人服务:

```
#  rcctl -f start %disabled-service% 
%disabled-service%(ok) 
```

Enter fullscreen mode Exit fullscreen mode

<center>✿ ✿ ✿</center>

为🕊服务愉快