# 如何通过 Hugo 为 GitHub 页面生成静态站点

> 原文：<https://dev.to/callas1900/how-to-generate-a-static-site-by-hugo-for-github-pages-1el9>

# 概述

我想使用静态站点生成器，而不是手写 html 页面。我需要一个静态网站生成器，因为我是由 GitHub 网页托管我的网页。
我决定用[雨果](https://gohugo.io/)是因为[终端主题](https://themes.gohugo.io/hugo-theme-terminal/)。
供你参考，这是从手写过渡到 Hugo 的步骤。

# 安装雨果

我用的是 linux mint 和 linux brew。有用！

```
$ brew install hugo
...
$ hugo version
Hugo Static Site Generator v0.55.4/extended linux/amd64 BuildDate: unknown 
```

# 创建新站点&安装主题

我选择了[终端主题](https://themes.gohugo.io/hugo-theme-terminal/)T2

```
$ hugo new site hugo_webpage
$ cd hugo_webpage
$ git init
$ git submodule add https://github.com/panr/hugo-theme-terminal.git themes/terminal 
```

使用`submodule`不是强制性的。

# 配置

```
vim config.toml 
```

通过阅读 [README.md](https://github.com/panr/hugo-theme-terminal/blob/master/README.md#how-to-configure)

# 从旧站点复制

我有几个手写的 html 页面。有些页面可以转到`content/`目录下的 Hugo markdown documents，但其余页面无法完成。
所以我不得不使用`static/`目录来保存剩下的页面。

```
$ cp -R ${old_pages} static/ 
```

# 确认

雨果有一个服务的未来。我们可以用这个来确认。

```
$ hugo server 
```

另见:[雨果发球命令](https://gohugo.io/commands/hugo_server/)

您可以通过`localhost:1313`确认您的站点

* * *

## 故障排除#1

如果你不能确认你的雨果网页。
请使用以下命令
再次检查

```
$ hugo server -D 
```

如果你能确认这几页。你应该改变 hugo markdown 的元数据。

```
- draft: true + draft: false 
```

* * *

# 建造它

hugo 命令将您的静态 html 页面生成到公共目录中。

```
$ hugo 
```

参见:[雨果命令](https://gohugo.io/commands/hugo/)

# 确认静态 html 服务器

```
$ cd public
$ python -m http.server 8000 
```

# 出版

我将`public/`目录中的所有内容复制到 GitHub 页面的存储库中，然后进行推送。

仅此而已。

* * *

## 故障排除#2

起初，我试图为我的首页创建一个索引页，旁边是一个关于页面，如下所示。

```
content/
├── index.md
├── about.md
... 
```

我对生成的页面结构的期望如下。

```
public/
...
├── about
│   └── index.html
└── index.html 
```

然而，关于目录不见了。
起因是`index.md`。在雨果，`index.md`和`_index.md`有着特殊的含义。(【https://gohugo.io/content-management/organization/】T4)

最终我放弃了创建一个首页。所以我创建了下面的结构。

```
content/
├── about.md
├── posts
│   └── hello.md
... 
```

这是真的。