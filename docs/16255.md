# 掌脸开发者🤦‍♂️ #2

> 原文：<https://dev.to/jsn1nj4/the-facepalming-dev--2-1h9j>

# 前言📝

别担心，所有这些都不会有序言，但我觉得这本书有点必要。

由于我的日常工作，我主要是一个 Windows 用户，我安装了 WAMP 服务器，所以我可以很容易地同时安装 PHP、Apache 和 MySQL。我只是没有意识到“自动安装”功能是针对第一次安装 WAMP 服务器的。

# 下兔子洞🐇

所以，我最初是从 PHP 5.5.x 和 MySQL 5.5.x 开始的，也可能是 MySQL 在 5.6.x，记不清了。但我有段时间没升级了。我按照找到的步骤手动安装了 PHP 7.2.6 和 MySQL 5.7.21。一切又都好了...暂时如此。

## 一段时间后...🕰

过了一会儿，WAMP 服务器无法再启动 MySQL 进程。我试着切换 WAMP 服务器使用的 MySQL 版本，并研究这个问题。Apache 正在启动，没有 PHP 问题。MySQL 就是启动不了。

经历了很多挫折后，我最终卸载了 WAMP 服务器，这也导致了卸载 Apache、我安装的大部分 MySQL 版本以及每个 PHP 版本中的大部分内容。我知道这样做会破坏这个设置，但我真的不在乎这一点。由于我不得不手动设置这些组件的任何新版本，WAMP 服务器现在对我来说几乎毫无用处，特别是如果其中一个组件因为 WAMP 服务器无法正常启动而崩溃(除了 PHP，它没有 WAMP 服务器需要启动的服务)。

## 出人意料👀

随着 WAMP 服务器的退出，我安装了 PHP 7.3.0，设置了 php.ini 文件，并把它放在我的路径中。我已经准备好了！

然后我发现我不是...

当我试图启动一个我已经工作了一段时间的 Laravel 项目(这个项目还没有使用 MySQL)时，它一直抱怨不能使用 PHP 的 OpenSSL 相关函数，尽管这个扩展在 php.ini 中启用了。

然后我进一步向下滚动错误，注意到它仍然使用 PHP 7.2.6。

**重要的事情:**在之前的*我已经有了 PHP 7.3.0，它是在`c:\wamp\bin\php`中的旧的 7.2.6 版本。旧版本并没有 100%消失，但是大部分内容在安装时被移除了(包括扩展)。因此，如果项目正在寻找它，它可以部分运行，但不是完全运行。*

这个项目仍然试图使用 7.2.6。这说不通。无论我重启这个项目多少次，它都不会获得新的 PHP 版本。

所以我暂时放弃了。

今天，我决定解决这个问题，但可能是在重新安装 PHP 7.2.6 之后。无论如何，我最好在机器之间使用相同的版本(我的 Linux 安装目前使用这个版本)。

我浏览了 7.3.0 和 7.2.6 的 php.ini 文件，看看是否有什么遗漏。然后我发现了这个:

```
; When enabled, the ENV, REQUEST and SERVER variables are created when they're
; first used (Just In Time) instead of when the script starts. If these
; variables are not used within a script, having this directive on will result
; in a performance gain. The PHP directive register_argc_argv must be disabled
; for this directive to have any affect.
; http://php.net/auto-globals-jit auto_globals_jit = On 
```

Enter fullscreen mode Exit fullscreen mode

😶...

我愣了一分钟。然后我决定尝试一些以前没有想过的事情:我打开我的终端，运行`php artisan tinker`，从那里检查路径。

```
foreach( explode(';', env('PATH')) as $path ) {
  echo "$path\n";
} 
```

Enter fullscreen mode Exit fullscreen mode

它与我的新路径设置相匹配！

我启动了我的项目，打开了浏览器，页面加载完毕。😑

我意识到了另一件事:在我试图启动我的项目的那几天，**我让我的笔记本电脑进入了休眠状态**。从技术上讲，这是*“关机”，但是状态被重新加载，就像休眠时一样。*

 *## 吸取教训📒

1.  多梳理一下新配置，以防有重要的额外细节
2.  找到一种方法来检查你的路径*就像你的项目看到的那样*
3.  如果路径或其他最近的设置由于某种原因还没有生效，重新启动也无妨

**好处:**如果您使用[酒店](https://github.com/typicode/hotel)在 Windows 上管理项目流程，您可能不会马上知道，但酒店也存储了路径的副本——当项目被添加到酒店时，每个项目的配置中都会存储一个副本*。这是我最近在拆除 WAMP 服务器并手动安装 PHP 7.3.0 后发现的另一件事。*

希望这是一个有趣的阅读，其他人可以在未来从这里收集。享受你余下的周末吧！*