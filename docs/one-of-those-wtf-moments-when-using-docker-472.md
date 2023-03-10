# 使用 docker 时的一个 wtf 时刻

> 原文：<https://dev.to/zaffja/one-of-those-wtf-moments-when-using-docker-472>

我使用 docker 已经一年半了。从那以后，我学到了许多关于 docker 的简洁而又酷的技巧。我将与你分享一个我在使用 docker 时发现非常有趣的技巧。看看下面的脚本。

```
docker run --rm -it -v $(PWD):/app -w /app busybox rm -rf deps 
```

乍看之下，你可以看出我使用`busybox` image 只是为了删除我的`deps`文件夹。但是我们为什么要担心这个呢？事实证明，docker 利用了 root 访问权限，这意味着我们基本上是在运行`sudo rm -rf deps`而不需要询问我们的 sudo 密码！那很危险！！！

虽然它很危险，但也很有用。我们只是要小心使用它，仅此而已。Tbh，在我的开发过程中，我经常使用这个技巧。上面的例子实际上是我的`Makefile`设置的一部分，如下图
所示

```
setup:
        docker run --rm -it -v $(PWD):/app -w /app busybox rm -rf deps
        docker run --rm -it -v $(PWD):/app -w /app elixir:1.6 mix local.hex --force && mix deps.get
        cd assets && $(MAKE) setup
        docker-compose build 
```

如你所见，我正在开发一个 elixir 应用程序，而`make setup`是你想运行很多次(如果不只是一次)的东西。因此，这确保了如果有人要克隆我的项目，为他们运行`make setup`将是轻而易举的，不会有任何关于许可的问题。

不管怎样，你觉得这个戏法怎么样？好吃吗？不好？请与我分享您的想法，如果可能的话，我该如何改进我的设置。