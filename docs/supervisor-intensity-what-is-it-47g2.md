# 主管强度，是什么？

> 原文：<https://dev.to/bakenator/supervisor-intensity-what-is-it-47g2>

您知道吗，如果 Elixir 管理员检测到子进程中出现了问题，他们会停止尝试重新启动子进程。

他们有！

这就引出了下一个问题，他们如何知道一个子进程何时会失败？

主管强度！

# 什么事？

监督者的强度设置是在一定时间内它可以容忍子进程出现多少次故障。如果记录了更多的失败，那么管理程序将停止所有子进程并使自己失败。

强度和周期是可选的设置，可以像这样在药剂中设置

```
opts = [..., max_restarts: 3, max_seconds: 5]
Supervisor.start_link(children, opts) 
```

请注意，我使用的是 Elixir 当前默认的值，在 5 秒内失败 3 次。

# 你什么时候碰到这个的？

我在处理一个很好的 bug 时发现了这一点。在我的一个子进程的顶部，我有这样一行代码

```
def start_link(port: port, dispatch: dispatch) do
    {:ok, socket} = :gen_tcp.listen(port, active: false, packet: :http_bin, reuseaddr: true) do
    ... 
```

我故意破坏了这个进程，随后整个应用程序都会关闭。这让我很困惑，因为主管不应该重启流程吗？
这个问题是，如果端口已经被另一个套接字使用，`:gen_tcp.listen`会返回一个错误。

所以第一次之后，这个模块在`start_link`的第一行抛出一个错误。主管很快尝试了三次，都失败在同一个 bug 上，主管就被关闭了。

# 主管为什么要这么做？

简而言之，这是为了防止无限循环的进程试图重新启动一遍又一遍。

但是请注意，管理员并不只是停止尝试重新启动孩子。如果超过强度限制，监控器会自动关闭。

为什么？

它会关闭，以便该主管的任何可能的主管都可以得到通知，有古怪的事情正在发生，并尝试解决问题。最初的主管已经尽了最大努力，现在基本上是将问题向上传递以寻求帮助。

# 在家试试！

这里有一个不错的小模块，你可以在自己的应用程序中试用。

将这一行放入你的主管孩子开始列表

```
{FailTwoSeconds, []} 
```

并将该模块添加到您的项目

```
defmodule FailTwoSeconds do
    def start_link([]) do
        IO.inspect "New Process Starting"

        pid = spawn_link(fn -> 
            Process.sleep(2000)
            raise "Failing Now"
        end)
        {:ok, pid}
    end

    def child_spec(opts) do
        %{
            id: FailTwoSeconds, 
            start: {FailTwoSeconds, :start_link, [opts]}, 
        }
    end
end 
```

我们可以看到，该模块的存在只是为了启动，等待 2 秒钟，然后崩溃。

但是每 2 秒钟崩溃一次是在药剂的默认强度范围内。因此，这个模块将继续崩溃，并重新启动，直到奶牛回家。

如果你把睡眠时间切换到`1000`，那么它将触发主管的强度极限，主管会说“我已经受够了！”和关机。

感谢你的阅读，希望有一天这能让你不再头疼！