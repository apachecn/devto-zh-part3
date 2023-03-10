# 如何用 Yarrrgs 编写博客发布链接 CLI！

> 原文：<https://dev.to/aexol/how-to-write-a-blog-publish-link-cli-with-yarrrgs-244c>

## 简介

你好，今天我将写一点关于工具的内容。两周前，我想开这个博客。我问我写博客的朋友我应该用什么博客引擎。他的回答相当令人惊讶，因为他告诉我，我应该使用静态网站，并提交博客帖子作为对它的拉请求。

## 寻找完美的解决方案

所以，我开始在互联网上寻找最好的博客布局(reactjs)工具来写我的静态博客。我能够从 GitHub 现在充斥的数百个中文回复中找出`gatsby starter blog`。在我创建这个博客的时候，我还没有盖茨比生态系统的经验，但它对我来说很有前途。

## 跑步

运行这种 Gatsby 包只需要我安装`gatsby-cli`并运行命令`gatsby develop`。很容易吗？啊？

## 修改

我决定对这个简单的博客包做一些调整，因为它是一个只有一个作者的真正纯粹的博客。所以要添加其他作者(我还不知道是谁:( )我添加了 authors 文件夹，所以要将你添加为作者，你需要创建一个以你的名字命名的文件夹，并创建包含这种内容的`index.js`文件:

```
export const Artur = {
  photo: require('./Artur.jpeg'),
  desc:
    'GraphQL passionate. Code generation guru. Short code lover. Father. CTO. CEO.',
  name: 'Artur Czemiel',
  email: 'aexol@aexol.com',
} 
```

Enter fullscreen mode Exit fullscreen mode

并将这一行添加到`authors/index.js` :

```
import { Artur } from './Artur'
export const Authors = {
  Artur,
} 
```

Enter fullscreen mode Exit fullscreen mode

稍后，你可以在你的博客文章中使用它。

## 如何添加您的博客文章

尽管添加你的博客文章非常简单。同样，你必须在 pages 文件夹中创建一个类似 blog post slug 的文件夹`my-very-interesting-article`。添加一个带有这种头文件的`index.md`文件，该头文件由`graymatter`包格式化，然后:

```
--------
title: My very interesting article
date: '2018-10-27T13:23:04.284Z'
author: Artur
-------- 
```

Enter fullscreen mode Exit fullscreen mode

就这样。写完文章后，您只需向 fork 提交 pull 请求。我合并拉取请求，并将你的文章发布到网站上。

## 发布工具

有时候我有点懒。我在这个项目中添加了一个小的 publish CLI，它会自动使用`opn`打开浏览器，并带有预填充的 URL 和标题字段:`reddit` `LinkedIn` `twitter` `hackernews`。所以从这个博客分享你的博客文章要容易得多。它位于这个博客的`bin/index.js`文件夹中，使用我之前提到的`yargs`和`inquirer`和`graymatter`。

下面是代码

```
const yargs = require('yargs')
const fs = require('fs')
const inquirer = require('inquirer')
const opn = require('./opn')
const matter = require('gray-matter')

const HOSTNAME = 'https://blog.graphqleditor.com'
const pagesDirectory = __dirname + '/../src/pages'
const reddits = [
  'typescript',
  'javascript',
  'reactnative',
  'reactjs',
  'reactxp',
  'node',
  'webdev',
  'graphql',
  'programming',
  'technology',
  'startups',
  'learnprogramming',
  'marketing',
  'entrepreneur',
  'golang',
  'ruby',
  'dotnet',
  'java',
  'python'
].sort()
const voats = ['technology']
const mediums = [
  {
    name: 'voat',
    fn: ({ url, title }) =>
      inquirer
        .prompt([
          {
            type: 'list',
            name: 'voat',
            message: 'What voat you would like to publish to?',
            choices: voats,
          },
        ])
        .then(answers =>
          submit({
            medium: 'voat',
            reddit: answers.voat,
            title,
            url,
          })
        ),
  },
  {
    name: 'reddit',
    fn: ({ url, title }) =>
      inquirer
        .prompt([
          {
            type: 'list',
            name: 'reddit',
            message: 'What reddit you would like to publish to?',
            choices: reddits,
          },
        ])
        .then(answers =>
          submit({
            medium: 'reddit',
            reddit: answers.reddit,
            title,
            url,
          })
        ),
  },
  {
    name: 'hackerNews',
    fn: ({ url, title }) =>
      submit({
        medium: 'hackerNews',
        title,
        url,
      }),
  },
  {
    name: 'linkedIn',
    fn: ({ url, title }) =>
      submit({
        medium: 'linkedIn',
        title,
        url,
      }),
  },
  {
    name: 'twitter',
    fn: ({ url, title }) =>
      submit({
        medium: 'twitter',
        title,
        url,
      }),
  },
]
const submit = ({ medium, title, url, reddit }) =>
  opn(
    {
      voat: `https://voat.co/submit?linkpost=true&title=${title}&url=${url}&subverse=${reddit}`,
      hackerNews: `http://news.ycombinator.com/submitlink?u=${url}&t=${encodeURIComponent(
        title
      )}`,
      reddit: `https://www.reddit.com/r/${reddit}/submit?title=${encodeURIComponent(
        title
      )}&url=${url}`,
      linkedIn: `https://www.linkedin.com/shareArticle?mini=true&url=${url}&source=${encodeURIComponent(
        HOSTNAME
      )}`,
      twitter: `http://twitter.com/share?url=${url}&text=${encodeURIComponent(
        title
      )}`,
    }[medium],
    {
      wait: false,
    }
  )

