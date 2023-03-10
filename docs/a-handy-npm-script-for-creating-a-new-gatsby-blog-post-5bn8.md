# 一个创建新的盖茨比博客帖子的方便的 npm 脚本

> 原文：<https://dev.to/jhooks/a-handy-npm-script-for-creating-a-new-gatsby-blog-post-5bn8>

[我的博客](https://joelhooks.com)是用 Gatsby 构建的，文章内容使用 MDX。这是一个很棒的设置，到目前为止，我非常喜欢使用它。

我在旧的 Octopress/Jekyll 配置中错过的一件事是运行 rake 任务来创建一个新帖子的能力，其中填充了所有基本的必需 frontmatter 元数据。

```
---
slug: a-handy-npm-script-for-creating-a-new-gatsby-blog-post~M3MyBb6Fd
guid: M3MyBb6Fd
date: 2019-01-30
title: 'A  handy  npm  script  for  creating  a  new  Gatsby  blog  post'
published: false
--- 
```

这是这篇文章的降价元的一个例子。Gatsby 在构建网站时使用这个 frontmatter 来创建相应的页面。

每次都要手动输入，这有点让人头疼，所以我决定通过代码的力量来解决我的挫败感。

```
$ npm run newPost "This is the title of my blogpost" 
```

这是我希望能够从终端命令行运行的命令的基本结构。一旦执行了这个操作，就应该创建一个新的文件夹并正确命名。文件夹里会有一个`index.mdx`文件，里面有所有基本/必需的 frontmatter，我需要做的就是跳进去写这篇文章。

```
/content
  - blog
    -- 2019-01-30-a-handy-npm-script-for-creating-a-new-gatsby-blog-post
      --- index.mdx 
```

这是运行脚本后我的设置的结果。

因此，让我们先看看需要发生什么，来看看我是如何构建它的。

1.  执行带有参数的节点脚本
2.  解析参数
3.  提取标题
4.  “slugify”文件名和 url 中使用的标题
5.  捕获当前日期
6.  将文件写入磁盘

我做了一个名为`scripts`的子文件夹，在里面创建了`newPost.js`。

为了获得命令行参数，我们需要访问 node 中的`process.argv`。`newPost.js`的内容是这样的:

```
console.log(process.argv) 
```

现在我们可以通过运行下面的命令来看看`process.argv`包含了什么:

```
node ./scripts/newPost.js "this is my test post" 
```

假设没有错误，输出是一个如下所示的数组:

```
;[
  '/Users/joel/.nodenv/versions/10.6.0/bin/node',
  '/Users/joel/Code/joelhooks-com/test.js',
  'this is my test post',
] 
```

`process.argv`的内容是一个数组，包含正在使用的可执行节点的位置、正在执行的脚本的位置，以及我们传入的参数。

我们可以用更多的参数再试一次:

```
node ./scripts/newPost.js "this is my test post" 1 "gopher" 
```

你会看到它只是简单地添加到数组:

```
;[
  '/Users/joel/.nodenv/versions/10.6.0/bin/node',
  '/Users/joel/Code/joelhooks-com/test.js',
  'this is my test post',
  '1',
  'gopher',
] 
```

我们希望确保我们实际上有一个名称可以使用，所以我将在我的脚本中使用一个简单的 if/else 保护来进行检查和确认。

```
const title = process.argv[2]

if (!title) {
  throw 'a title is required!'
} 
```

现在试着不带名称参数运行它。它应该抛出一个错误并崩溃。

现在我们有了一个名字，我们想创造一个“烤肉串案件”蛞蝓。Slugs 在所有情况下都可能有点棘手，因此为此我将使用一个小型库。`npm i slug`将得到我所需要的:

```
const slugify = require('slug')

const title = process.argv[2]

if (!title) {
  throw 'a title is required!'
}

console.log(slugify(title)) 
```

这将输出`This-is-the-title-of-my-blogpost`，它很接近，但是通常一个 slug 将全部小写，所以它看起来更像这样:

```
const slugify = require('slug')

const title = process.argv[2]

if (!title) {
  throw 'a title is required!'
}

const slug = slugify(title.toLowerCase()) 
```

出于排序的目的，我也喜欢在文章的文件夹名中添加日期。我的 goto 是一个很小的`date-fns`库，它在一个很小的包中提供了你所需要的大部分日期/时间工具，并使用它:

```
const slugify = require('slug')
const dateFns = require('date-fns')

const title = process.argv[2]

if (!title) {
  throw 'a title is required!'
}

const slug = slugify(title.toLowerCase())
const date = dateFns.format(new Date(), 'YYYY-MM-DD') 
```

您可以手动提取和格式化日期，但是...谁有时间做那个？？这很好，我有了组装和输出文件所需的所有部件。

首先我要创建路径:

```
const slugify = require('slug')
const dateFns = require('date-fns')

const title = process.argv[2]

if (!title) {
  throw 'a title is required!'
}

const slug = slugify(title.toLowerCase())
const date = dateFns.format(new Date(), 'YYYY-MM-DD')
const dir = `./content/blog/${date}-${slug}` 
```

现在我可以使用节点`fs`或者文件系统模块来创建文件夹:

```
const fs = require('fs')
const slugify = require('slug')
const dateFns = require('date-fns')

const title = process.argv[2]

if (!title) {
  throw 'a title is required!'
}

const slug = slugify(title.toLowerCase())
const date = dateFns.format(new Date(), 'YYYY-MM-DD')
const dir = `./content/blog/${date}-${slug}`

if (!fs.existsSync(dir)) {
  fs.mkdirSync(dir)
} else {
  throw 'That post already exists!'
} 
```

需要使用 require“导入”`fs`模块，然后我还在对`fs.mkdirSync(dir)`的调用周围添加了一个 if/else，以确保我不会覆盖现有的 post。安全总比后悔好。运行之后，您会看到创建了一个空文件夹。现在我们可以使用`fs.writeFileSync`来创建实际的文件本身:

```
const fs = require('fs')
const slugify = require('slug')
const dateFns = require('date-fns')

const title = process.argv[2]

if (!title) {
  throw 'a title is required!'
}

const slug = slugify(title.toLowerCase())
const date = dateFns.format(new Date(), 'YYYY-MM-DD')
const dir = `./content/blog/${date}-${slug}`

if (!fs.existsSync(dir)) {
  fs.mkdirSync(dir)
} else {
  throw 'That post already exists!'
}

fs.writeFileSync(
  `${dir}/index.mdx`,
  `---
slug: ${slug} date: ${date} title: "${title}"
published: false
---`,
  function(err) {
    if (err) {
      return console.log(err)
    }

    console.log(`${title} was created!`)
  },
) 
```

`fs.writeFileSync`需要三个参数。第一个是路径或目的地，第二个是文件内容。由于这是现代节点，我们可以使用反勾号访问字符串模板文字。这特别好，因为它们允许我们创建相对干净的格式化字符串，尊重空白，不需要特殊的换行符。

最后一个参数是操作完成时调用的回调函数。如果有错误，它会被记录下来，如果成功，我们还会收到一条友好的消息。

这样，您现在就有了一个方便的脚本来为您的 Gatsby 应用程序创建新的 markdown blogposts。

因为这完全是我个人使用的，所以我没有花任何时间让命令行参数 handline 变得健壮。这是一个为单个用户提供非常具体的单个任务的脚本。这意味着它可能有点危险，不会有任何负面影响。写这篇文章花了大约 10 分钟的时间，这将为我将来省去许多无谓的打字时间。

更重要的是，它消除了我写博客经历中的一点摩擦/痛苦，这意味着我可能会做得更多🙂