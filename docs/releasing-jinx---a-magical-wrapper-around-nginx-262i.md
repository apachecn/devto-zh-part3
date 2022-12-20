# 发布 jinx——nginx 的神奇包装

> 原文：<https://dev.to/pretzelhands/releasing-jinx---a-magical-wrapper-around-nginx-262i>

> 这篇文章最初发表在我的个人博客上。

我对这个帖子超级兴奋，因为我做了一个东西！是的，我实际上用自己的双手做了一个东西，现在我正在发布它！我向你介绍: **jinx**

作为背景故事，我最近[由于意外地在很短的时间内生成了太多的加密账户而毁掉了我的整个服务器基础设施](https://pretzelhands.com/posts/bringing-your-entire-infrastructure-down)。这非常有趣，我花了周六的大部分时间在备份 nginx 上备份所有东西。

作为其中的一部分，我很快注意到了一个非常重复的模式来设置我的虚拟主机。每个网站都是这样的

```
# create a new site
cp \
    /etc/nginx/configurations/php.conf \
    /etc/nginx/sites-available/pretzelhands.com.conf

# change the host name to the new site name
nano /etc/nginx/sites-available/pretzelhands.com.conf  

# activate the site in nginx
ln -s \
    /etc/nginx/sites-available/pretzelhands.com.conf \
    /etc/nginx/sites-enabled

# restart to publish newly activated sites
systemctl restart nginx 
```

Enter fullscreen mode Exit fullscreen mode

使用`^R`稍微快了一点，但仍然令人难以置信的乏味。这是 4 个不同的命令，我必须输入相同或相似的路径多达四次。这种感觉并不好，所以我想起了我最近写的关于 shell 脚本的文章，并决定利用它来创建 **jinx** 。

同样的过程现在看起来像这样。

```
# create a new site and insert hostname in template file
jinx site create pretzelhands.com php

# activate the site and restart
jinx site activate pretzelhands.com -r 
```

Enter fullscreen mode Exit fullscreen mode

从四个长而重复的命令到两个非常短的命令。多好的改进啊。这只花了我一个周日下午的时间。

最初的开发工作可能比手动设置网站要高，但我仍然很高兴我这样做了，因为这样做很有趣，而且我可以一直重复使用它。每当我创建一个新的虚拟主机，这个工具可能会为我节省 2-3 分钟的重复输入。

还有一些其他的特性，但是你可以[阅读文档](https://github.com/pretzelhands/jinx/blob/master/README.md)中的所有内容。

我坦率地承认，这可能是我迄今为止最有用的一个兼职项目。此外，它还得到了更高层次的认可，所以我想这是有价值的。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [椒盐卷饼](https://github.com/pretzelhands) / [扫把星](https://github.com/pretzelhands/jinx)

### ✨jinx -一个神奇的 nginx 包装

<article class="markdown-body entry-content container-lg" itemprop="text">

[![jinx - a magical nginx wrapper](img/f097cc93d7189749312c22522866ff5d.png)](https://camo.githubusercontent.com/9dbe97a872b742444bb55cb05b5f226e259d32f1a6c1cb92d01c0a7021b3d7e3/68747470733a2f2f707265747a656c68616e64732e636f6d2f6173736574732f6a696e782f6a696e782d6c6f676f2d66756c6c2e706e67)

* * *

jinx 是一个完全用 Bash 编写的 nginx 的包装脚本。与使用简单的 shell 命令相比，它可以帮助您以更简化的方式管理您的站点和配置。

* * *

#### 目录

*   [先决条件](https://raw.githubusercontent.com/pretzelhands/jinx/master/#prerequisites)
*   [安装](https://raw.githubusercontent.com/pretzelhands/jinx/master/#installation)
*   [更新](https://raw.githubusercontent.com/pretzelhands/jinx/master/#updating)
*   [卸载](https://raw.githubusercontent.com/pretzelhands/jinx/master/#deinstallation)
*   [配置`jinx`](https://raw.githubusercontent.com/pretzelhands/jinx/master/#configuring-jinx)
    *   [设置选项](https://raw.githubusercontent.com/pretzelhands/jinx/master/#setting-options-via-jinx)
    *   [获取选项](https://raw.githubusercontent.com/pretzelhands/jinx/master/#reading-the-value-of-options-with-via-jinx)
    *   [可用选项](https://raw.githubusercontent.com/pretzelhands/jinx/master/#available-options)
*   [使用命令](https://raw.githubusercontent.com/pretzelhands/jinx/master/#using-the-commands)
    *   [T2`jinx start|restart|stop`](https://raw.githubusercontent.com/pretzelhands/jinx/master/#jinx-startrestartstop)
    *   [T2`jinx logs`](https://raw.githubusercontent.com/pretzelhands/jinx/master/#jinx-logs)
    *   `jinx site`
        *   [T2`jinx site activate`](https://raw.githubusercontent.com/pretzelhands/jinx/master/#jinx-site-activate)
        *   [T2`jinx site deactivate`](https://raw.githubusercontent.com/pretzelhands/jinx/master/#jinx-site-deactivate)
        *   [T2`jinx site delete`](https://raw.githubusercontent.com/pretzelhands/jinx/master/#jinx-site-delete)
        *   [T2`jinx site create`](https://raw.githubusercontent.com/pretzelhands/jinx/master/#jinx-site-create)
        *   [T2`jinx site edit`](https://raw.githubusercontent.com/pretzelhands/jinx/master/#jinx-site-edit)
*   [模板化](https://raw.githubusercontent.com/pretzelhands/jinx/master/#templating)
    *   [模板命名](https://raw.githubusercontent.com/pretzelhands/jinx/master/#naming-of-templates)
    *   [默认模板](https://raw.githubusercontent.com/pretzelhands/jinx/master/#default-template)
    *   [替换变量](https://raw.githubusercontent.com/pretzelhands/jinx/master/#replacement-variables)
*   [杂项](https://raw.githubusercontent.com/pretzelhands/jinx/master/#miscellaneous)
*   [贡献者](https://raw.githubusercontent.com/pretzelhands/jinx/master/#contributors)

* * *

## 先决条件

由于这是 jinx 的早期版本，它仍然对您的环境有一些假设。目前这些措施如下:

*   **你的站点被组织在两个文件夹中:`sites-available`和`sites-enabled`** :前一个保存所有你可能托管的可用站点，后一个保存可以在网上访问的站点。

## 装置

jinx 的安装可以使用方便的安装脚本在一行中完成…

</article>

[View on GitHub](https://github.com/pretzelhands/jinx)

我希望你能和我一样从这个工具中获得更多的乐趣和实际用途，我会尝试用一些更有用的功能来扩展它，比如激活 HTTPS 和从包含的片段中构建配置。我们看看我能走多远！

尽情享受吧！~