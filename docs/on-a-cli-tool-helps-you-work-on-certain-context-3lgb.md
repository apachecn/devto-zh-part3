# on-CLI 工具可帮助您在特定环境下工作

> 原文：<https://dev.to/dannypsnl/on-a-cli-tool-helps-you-work-on-certain-context-3lgb>

上的[是为减少打字而创建的](https://github.com/dannypsnl/on)

当我与`kubectl`一起工作时，大多数时候我会使用`kubectl get pod`来获得所有的 pod，然后使用`kubectl exec -ti <pod-name> bash`来调试/测试我们的路由器，使用它的所有日子我发现它非常重复，作为一名软件工程师，我们讨厌重复。所以我创建了一个命令行工具来执行基于现有上下文的命令，这就是`on`。但是我刚刚提到了`kubectl`，为什么我要创建一个通用 CLI 呢？因为我还必须与其他命令如`go`、`neutron`、`docker`以相同的模式工作。

背景介绍完后，让我们看看它是如何工作的:

```
$ on go
on(go)> test
PASS
ok      github.com/dannypsnl/rocket 0.031s
on(go)> test ./...
ok      github.com/dannypsnl/rocket 0.033s
ok      github.com/dannypsnl/rocket/cookie  0.024s
ok      github.com/dannypsnl/rocket/fairing 0.018s
?       github.com/dannypsnl/rocket/internal/context    [no test files]
?       github.com/dannypsnl/rocket/internal/filepath   [no test files]
ok      github.com/dannypsnl/rocket/response    0.024s
ok      github.com/dannypsnl/rocket/router  0.013s
on(go)> test ./... -count 1 -cover -failfast
ok      github.com/dannypsnl/rocket 0.041s  coverage: 86.5% of statements
ok      github.com/dannypsnl/rocket/cookie  0.024s  coverage: 100.0% of statements
ok      github.com/dannypsnl/rocket/fairing 0.022s  coverage: 100.0% of statements
?       github.com/dannypsnl/rocket/internal/context    [no test files]
?       github.com/dannypsnl/rocket/internal/filepath   [no test files]
ok      github.com/dannypsnl/rocket/response    0.032s  coverage: 94.1% of statements
ok      github.com/dannypsnl/rocket/router  0.015s  coverage: 95.3% of statements
on(go)>
         test
         test ./...
         test ./... -count 1 -cover -failfast 
```

你可以看到提示会出现在提示框中，你可以使用`tab`从中选择。

您可以通过键入`Ctrl+a`将新上下文附加到当前上下文，然后键入附加部分，例如

```
on(go)> test
on(go test)> ./...
ok      github.com/dannypsnl/rocket (cached)
ok      github.com/dannypsnl/rocket/cookie  (cached)
ok      github.com/dannypsnl/rocket/fairing (cached)
?       github.com/dannypsnl/rocket/internal/context    [no test files]
?       github.com/dannypsnl/rocket/internal/filepath   [no test files]
ok      github.com/dannypsnl/rocket/response    (cached)
ok      github.com/dannypsnl/rocket/router  (cached)
on(go test)>
ok      github.com/dannypsnl/rocket (cached)
on(go test)
         ./... 
```

如您所见，提示会因不同的上下文而改变。然后你可以使用`Ctrl+c`删除上下文中的最后一个元素。提示也会变回来，例如

```
on(go test)>
on(go)>
         test
         test ./...
         test ./... -count 1 -cover -failfast 
```

一切看起来都不错，但不仅仅如此，`on`不会试图将一个失败的命令存储到提示中，所以不会太烦人。

并且`on`可以让你在里面运行一个 shell，比如

```
$ on kubectl
on(kubectl)> get po
NAME                             READY   STATUS    RESTARTS   AGE
alpine-deploy-5694b87b56-2gscz   1/1     Running   0          11h
alpine-deploy-5694b87b56-xnwl2   1/1     Running   0          11h
on(kubectl)> exec -ti alpine-deploy-5694b87b56-2gscz bash
bash-4.4# ls -la
total 64
drwxr-xr-x    1 root     root          4096 Mar 29 16:11 .
drwxr-xr-x    1 root     root          4096 Mar 29 16:11 ..
-rwxr-xr-x    1 root     root             0 Mar 29 16:11 .dockerenv
drwxr-xr-x    1 root     root          4096 Mar 29 16:11 bin
drwxr-xr-x    5 root     root           360 Mar 29 16:11 dev
drwxr-xr-x    1 root     root          4096 Mar 29 16:11 etc
drwxr-xr-x    2 root     root          4096 Mar  4 15:43 home
drwxr-xr-x    1 root     root          4096 Mar 29 16:11 lib
drwxr-xr-x    5 root     root          4096 Mar  4 15:43 media
drwxr-xr-x    2 root     root          4096 Mar  4 15:43 mnt
drwxr-xr-x    2 root     root          4096 Mar  4 15:43 opt
dr-xr-xr-x  224 root     root             0 Mar 29 16:11 proc
drwx------    1 root     root          4096 Mar 29 16:11 root
drwxr-xr-x    1 root     root          4096 Mar 29 16:11 run
drwxr-xr-x    2 root     root          4096 Mar  4 15:43 sbin
drwxr-xr-x    2 root     root          4096 Mar  4 15:43 srv
dr-xr-xr-x   13 root     root             0 Mar 29 16:09 sys
drwxrwxrwt    2 root     root          4096 Mar  4 15:43 tmp
drwxr-xr-x    1 root     root          4096 Mar 29 16:11 usr
drwxr-xr-x    1 root     root          4096 Mar  4 15:43 var
bash-4.4# exit
exit
on(kubectl)> 
```

完成工作后，只需输入`Ctrl-d`就可以离开`on`

感谢您的关注，我只在 MacOS 中用 iTerm2 测试了这个命令，所以我想知道在其他平台上的结果:)