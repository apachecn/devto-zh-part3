# Apache -将所有 URIs 重写到一个文件中

> 原文：<https://dev.to/lyubomir/apache-rewrite-all-uris-to-a-single-file-1mcb>

这个星期，我不得不帮助一个朋友在 Apache 上运行他的新的小型 ReacJS 网站。

他的问题是不存在的 URIs Apache 返回 404 页，他需要一个被很多 PHP 框架使用的通用规则，重写所有不是文件或目录的 URIs 到 **/index.html**

示例 URL `www.example.com/myprofile`实际加载`www.example.com/index.html`并让 ReactJS 路由做他的工作并显示它需要什么。

在这种情况下，你可以这样写:

```
 RewriteEngine on

    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^ index.html [L] 
```

这是在做什么，它将检查 URI 是作为一个文件存在还是一个目录，如果不是，它将调用**index.html**。L 表示这将是最后一场比赛，将在此停止，如果我们有其他规则，将不会继续。

这是代码是可以的，不是那么简单，你可以弄乱条件，例如，我已经看到了很多[或]旁边的`RewriteCond %{REQUEST_FILENAME} !-f [OR]`，这将不会在我们的情况下以上。

如果你需要这个简单的重写规则，你可能会使用在 **2.2.16 版本**
之后可用的 Apache 命令

```
 FallbackResource /index.html 
```

如果在服务器上找不到 URI，这将和上面 4 行一样。不是文件或目录，它将加载到 **/index.html**

这个命令不是 **mod_rewrite** 的一部分，而是 **mod_dir** 的一部分，如果你没有启用它，运行`sudo a2enmod dir`并重启 Apache。