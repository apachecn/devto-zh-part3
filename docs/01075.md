# 通过 brew 在 Mac 上安装 PostgreSQL

> 原文：<https://dev.to/0xkoji/install-postgresql-on-mac-via-brew-2a8n>

## 安装

本案正在安装 9.6。如果去掉`@9.6`，会得到最新版本的 PostgreSQL。

```
$ brew reinstall postgresql@9.6
$ echo 'export PATH="/usr/local/opt/postgresql@9.6/bin:$PATH"' >> ~/.zshrc
or 
$ echo 'export PATH="/usr/local/opt/postgresql@9.6/bin:$PATH"' >> ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

## 开始&停止

```
$ brew services start postgresql
$ brew services stop postgresql
$ brew services list 
```

Enter fullscreen mode Exit fullscreen mode