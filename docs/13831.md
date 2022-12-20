# 在 WSL1 下安装 Nix

> 原文：<https://dev.to/notriddle/installing-nix-under-wsl-2eim>

我正在管理 Linux 服务器，同时使用 Windows 作为我的桌面，并且正在使用 NixOS 进行测试部署。我在本地运行时遇到了一些问题。

在尝试运行 Nix 软件包管理器安装程序脚本之前，创建一个目录`/etc/nix`并将以下文本写入`/etc/nix/nix.conf` :

```
# Work around missing cgroups support https://github.com/Microsoft/WSL/issues/994
sandbox = false
# Work around incorrect file locking https://github.com/Microsoft/WSL/issues/2395
use-sqlite-wal = false 
```

Enter fullscreen mode Exit fullscreen mode

## 解决缺少 cgroups 支持的问题

Nix 沙箱依赖于容器功能，Windows 还没有为 Linux 个性实现这些功能。如果你不关闭沙箱，你会得到这个错误。

```
error: cloning builder process: Invalid argument
error: unable to start build process 
```

Enter fullscreen mode Exit fullscreen mode

## 解决不正确的文件锁定

Windows NT 中的锁处理并不完全符合 Linux。虽然 SQLite 在 Win32 个性下运行良好，但是当您试图安装 Nix 时，它会产生死锁，从而产生这个无限的警告循环:

```
warning: SQLite database '/nix/var/nix/db/db.sqlite' is busy (SQLITE_PROTOCOL)
warning: SQLite database '/nix/var/nix/db/db.sqlite' is busy (SQLITE_PROTOCOL)
warning: SQLite database '/nix/var/nix/db/db.sqlite' is busy (SQLITE_PROTOCOL)
warning: SQLite database '/nix/var/nix/db/db.sqlite' is busy (SQLITE_PROTOCOL)
warning: SQLite database '/nix/var/nix/db/db.sqlite' is busy (SQLITE_PROTOCOL) 
```

Enter fullscreen mode Exit fullscreen mode