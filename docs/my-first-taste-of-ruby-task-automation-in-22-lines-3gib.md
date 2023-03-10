# 我对 Ruby 任务自动化的第一次体验是在 22 行代码中

> 原文：<https://dev.to/maxwell_dev/my-first-taste-of-ruby-task-automation-in-22-lines-3gib>

了解我的代码的少数人知道我主要关注前端:HTML、CSS、JavaScript(可悲的是)、可访问性，以及其他困扰我同事的东西。事实上，每次我使用像 Ruby 这样的后端语言，都是直接或间接地因为我的工作，而不是我的选择。

所有这些使得昨天的一个小时显得与众不同。这是我第一次真正的、冲动的灵感来用 Ruby 解决一个问题。这甚至是一个自动化问题，这是我从未在更有限的工具(如 Gulp)之外做过的事情。

这是一个相对较小的胜利，但它有助于缓解我常见的职业焦虑。所以我觉得有必要分享一下我是如何解决这个问题的，以及为什么这个问题如此重要。

## 问题

我不会讲太多背景知识，但基本问题是:

*   我有几个关于前台的减价文件，还有一个长长的 YAML 文件，里面有类似的数据。
*   我想把 YAML 的每一件商品都转换成一个带有正确名称和数据的减价文件。这样，它们可以按照时间顺序一起循环，用于某个站点生成器。

我的第一个想法是手工制作所有的 markdown 文件，对它们进行重命名，复制所有的数据，重复公共代码，根据需要调整数据，等等。我的第二个想法是“这不正是编程应该避免的吗？”

我记得有人在工作中说过 Ruby 可以处理基本的文件操作和创建。我意识到这是检验它的绝佳机会，更重要的是，看看我是否能理解它。

## 自动化必须做什么

为了便于理解，YAML 的文件是这样的:

```
- articles:
  - name: You Can Be a Casual Blogger
    date: "2018-11-05"
    link: https://dev.to/maxwell_dev/you-can-be-a-casual-blogger-ff0
    description: Seeing so many professional writers emerge on Dev.To has helped me see, and  accept, my casual blogger habits.
  ...etc 
```

Enter fullscreen mode Exit fullscreen mode

Ruby 脚本必须做几件事:

1)导入 YAML 数据
2)循环通过 YAML 数据
3)根据数据创建一个文件名
4)为文件的前端创建一个多行字符串
5)将其保存到一个新文件中

最终结果将是这样的降价文件:

```
--------
title: "You Can Be a Casual Blogger"
date: "2018-11-05"
external: true
link: "https://dev.to/maxwell_dev/you-can-be-a-casual-blogger-ff0"
excerpt: "Seeing so many professional writers emerge on Dev.To has helped me see, and  accept, my casual blogger habits."
-------- 
```

Enter fullscreen mode Exit fullscreen mode

他们也有名字和他们的日期和头衔，就像这样:

```
2018-11-05-you-can-be-a-casual-blogger.md 
```

Enter fullscreen mode Exit fullscreen mode

我以为会遇到一些障碍而放弃。原来，只花了 35 分钟，写了 22 行！

## 达成解决方案

### 1)导入 YAML 数据

原来 Ruby 里面有一个 YAML 模块，不需要安装 gems。小小的搜索告诉我如何快速使用它。

```
require 'yaml'
data = YAML.load_file('articles.yml'); 
```

Enter fullscreen mode Exit fullscreen mode

### 2)循环通过 YAML 数据

由于格式的原因，循环遍历上述数据很棘手，这可能是我以某种方式编写 YAML 的错误。导入的数据基本上是一个数组，在一个对象中，在另一个数组中。这部电影接近 YAML 电影，需要一个莱昂纳多·迪卡普里奥的客串。

解决这个问题并不需要陷入困境，只需要一些尝试和错误。

