# 配置 Go sql 的重要性。数据库连接

> 原文：<https://dev.to/aaronellington/importance-of-configuring-go-sqldb-connections-15bc>

## 背景

我最近开始从 PHP 切换到 Go，我错过了一些重要的东西(但现在看起来非常明显)。我第一次尝试技术博客是一个巨大的失败:

[![aaronellington](img/fd4f5b13b16f16714c41b783579a9529.png)](/aaronellington) [## 转到 sql。数据库周期性错误:无效连接

### 亚伦埃林顿 8 月 26 日 182 分钟阅读

#go #sql #beginners](/aaronellington/go-sqldb-periodic-error-invalid-connection--35a)

我想回去展示我学到的东西。我也希望这一次我没有遗漏任何大的东西。

## 一期

来自 PHP，我习惯于脚本频繁地启动和停止，并且打开的数据库连接只会保持打开几秒钟(最多)。但是当我切换到 Go 时，我的脚本运行了几天，而不是几秒钟。

如果超过 15 分钟没有执行 MySQL 查询，下面的尝试将会失败，并出现一个`invalid connection`错误。在我之前的帖子中，我发现每 5 分钟调用一次`db.Ping()`“基本上”解决了我的问题。但正如许多人善意指出的那样，这不是一个好的解决方案。我也时不时会出错，但不像以前那么频繁了。

## 解(新！)

不太确定第一次是怎么漏掉的，但是有一个`db.SetConnMaxLifetime`函数。这允许 sql 包在抢先关闭连接并重新打开新连接之前，处理连接可以持续多长时间。默认是永远重用它们。文档说得好:

```
$ go doc sql.DB.SetConnMaxLifetime
func (db *DB) SetConnMaxLifetime(d time.Duration)
    SetConnMaxLifetime sets the maximum amount of time a connection may be
    reused.

    Expired connections may be closed lazily before reuse.

    If d <= 0, connections are reused forever. 
```

Enter fullscreen mode Exit fullscreen mode

我与我们的数据库团队一起工作，看看 MySQL 服务器多久后会开始终止空闲连接，并使用我新发现的`SetConnMaxLifetime`函数来匹配服务器。从那以后，在过去的几个月里，我收到了将近 0 个连接错误。

```
package main

import (
    "database/sql"
    "time"

    _ "github.com/go-sql-driver/mysql"
)

func main() {
    db, _ := sql.Open("mysql", "user:password@/dbname")
    // Error handling omitted for this example
    db.SetConnMaxLifetime(time.Minute * 5)

    // Start the web server
} 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的想法

虽然这现在看起来是一个显而易见的概念，但当我第一次开始学习围棋时却不是这样。希望这能帮助其他人避免我犯的同样的错误。