# 制作一个漂亮的网站，学习哲基尔:教程

> 原文：<https://dev.to/andy1/make-a-pretty-site-learn-jekyll-tutorial-m8h>

前几天，为了熟悉 GIMP 的用法，我去了 Sirar Salih 的开发者 T2 的博客 T3。

我觉得它看起来很好:

[![Sirar Salih's blog](img/1bd0a558413745553f5c6bbf2efa794b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ji0ed0hb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h3ydp4v8b4a2o3n3km76.png)

有帮助的是，Sirar 链接到他页面上的原始主题: [Hydejack](https://hydejack.com/) ，由 [Jekyll](https://jekyllrb.com/) 构建，托管在 Github 页面上。显然，我不得不亲自尝试。

下面是我的[工作版](https://an-dy1.github.io/hydejack-by-andy/):

[![working hydejack version](img/79b63202a454a6129f5dfa0da58895f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QKGPEv4u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/62ad5zi1wi9ofad4cobh.png)

要获得一些有用的提示和比你在别处能找到的更好的方法，请继续阅读。

### 装置

Hydejack 网站给出了几个安装选项。

我对 Ruby/gems 了解不多，所以没有走那条路线。走 zip 文件的路线听起来很混乱，我已经知道混淆了我的 git 克隆和我的 git 获取。

剩下的就是通过 [starter kit](https://github.com/qwtel/hydejack-starter-kit) 进行安装，它在自述文件中为您提供了一个不错的快速入门指南。

我选择从建立我的 GitHub pages 站点开始，只是因为这听起来最简单，但是我必须改变一些步骤:

1.  拨叉液压千斤顶[启动器 repo](https://github.com/qwtel/hydejack-starter-kit) 。
2.  转到新存储库中的设置选项卡。重命名存储库。
3.  仍然在设置中，向下滚动到 GitHub 页面。
4.  将信号源设置为`master branch`
5.  复制由此生成的 URL。
6.  回到代码(还在 GitHub)。打开`_config.yml`文件。
7.  取消对`url`行的注释，用新的 GitHub 页面替换 url。
8.  取消对`baseurl`行的注释，并替换为单引号中的存储库名称。
9.  提交更改。
10.  要查看该网站，请访问 GitHub pages 的 URL。

### 在本地运行 Jekyll

现在，在我有自己的网站本地副本之前，我做了所有这些。所以看到博客主题立即被激活和托管是令人满意的，但显然对开发来说并不理想。前进到建立地方发展。

1.  在您的计算机上为您的项目创建一个新目录。
2.  进入你的新目录。
3.  运行`git clone [YOUR_PROJECT_REPO]`(注:克隆前不用文件夹里的`git init`，让我很惊讶)
4.  `cd`到根目录(这是你的`_config.yml`文件存储的地方。
5.  运行`bundle install`(注意:这需要捆绑器。如果还没有，运行`gem install bundler`
6.  运行`bundle exec jekyll serve`
7.  要访问该网站，请访问 localhost:4000
8.  要开始开发，请在您选择的代码编辑器中打开这个项目目录

现在，关于本地站点的一个注意事项:尽管 jekyll 服务器在您对代码库进行更改时会继续运行，但我不得不停止并重启服务器以使更改生效。我需要做更多的研究，看看是否有办法让 jekyll 服务器监视变化并相应地更新。

### 深入开发

我浏览了 HydeJack [配置](https://hydejack.com/docs/config/)页面，开始定制我的网站，它很棒。

但是，我开始注意到我的 GitHub repo 中有一些文件和文件夹不在我的本地副本中。怎么回事？

嗯，`.gitignore`文件包含了`_site`文件夹。你知道，它包含了几乎整个网站。

[![_site ignore](img/21818ac6a383f4a31f05b859ddb58028.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6sTvH-1I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3gove9ma6z7fdhqb72nz.png)

我把那一行注释掉了，这样我就可以进入那几页的内容了。

## * ~ *更新~*~

所以伙计们，`_site`文件是在运行 Jekyll 服务器时构建的。如果你试图在里面放任何东西，当你构建的时候它会被删除。所以，保持你的`.gitignore`。

### 资源和学习

在创办这个网站之前，我对 Jekyll 一无所知。老实说，没有太多关于它的资源。我找到的很多视频和文章都是 4、5 年前的，让我觉得 Jekyll 有点过时了？

无论如何，我喜欢这个视频的介绍:

[https://www.youtube.com/embed/iWowJBRMtpc](https://www.youtube.com/embed/iWowJBRMtpc)

就像 HydeJack 可以免费使用一样，还有很多其他的 Jekyll 主题！

前往[此链接](https://jekyllthemes.io/free)浏览一系列免费和付费选项。

最后，关于 Jekyll 博客的精简介绍，请查看这篇 dev 帖子:

[![jaybeekeeper image](img/157abbf15db9a7d6994403aeca55a4de.png)](/jaybeekeeper) [## Jekyll 入门

### jarret Bryan 4 分钟读数

#codenewbie #jekyll #githubpages #ruby](/jaybeekeeper/getting-started-with-jekyll-3nf9)