```
data.each do |articles|
  articles['articles'].each do |article|

    # Code goes here!

  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 3)根据数据创建一个文件名

每个 YAML 条目都需要一个基于日期和标题的特定文件名。这样做需要两步:

*   将每个字符串转换成所需的格式。
    *   标题需要小写，用破折号代替空格。
    *   日期不需要更改。
*   每个字符串都必须插入文件名、扩展名和文件路径中。

两个变量之后，我需要的一切都在变量`file_name`中。

```
titlized_name = article['name'].downcase.gsub!(/\s+/, '-')
file_name = "./../_posts/#{article['date']}-#{titlized_name}.md" 
```

Enter fullscreen mode Exit fullscreen mode

### 4)为文件的前一行创建一个多行字符串

这一个花费了一些额外的搜索，但是我找到了`%Q`，这是一个用内插法创建多行字符串的简写(或者正是我需要的)。

缺点是它破坏了代码的对齐和可读性，您将在最终结果中看到这一点。但我仍然觉得它简单易读，值得一读。

```
front_matter = %Q(---
title: "#{article['name']}"
date: "#{article['date']}"
external: true
link: "#{article['link']}"
excerpt: "#{article['description']}"
--------
) 
```

Enter fullscreen mode Exit fullscreen mode

### 5)将此保存到一个新文件中

事实上，当我开始在谷歌上搜索彻底调查我的选择时，我就已经发现了这一点。Ruby 有一些非常简单的创建、写入和保存文件的工具。我所要做的就是把我编译的所有东西插入到`File`对象中，它就发挥了它的魔力。

```
out_file = File.new(file_name, "w")
out_file.puts(front_matter)
out_file.close 
```

Enter fullscreen mode Exit fullscreen mode

## 解决方案找到了！

下面是最后的 22 行脚本！

```
# convert_data.rb

require 'yaml'
data = YAML.load_file('articles.yml');

data.each do |articles|
  articles['articles'].each do |article|
    titlized_name = article['name'].downcase.gsub!(/\s+/, '-')
    file_name = "./../_posts/#{article['date']}-#{titlized_name}.md"

    front_matter = %Q(---
title: "#{article['name']}"
date: "#{article['date']}"
external: true
link: "#{article['link']}"
excerpt: "#{article['description']}"
--------
)

    out_file = File.new(file_name, "w")
    out_file.puts(front_matter)
    out_file.close
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我救了它，屏住呼吸，跑了`ruby convert_data.rb`。一秒钟后，我的`_posts`目录已经有了所有需要的文件，正确命名，一切就绪。

考虑到这要花多长时间(以及可以改进的地方)，总体来说我并没有节省多少时间。但是写这篇文章更有趣，也不那么令人麻木，所以从生产力的角度来说，这是一个胜利！

## 充满希望的感觉

有些人可能想知道为什么我对一个相对简单的 Ruby 脚本如此满意。庆祝小胜利是我的部分原则。这也与我作为一名开发人员有一段时间的不安全感有关。

这种不安全感在我和另一位开发人员共同举办的职业日活动中得到了很好的体现。我们有很多不同的专业——我从事用户界面和可访问性，他从事任务自动化和数据处理。

虽然我们都很受学生欢迎，但他的节目类型似乎更能引起共鸣。自动化往往是编程技能中最受重视的(正如他告诉孩子们的，报酬要高得多)。几乎所有自动化的更高重要性总是让我对自己的前端关注没有安全感。我喜欢前端和一切，但像职业日这样的日子仍然让我对职业生涯的未来感到焦虑。

这个 Ruby 脚本是我第一次真正的尝试，尽管它很小，但却实现了严肃的任务自动化。它不是从其他地方复制来的，或者是有限的前端资产自动化。我不得不自己写，节省了时间和精力，否则很多人会浪费这些时间和精力。看到自己做了这些，职业不安全感稍微减轻了一点。

最重要的是，这表明我可以在这里继续学习我的技能。我可以做前端工作，同时根据需要自动处理无聊的事情。任务自动化让我感觉自己更像一个真正的程序员——高效、懒惰、务实，而且非常自鸣得意。

它能帮我粉碎职业不安全感，一次一个小剧本。