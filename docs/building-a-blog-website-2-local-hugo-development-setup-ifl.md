# Hugo 和 GitLab 博客(2):本地开发设置

> 原文：<https://dev.to/loadbalancing/building-a-blog-website-2-local-hugo-development-setup-ifl>

虽然您可以在 GitLab 中在线编辑页面(例如，通过 Web IDE，它还允许预览 Markdown 页面)，但 Hugo 的本地开发环境提供了“所见即所得”的最佳支持。

## 环境

*   macOS Sierra。
*   雨果 v0.54.0。

## 创建本地知识库

使用 GitLab 上托管的网站存储库，我们可以将存储库克隆到本地。

1.  将您的网站库克隆到本地。

```
git clone https://gitlab.com/username/username.gitlab.io.git 
```

1.  在本地存储库中配置您的 GitLab 帐户电子邮件，它将被用作所有变更提交的标识。

```
git config user.email "example@email.com" 
```

可以在`.git/config` :
中验证更新后的账户信息

```
[user]                                                                           
email = example@email.com 
```

## 将 Hugo 安装到本地

1.  在 macOS 中安装自制软件。

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

1.  安装 Hugo。

```
brew install hugo 
```

通过查看 Hugo 版本来验证安装:

```
hugo version 
```

## 启动 Hugo 服务器

在启用“草稿”的情况下启动 Hugo 服务器，这将显示所有页面，包括本地服务器上的草稿。

```
hugo server -D 
```

现在您可以在本地查看您的网站:*http://localhost:1313*。

Hugo 服务器会一直运行，直到你按 Ctrl+C 停止它。任何保存的更改将立即重新加载。