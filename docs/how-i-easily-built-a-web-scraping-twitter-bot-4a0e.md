# 我如何建立一个网络抓取推特机器人

> 原文：<https://dev.to/lucysuddenly/how-i-easily-built-a-web-scraping-twitter-bot-4a0e>

[![twitter logo inside a gear](img/f20e44cdbc7341343deaf385fc075b8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q8mBCQBW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ykamh5rf8ukqajqqpaq.png)

好吧，完全披露:我认为建立一个 twitter 机器人将是一个很大的工作量。原来真的很简单！首先我将分解如何使用 Chatterbot Ruby 宝石，然后继续我如何使用 Nokogiri。

## 第一步:安装并配置 Chatterbot

这里是聊天机器人指南网页

首先，创建一个新的 Twitter 账户！很简单。

其次，使用 twitter 帐户申请一个开发者帐户[这里是](https://developer.twitter.com/en/apply/user)。

在等待批准的时候(应该需要一两个小时)，通过在终端中运行`gem install chatterbot`，在项目文件夹中安装 chatterbot ruby gem。然后，也许开始你的机器人逻辑(更多关于这一点)。

要运行设置脚本，只需创建一个新的 Ruby 文件，其标题是您将用于 bot 的 twitter 帐户的用户名。填充以下内容开始:

```
require 'rubygems'
require 'chatterbot/dsl'

tweet "Hello World!" 
```

从终端运行新的 Ruby 文件，安装脚本就会运行——它会提示您输入 api 密钥和秘密，这是成功的 twitter dev 帐户应用程序会提供给您的。请务必从您的批准中复制您的确认 pin，并将其粘贴到设置脚本的正确步骤中。

就是这样！将`"Hello World!"`替换成你想让你的机器人如何发推特的任何逻辑！还有其他的搜索、转发和回复选项——查看 chatterbot 指南，了解详细的选项概要。

对于我的机器人，我想抓取一组引用网页:下面是我如何做的。

## 第二步:安装 Nokogiri

运行`gem install nokogiri`将 Nokogiri 安装到您的项目文件夹中。

在 Ruby 文件的头中包含以下内容:

```
require 'Nokogiri'
require 'open-uri' 
```

我设置了一个等于 URLS 变量的 URL 字符串数组——下面是我如何使用 Nokogiri 从 URL 数组中抓取一个随机选择:

```
doc = Nokogiri::HTML(open(URLS[rand(0..URLS.length - 1)])) 
```

`Nokogiri::HTML`访问 Nokogiri HTML 解析方法，`open`使用 open-url 打开网页，`URLS`是我的 url 数组，`[rand(0..URLS.length - 1)]`是 url 数组中某处的随机索引号。

出于我的目的，我希望页面上的所有 p 标签都不具有特定的性质——所以我告诉 Nokogiri 我希望使用`.css`命令，并在我非常长的 CSS 语句中添加了:

```
content = []

doc.css('p:not(.title):not(.toc):not(.index):not(.indentb):not(.quoteb):not(.information):not(.fst):not(.footer):not(.pagenote):not(.quote)').each do |node|
    content << node.text
  end 
```

我在每个 p 节点的文本中加入了标题、索引、引用等等。到内容数组中。

接下来，我使用正则表达式将每个段落分成句子，同时通过将正则表达式括在括号中来保留标点符号。

```
new_content = []

  content.each do |c|
    d = c.split(/([\?\!\;\.])/)
    d.each do |e|
      new_content << e
    end
  end 
```

接下来，我将为这个巨大的数组设置一个随机索引，以随机选择一个引用。

```
 #set random index
  random = rand(0..new_content.length - 6)

  #ensure first element chosen is not punctuation
  while new_content[random].length < 4
      random = rand(0..new_content.length - 6)
  end 
```

接下来实例化 tweet 内容字符串并进行一些格式化:

```
 #concatenate elements after random chosen index
  tweet_content = new_content[random] + new_content[random + 1] + new_content[random + 2] + new_content[random + 3] + new_content[random + 4] + new_content[random + 5]

  #formatting
  tweet_content = tweet_content.gsub("\r\n", " ")
  tweet_content = tweet_content.gsub("\n", " ")
  tweet_content = tweet_content.gsub("   ", " ")
  tweet_content = tweet_content.gsub("i.e.", "that is") 
```

接下来，我们要检查以确保选择的句子符合 tweet 字符限制:

```
 #split again to begin checking length of sentences
  tweet_content = tweet_content.split(/([\?\!\;\.])/)

  #decide on complete sentences that are under the character limit
  if tweet_content[3]
    if (tweet_content[0].length + tweet_content[1].length + tweet_content[2].length + tweet_content[3].length) < 240
      tweet_content = tweet_content[0..3].join
    else
      tweet_content = tweet_content[0..1].join
    end
  else
    tweet_content = tweet_content[0..1].join
  end 
```

## 第三步:庆祝！

tweet_content 现在可以返回到我们的 tweet 函数中了！我们可以写`tweet tweet_content`来代替`tweet "Hello World!"`

为了自动化我们的机器人，我们可以将脚本的内容包装在一个 do 循环中，并在循环中使用 sleep 方法来设置我们的 tweets 的间隔。

就是这样！一个机器人的快速设置和你为机器人选择的任何逻辑。如果你决定做一个 bot，并且有一些有趣的逻辑可以分享，那就放在评论里吧！