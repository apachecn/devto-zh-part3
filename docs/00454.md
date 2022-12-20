# 十一件必需品

> 原文：<https://dev.to/andeersg/eleventy-essentials-14lh>

最近我把我的个人网站换到了十一月。这是一个非常好的静态站点生成器。以下是我发现的一些创建网站的技巧和窍门。

## 开始使用 _data 文件夹

如果你来自 Jekyll，你会习惯于模板中不同种类的变量(`site.time`、`site.posts`)。
Eleventy 默认没有这些，但是添加它们(以及更多)非常容易。

`_data`文件夹中的每个 JSON/JS 文件将在模板的文件名命名空间下可用。所以`_data/meta.json`变成了`meta.some_key_inside_file`。

### eleventy.js

我创建的第一个文件包含与引擎和环境相关的内容。

```
module.exports = {
  production: process.env.ELEVENTY_ENV === 'production',
  development: process.env.ELEVENTY_ENV === 'development',
  env: process.env.ELEVENTY_ENV || 'development',
  notReady: false, // Flag to prevent something from rendering.
  time: () => {
    return new Date(); // Simplest example, could use Moment.js to return another format.
  },
}; 
```

这使我能够访问关于环境的信息，所以如果它不是生产，我可以排除 Google Analytics，或者在开发中打印更多信息。

### 元数据. json

```
{  "title":  "Andeers.com",  "description":  "Personal site of Anders Grendstadbakk",  "url":  "https://andeers.com/",  "feed":  {  "subtitle":  "I mainly write about web development and tech.",  "filename":  "feed.xml",  "path":  "/feed/feed.xml",  "url":  "https://andeers.com/feed/feed.xml",  "id":  "https://andeers.com/"  },  "author":  {  "name":  "",  "email":  ""  }  } 
```

metadata.json 包含关于这个站点和我的信息。我也有一些 RSS 提要字段。将所有这些信息收集在一个文件中是很好的，这样当你进行更改时，就可以将它们都放在一起。

## [T1】eleventy . js](#eleventyjs)

这个文件是您定义集合、过滤器和短代码的地方。

一个热门提示是，由于这是 node.js，您可以在这里`require()`您的数据文件并访问变量。这样你可以在帮助函数中使用来自`metadata`的内容，或者在开发中输出不同的内容。

```
const eleventyVars = require('./_data/eleventy'); 
```

### 创建一个基于文件夹的文件集合

```
eleventyConfig.addCollection('posts', function(collection) {
  return collection.getFilteredByGlob('./_posts/*').reverse().filter(function(item) {
    // Only include items with a permalink.
    return !!item.data.permalink;
  });
}); 
```

这为您提供了一个`_posts`文件夹中所有项目的集合。

### 最新帖子

```
eleventyConfig.addCollection('latestPosts', function(collection) {
  const posts = collection.getSortedByDate().reverse();

  let items = [];
  for( let item of posts ) {
    if(!!item.inputPath.match(/\/_posts\//)) {
      items.push( item );
      if( items.length >= 5 ) {
        return items;
      }
    }
  }
}); 
```

这个片段为您提供了一个变量，其中包含您可以在首页上使用的 5 篇最新文章。

### 日期功能

```
eleventyConfig.addFilter('readableDate', dateObj => {
  return DateTime.fromJSDate(dateObj).toLocaleString(DateTime.DATE_FULL);
});

// Used in Handlebars like this: {{readableDate post.date }} 
```

这个函数以特定的方式呈现日期，但是它可以很容易地接受另一个指定格式的参数，或者使用 Moment.js 代替 Luxon。

### 元函数

```
eleventyConfig.addFilter('pageTitle', title => {
  if (title && title !== '') {
    return `${title} | ${metadata.title}`;
  }
  return metadata.title;
});

eleventyConfig.addFilter('pageDescription', (description, opt) => {
  const {content, excerpt } = opt.data.root;

  if (description && description !== '') {
    return description;
  }
  else if (excerpt && excerpt !== '') {
    return excerpt;
  }
  else if (content && content !== '' && !opt.data.root.isFront) {
    return snippetGenerator(content);
  }

  return metadata.description;
}); 
```

我有两个函数用于``和`<description>`。第一个代码呈现附加了站点名称的页面标题，如果页面标题不可用，它只返回站点名称。

pageDescription 在多个地方检查一个描述，首先，如果一个被特别定义，然后它尝试从页面的内容生成一个。如果这不起作用，就回到一般的站点描述。

## 其他东西

在根 index.html 中，我给首页添加了一个`isFront: true`，这使得在渲染首页时可以做一些有条件的事情:

```
<section>
  {{#if isFront}}
    <p>This paragraph is only written to the front page.</p>
  {{/if}}
  <p>This paragraph is rendered on all pages.</p>
</section> 
```

由于我的网站还在建设中，我可能会找到其他有用的片段来让 Eleventy 变得更好，所以请在 Github 上查看这个网站。