const argv = () =>
  yargs
    .command(
      'publish',
      'Publish your blog post for different mediums',
      {},
      async argv => {
        const pageNames = fs
          .readdirSync(pagesDirectory)
          .filter(page =>
            fs.lstatSync(`${pagesDirectory}/${page}`).isDirectory()
          )
        const pages = pageNames.map(page => ({
          url: `${HOSTNAME}/${page}`,
          title: matter(
            fs.readFileSync([pagesDirectory, page, 'index.md'].join('/'))
          ).data.title,
        }))
        inquirer
          .prompt([
            {
              type: 'list',
              name: 'page',
              message: 'Which page would you like to publish',
              choices: pages.map(p => p.title),
            },
          ])
          .then(answers => pages.find(p => p.title === answers.page))
          .then(page =>
            inquirer
              .prompt([
                {
                  type: 'list',
                  name: 'medium',
                  message: 'What medium you would like to publish to?',
                  choices: mediums.map(m => m.name).concat('All mediums'),
                },
              ])
              .then(
                answers =>
                  answers.medium === 'All mediums'
                    ? Promise.all(
                        mediums
                          .filter(m => m.name !== 'reddit' && m.name !== 'voat')
                          .map(m => m.fn(page))
                      )
                    : mediums
                        .find(m => m.name === answers.medium)
                        .fn(page)
                        .then(proc =>
                          inquirer
                            .prompt([
                              {
                                type: 'list',
                                name: 'again',
                                message: 'What next?',
                                choices: ['Exit', 'Next Action'],
                              },
                            ])
                            .then(({ again }) => {
                              if (again === 'Exit') {
                                return
                              }
                              return argv()
                            })
                        )
              )
          )
      }
    )
    .help().argv
argv() 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/43c4d4f02ae1222859829a643f446a2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8N5m-5id--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5tf5mofyn19z80bq9p53.gif)

之后，它会打开一个窗口，这样我就可以在 Reddit 上发帖了。简单又好看！

[![](img/ab0e1b2f0aa792f89c2c5f8e038cc9a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hfj2eNVb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qvzxn3ewmph8dyu3li0t.png)