# WordPress -被黑客攻击的网站重定向

> 原文：<https://dev.to/uwutrinket/wordpress-hacked-site-redirections-4i8d>

## 问题

我遇到过一个项目，WP (WordPress)网站被重定向到一些“广告”网站。

## 识别问题

通过调试，从带有`pause script execution`的谷歌 Chrome `Sources`面板，我们发现重定向发生在项目内部的`.php`文件中注入的第三方脚本。

## 根本原因

一个插件更新[相关帖子插件](https://wordpress.org/plugins/yuzo-related-post/)。

WP 管理员在更新通知后更新了插件，却不知道插件实际上变成了劫持网站的“恶意软件”。

当然，插件网站上没有源代码，所以很难事先检查代码的完整性。在检查该插件的 WP 页面后，状态为`This plugin was closed on March 30, 2019 and is no longer available for download.`，但插件所有者已在 2019 年 4 月 10 日晚上 7 点左右(巴黎时间)推送了一条更新(掩盖踪迹？).

## 修复问题

### 1)插件移除

只有在插件移除不影响插件的外部文件的情况下，才可能解决问题。

### 2)用手清洁(不安全)

在我的例子中，“恶意软件”在`${ROOT}/wp-includes/`成功地改变了文件。于是我不得不“手动”清洗所有的`javascript`针剂。

### 3)备份

如果你在感染前有一个`backup`的数据库，我建议删除这个项目并重新安装这个项目和饲料`backup`。

## 避免外挂恶意软件

1.  永远不要在生产环境中更新插件
2.  做备份
3.  安装前检查插件源代码

来源:
[重定向恶意软件第三方链接](https://hellofromhony.org/counter)
[类似恶意软件要点](https://gist.github.com/stefan2904/a31a676b6fc202d2c6428acd728ad791)
[插件入罪 StackOverflow 帖子](https://stackoverflow.com/questions/55610548/vulnerability-in-closed-plugin-yuzo-related-posts)
[相关被黑问题](https://stackoverflow.com/questions/50693160/wordpress-hacked-script-is-injected-into-wp-emoji-release-min-js-jquery-js)