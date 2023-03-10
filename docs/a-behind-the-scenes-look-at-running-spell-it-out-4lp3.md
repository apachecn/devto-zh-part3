# 一个幕后看跑步拼出来

> 原文：<https://dev.to/michael/a-behind-the-scenes-look-at-running-spell-it-out-4lp3>

10 个月多一点的时间里，我一直在运营一个名为[拼出科技行业中使用和发现的缩略语的网站](https://www.spellitout.xyz)。

拼写出它目前的版本只不过是一个静态的网站。这意味着一个名为 Jekyll 的静态站点生成器被用来将所有东西放在一起，然后托管在一个服务器上。那个服务器是 Netlify。

在我管理这个项目的 10 个月里，我创建了各种脚本来处理我工作流程的不同部分。在我进入我的工作流程之前，分享一下拼写的不同部分会有所帮助。

## 现场

如前所述,“拼出来”是使用名为 Jekyll 的静态站点生成器生成的。我之所以选择使用 Jekyll，是因为我想快速获得网站的想法，并为网站托管。对我来说，真正重要的事情是快速捕捉我几乎每天都在收集的所有缩写词。

Jekyll 项目的设置使得每个条目都作为“post”添加到 Jekyll 项目结构中。

每当我遇到一个新的首字母缩写词，我所做的就是向我在 BitBucket 上托管的 Git [repo](https://www.spellitout.xyz/repo-repository/) 添加一个新条目，它就会被添加到网站上。这是自动发生的，因为我使用 Netlify 来托管站点。Netlify 将监视新的提交。当进行新的提交时，Netlify 会启动一个编译我的 Jekyll 站点的构建过程。一旦编译完成，它会将其同步到他们的 CDN 上，并在网上提供给所有人使用。

我也使用 Algolia，一个提供搜索功能的第三方服务。在写这篇文章的时候，我正在把 530 多个缩写拼出来。这意味着搜索可以快速查看我的网站上的首字母缩略词是否拼写正确。

## 工作流程

### 给站点添加新的首字母缩写

在我的项目文件夹中，有一组用于运行各种任务的 JavaScript 文件。其中之一是在我的 Jekyll 项目结构中创建新条目。

```
node scripts/new.js "SIO" "Spell It Out" 
```

Enter fullscreen mode Exit fullscreen mode

新脚本有两个参数，第一个是缩写词本身，第二个是缩写词的拼写版本。这产生的是一个与此有关的降价文件。

```
---
layout: post
title:  "SIO"
spelt_out: "Spell It Out"
date:   2018-10-11 23:00:00
--- 
```

Enter fullscreen mode Exit fullscreen mode

我创建这个文件的原因是，Jekyll 默认不提供任何命令行选项来为您生成 post 文件。此外，我有非常具体的前面的事情，用于该项目，所以用这个脚本，我可以调整文件生成，以满足我的需要。

从那里，我将通过 Git 提交，然后启动 Netlify 工作流来构建和推送对我的站点的更新。

### 与 Algolia 同步最新指数

一旦添加了新的缩写，Algolia 就需要更新。这是使用一个名为 [Algolia Atomic](https://github.com/chrisdmacrae/atomic-algolia) 的节点包完成的。这个包只需要一个 [JSON](https://www.spellitout.xyz/json-javascript-object-notation/) 文件，其中包含索引和更新搜索索引的所有缩写。

为了生成所需的 JSON 文件，我的项目文件夹中有一个文件，它遍历所有的帖子并构造一个 JSON 文件。代码看起来像:

```
---
---
{% if jekyll.environment == "development" %}
{% assign sorted = site.posts | sort: 'date' | reverse %}
[
{% for post in sorted %}
  {
    "title": "{{ post.title }}",
    "url": "{{ post.url }}",
    "spelt_out": "{{ post.spelt_out }}",
    "objectID": "{{ post.path }}"
  }{% unless forloop.last %},{% endunless %}
{% endfor %}
]
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

我只希望这个 JSON 文件在开发环境中生成，因为我不在乎在我的站点上共享这个 JSON。由于这个原因，文件输出任何有用信息的唯一方法是使用 Jekyll 命令:

```
JEKYLL_ENV=development jekyll build 
```

Enter fullscreen mode Exit fullscreen mode

从那里，我运行我的原子 algolia 脚本，我的 Algolia 帐户将更新与最新的缩写。

### 生成社交图像

除了更新 Algolia，生成的 JSON 文件还用于生成社交媒体图像。专门针对 Twitter 的。

我有一个自定义的 HTML“主题”设置，PhantomJS 使用它来生成供 Twitter 使用的图像。JSON 文件用于确定哪些首字母缩略词已经生成到图像中，并且只生成新的首字母缩略词。所有的图像都放在一个文件夹中，然后用来与 [AWS](https://www.spellitout.xyz/aws-amazon-web-services/) 同步。

### 同步社交媒体图片

社交媒体图片生成后，我使用 AWS 的 [CLI](https://www.spellitout.xyz/cli-command-line-interface/) 来同步图片文件夹。这很好，因为它像 rsync 一样，只上传新的图像文件。

```
aws s3 sync images/ s3://bucket-name 
```

Enter fullscreen mode Exit fullscreen mode

### 推文首字母缩写

虽然我希望我能多花点时间来自动化这一点，但每天我都会尝试运行一个脚本，在拼读 Twitter 帐户[@ spelli Out _ XYZ](https://twitter.com/spellitout_xyz)上发一个拼读的首字母缩写词。

该脚本所做的是遍历生成的 JSON 并选择一个随机的缩写词。有了 JSON 文件中的数据，它就可以从 S3 检索相关的图像。检索到图像后，图像被上传到 Twitter，然后生成一条类似如下的推文:

[![](img/b6614033be805d9cf8e1214f32a95403.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ae0qsSnR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/i.michaelsoolee.com/20181011-spell-it-out-tweet.png)

## 把它放在一起

虽然管理我的项目只需要几分钟就可以协调我的各种工作流程中的所有步骤，但这已经变得很乏味了。看到一旦首字母缩略词被添加到 repo 中，接下来的所有步骤都是一样的，我编写了一个 bash 脚本来为我运行一切。

```
#!/bin/bash

JEKYLL_ENV='development' jekyll build

cd scripts

node algolia.js

node social.js

aws s3 sync images/ s3://bucket-name

cd .. 
```

Enter fullscreen mode Exit fullscreen mode

虽然不是全自动的，但是编写这个 bash 脚本已经节省了我几分钟的工作流程，因为它自动化了我上面描述的所有工作流程。此外，运行一个命令，看到一切在你面前发生，感觉很神奇。

* * *

最初[发布在 michaelsoolee.com](https://michaelsoolee.com/behind-the-scenes-spell-it-out/)上。

感谢您花时间阅读这篇文章！我很想保持联系，并通过我的时事通讯给你发送编程和设计的技巧，在家工作和制作副业。[点击这里报名](http://eepurl.com/bGXerj)。