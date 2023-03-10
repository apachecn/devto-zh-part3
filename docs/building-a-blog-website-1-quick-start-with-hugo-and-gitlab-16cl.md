# Hugo 和 GitLab 博客(1):快速入门

> 原文：<https://dev.to/loadbalancing/building-a-blog-website-1-quick-start-with-hugo-and-gitlab-16cl>

如今，建立自己的博客网站很容易。只需几个步骤，你就能创建自己的博客网站，访问地址为*https://username . git lab . io*。

## 为什么是雨果？

如果你的目标是建立一个静态网站(这对于个人博客来说已经足够好了)，那么现在有几种解决方案可以让这个过程变得简单和自动化。基本上你需要做的是写一些 [Markdown](https://en.wikipedia.org/wiki/Markdown) 文档，他们会帮你把它们转换成静态 HTML 文件。在这些选项中， [Hugo](https://gohugo.io) 被认为是静态页面构建方面最快的一个，因为它是用编译的 go 编写的，而其他选项都是基于解释的。

## 为什么是 GitLab？

GitLab 免费提供 [GitLab 页面](https://about.gitlab.com/product/pages/)的服务，用于托管静态网站。GitLab Pages 服务还提供了对 Hugo 的原生支持，这使得构建和部署您的 webiste 变得极其容易和方便，而无需回复任何其他第三方支持。

如果你确信，第一步是在 GitLab 中创建一个帐户。请注意，您在 GitLab 中的用户名将是您网站 URL 的子域名，如*https://username . git lab . io*。

## 创建您的网站资源库

既然我们的目标是 Hugo+GitLab，那么最简单的方法就是在 GitLab 中分叉官方 Hugo 库。

1.  前往 GitLab 上的 [Hugo 仓库](https://gitlab.com/pages/hugo)
2.  使用您的 GitLab 帐户登录。
3.  派生此存储库。

一些进一步的配置:

1.  去除分叉关系，除非你想对上游项目有所贡献，否则没有必要。
    【设置】>【常规】>【高级】>点击【展开】
    点击【解除分叉关系】。

2.  重命名存储库，这样它将链接到您网站的 URL*https://username . git lab . io*。
    【设置】>【常规】>【高级】>点击“展开”>【重命名资源库】
    【项目名称】: *username.gitlab.io*
    【路径】: *username.gitlab.io*
    点击“重命名项目”。

3.  将您的存储库标记为私有——如果您只想让人们通过 URL 访问您的网站，但不能查看您的存储库。
    【设置】>【常规】>【可见性、项目特性、权限】>点击
    【展开】
    【项目可见性】:私有
    点击【保存修改】。

4.  更新`config.toml`中的基本 URL。

```
diff --git a/config.toml b/config.toml
--- a/config.toml
+++ b/config.toml
@@ -1,4 +1,4 @@
-baseurl = "https://pages.gitlab.io/hugo/"
+baseurl = "https://username.gitlab.io" 
```

几分钟后，你就可以访问你的网站了，网址是*https://username . git lab . io*。