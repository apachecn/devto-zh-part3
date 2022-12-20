# 禁用 Fedora ABRT 通知

> 原文：<https://dev.to/robbinespu/disabling-fedora-abrt-notification-1i40>

我不喜欢 ABRT，因为它滥发我的 fedora 系统通知。这有点困扰我，所以由于 MCE ABRT 通知不断垃圾邮件，我建议在`/etc/abrt/plugins/oops.conf`中设置`OnlyFatalMCE = yes`(你需要 root 访问权限来修改这个文件)。

如果需要，尝试禁用一些 ABRT 相关的服务。你可以用这个命令
找到它

```
$ sudo systemctl -t service | grep abrt 
```

比如我得到了 4 个 systemd 服务:

```
abrt-journal-core.service loaded active running Creates ABRT problems from coredumpctl messages                              
abrt-oops.service loaded active running ABRT kernel log watcher                                                      
abrt-xorg.service loaded active running ABRT Xorg log watcher                                                        
abrtd.service 
```

现在让我们停止它(对于当前会话)并禁用它(对于下次重新启动)

```
$ sudo systemctl stop abrt-journal-core.service 
$ sudo systemctl disable abrt-journal-core.service

$ sudo systemctl stop abrt-oops.service
$ sudo systemctl disable abrt-oops.service

$ sudo systemctl stop abrt-xorg.service
$ sudo systemctl disable abrt-xorg.service

$ sudo systemctl stop abrtd.service
$ sudo systemctl disable abrtd.service 
```

现在重启你的工作站。你应该不会再收到 ABRT 的通知了。

注意:这是我当前的步骤，可能不正确😛我不鼓励你无缘无故禁用自动问题报告(ABRT ),因为 ABRT 是一套帮助你检测和报告崩溃的工具。它的主要目的是简化报告问题和寻找解决方案的过程。