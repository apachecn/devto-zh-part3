# 用 Jekyll 和 Namecheap 制作博客的快速简单指南

> 原文：<https://dev.to/ctrlshifti/how-to-make-a-blog-with-jekyll-and-get-it-online-in-less-than-an-hour-5a46>

如果你还没有安装 Ruby，就安装它。

```
sudo apt update
sudo apt install ruby-full 
```

现在安装 Jekyll。

```
sudo gem install jekyll 
```

创建一个新的 Jekyll 项目。

```
jekyll new blogname 
```

(注意:如果你想使用一个模板，而不是使用上述命令，只需 git 克隆你想要的模板)

现在，只需将 cd 放入 Jekyll 刚刚创建的文件夹，并检查 _ *config.yml* 文件。更改所有适用的内容，如姓名、电子邮件以及其他任何内容(如果您使用的是模板)。*网址*可以在你购买域名后更改。

要发布帖子，在 _ *posts* 目录下创建一个 markdown 文件，命名约定为*yyyy-mm-DD-name-of-post . MD*，例如*2019-03-10-how-to-Jekyll . MD*。文件的格式应该是这样的:

```
---
layout: post
title: Welcome to Jekyll!
categories: jekyll
---
Post content goes here. 
```

如果你使用的是模板，检查一下样本文章，看看你还应该在顶部添加什么参数。

您可能需要运行以下命令来确保您拥有所有必需的依赖项:

```
bundle install 
```

做完这一切后，你运行这个来看看你的网站在运行:

```
bundle exec jekyll serve 
```

现在你的网站应该在 *[上线 http://127.0.0.1:4000/](http://127.0.0.1:4000/)* 。在 Github 中为它做一个 repo，这样我们以后就可以用 Github 页面设置了。把项目推到你的新回购上。

```
git add .
git commit -m "hello world"
git remote add origin [repo_url]
git push origin master 
```

接下来，我们要把这个吐到网上。在本指南中，我将使用 Namecheap，但是任何其他域提供者也可以。

[![domain_purchase](img/52f725626a9ac98a600a9e952a14a36f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8WPUyO6u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.explainhownow.com/asseimg/jekyll2.png)T3】

购买域名后，您需要更改 DNS 设置，以便可以访问该域名。在 Namecheap 中，你可以进入仪表盘，选择域名旁边的“管理”，然后选择“高级 DNS”，添加以下记录，把我的 Github 用户名改成你的。

[![dns](img/e6a876f10e75967b42860df02ce39054.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WRIkl_d6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.explainhownow.com/asseimg/jekyll3.png)
T4】

```
A Record    @   185.199.108.153
A Record    @   185.199.109.153
A Record    @   185.199.110.153
A Record    @   185.199.111.153
CNAME Record    www     you.github.io.      30 min 
```

现在进入你的博客回购的设置页面，应该是*[https://github . com/【your git name】/【your blogname】/设置](https://github.com/%5Byourgitname%5D/%5Byourblogname%5D/settings)* 。

在“Github Pages”标题下，添加您的自定义域名(应该是类似于)并点击保存。不要忘记将 _ *config.yml* 中的 *url* 以*[https://www.blogname.com](https://www.blogname.com)*的形式改为你的域名。DNS 解析可能需要一个小时左右，但最终，你应该可以在你购买的域名下看到你的网站。耶！干得好。注:实际博客内容单独出售。
T13】

### 附加提示

#### 模板

以上应该可以，但是你很酷，所以你想要一个很酷的博客。如上所述，让你的博客在一个定制模板上工作的步骤应该是完全一样的，但不是 *jekyll new blogname* 你只是把模板和 cd 克隆到里面，在那里做其他的事情。你可以从这些网站找到各种各样的 Jekyll 模板:

*   [http://jekyllthemes.org/](http://jekyllthemes.org/)
*   [https://jekyllthemes.io/](https://jekyllthemes.io/)
*   [http://themes.jekyllrc.org/](http://themes.jekyllrc.org/)

#### SSL

接下来，你会希望你的博客有 SSL，以避免任何人发现他们在你的网站上的尴尬。我想也是因为内容污损。幸运的是，Github Pages 很容易为您做到这一点——在您的 git repo 设置下，只需勾选“Github Pages”下的“强制 HTTPS”框。不要买 Namecheap 的 SSL 证书，不需要。

#### 图像

在您的根项目文件夹中，创建一个名为 *assets* 的文件夹，并在 assets 中制作一个*图像*。把图像放在那里。然后，在你的帖子内容里，加上这样的话:

```
![alt_text]({{static.static_files}}/asseimg/image.png) 
```

#### 自动站点地图

你可以让 Jekyll 自动为你生成一个网站地图！将 [Jekyll Sitemap 插件](https://github.com/jekyll/jekyll-sitemap)添加到您的项目中(说明在自述文件中)。

如果你使用 *bundle exec jekyll serve* 来构建你的站点，你可能会遇到一个问题，你的站点地图用 localhost 术语列出你的 URL，而不是用你的域名。相反，尝试用 *bundle exec jekyll build* 构建你的站点文件。如果您发现这仍然不起作用，请输入以下命令:*JEKYLL _ ENV = prod JEKYLL serve*

*本帖最初发表于[explainhownow.com](https://www.explainhownow.com/)T3】*