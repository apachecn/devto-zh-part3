# 初学者使用 NuxtJs 和 Markdown 构建静态博客

> 原文：<https://dev.to/nirebu/building-a-static-blog-with-nuxtjs-and-markdown-as-a-beginner-pkf>

你好，这是我在这里的第一篇文章...或者任何地方，因为我从来没有写过博客！我是 Nicolò Rebughini，一名试图掌握现代 web 开发实践的系统管理员。我在高中时专攻计算机科学，但多年来一直从事视频后期制作技术，一年多前戴着系统管理员的帽子回到 IT 界。现在我正在更新自己，学习使用像 [Ansible](https://ansible.com/) 和 [Docker](https://docker.com/) 这样的工具来标准化和简化我的工作场所。

## 旅程的开始

当 Bootstrap 的第一个版本还没有发布的时候，我就离开了开发和运营世界(维基百科说这发生在 2011 年)，在探索了现在无限的选项并尝试了太多时间之后，我终于成功了:我的 [JAM Stack](https://jamstack.org/) 博客正在成形。你也可以在 [my Github repo](https://github.com/nirebu/nirebu-2019/) 上探索它的源代码。

作为一个完全绿色的网络开发人员，我用我已经知道的工具创建了这个网站，所以我拼凑了一个单独的`index.html`网页，并尝试了一些新的玩具，自从我上次进去从头开始写东西以来，它们已经有了很大的进步。[在 Github 上探索 2018 版的来源](https://github.com/nirebu/nirebu-personal-website/)。

在惊叹于像热重载这样节省大量时间的技术、CSS 框架的最新创新(欢迎[布尔玛](https://bulma.io/))以及在 Firebase 上免费部署这些技术之后，我继续寻找可以扩充它的方法。

## 输入 NuxtJS

由于对所有这些新奇的 Javascript 框架都是全新的，我花费了大量的时间来尝试和试验它们，还没有计算仅仅为了掌握一些晦涩的 JS 概念所花费的时间。

然后...当我发现关于 [NuxtJS](https://nuxtjs.org/) 时，我终于明白了。

为了让这个网站和博客按照我想要的方式运行，我必须做一些事情:静态网站、降价博客帖子和免费托管(这次是在 [Netlify](https://netlify.com/) 上)

## 创建一个 NuxtJS 网站

初始化一个新的 NuxtJS 网站就像运行一个命令
一样简单

```
# Run the create-nuxt-app without having to install it beforehand
npx create-nuxt-app my-markdown-blog 
```

我将`Nuxt mode`设置为`Universal`来获得服务器端渲染 Vue 的奖励。

我想用这个新的(只是在引擎盖下)版本替换我的旧网站，所以我必须带来旧的源代码，在`pages/index.vue`的`<template>`标签中添加`index.html`的内容

## 装载布尔玛

我怎么在这里装载布尔玛？也许我的解决方案不是最干净或最优雅的，但这里是:

```
npm install --save-dev bulma @nuxtjs/style-resources node-sass sass-loader 
```

我还对股票布尔玛框架进行了一些定制，因此，为了让它工作，我复制了`assets/`目录中的`main.scss`，并更改了`nuxt.config.js` :
中的一些东西

```
module.exports = {
  [...]
  css: ["assets/main.scss"] // This line instructs Nuxt to load this file in every page
  [...]
  modules: [
    "@nuxtjs/axios", // This was already present because I chose it during the init
    "@nuxtjs/style-resources" // Necessary to make Nuxt load the SCSS and SASS files
  ]
  [...]
} 
```

在我的`main.scss`中设置了`bulma.sass`文件的正确路径后，所有的样式都会自动修复。耶！成功！(经过数小时的反复试验)

## 重构组件中的网站页眉和页脚

为了利用 Nuxt 固有的模块化，我将 navbar 和 footer 分离成单独的组件，这样我就可以在页面的默认布局中使用它们。

在这之后，我的`layouts/default.vue`文件看起来是这样的:

```
<template>
  <div class="main">
    <NavBar />
    <nuxt />
    <SiteFooter />
  </div>
</template>

<script>
import NavBar from '../components/NavBar';
import SiteFooter from '../components/SiteFooter';

export default {
  components:{
    NavBar,
    SiteFooter
  }
}
</script> 
```

## 增加降价支持

自从我第一次尝试以来，这一直是一个主要的痛点。以下是我设法设置它的方法。如果有人想告诉我“真正”的方法，请做一个公关来纠正这篇文章或我的来源，我很乐意了解它。

一些准备工作...

```
npm install --save-dev frontmatter-markdown-loader @nuxtjs/markdownit 
```

让我们让 Webpack 使用`raw-loader`来读取降价文件，以避免对输出的任何操作。

```
module.exports = {
  [...]
  build: {
    extend(config, ctx) {
      config.module.rules.push({
        test: /\.md$/,
        use: ['raw-loader']
      });
    }
  }
  [...]
} 
```

## 分离降价内容和元数据

在`pages/blog/_post/index.vue`文件中是奇迹发生的地方(至少对我来说)。鉴于`markdownit`不支持读取存储文章信息的 markdown 元数据，我们需要分离获取内容和属性的过程(如 front-matter 所称):

```
<script>
// Let's require the needed modules
const fm = require("front-matter");
var md = require("markdown-it")({
  html: true,
  typographer: true
});

export default {
  async asyncData({ params }) {
    // We read the markdown file by looking at the `post` parameter
    // in the URL and searching for a markdown file with that name in
    // the articles directory
    const fileContent = await import(`~/articles/${params.post}.md`);
    // We process the raw output through front-matter
    // (markdownit was giving me garbled results)
    let res = fm(fileContent.default);
    return {
      // attributes will be an object containing the markdown metadata
      attributes: res.attributes,
      // content will contain the body of the markdown file,
      // rendered in HTML via the `markdownit` class
      content: md.render(res.body)
    };
  }
};
</script> 
```

有了这些数据，我们可以填充一些元素:

```
<template>
  <div :key="$route.params.post">
    <div class="container">
      <div class="columns is-centered">
        <div class="blog column is-10-tablet">
          <div class="title">{{ attributes.title }}</div>
          <div class="subtitle">
            Published on {{attributes.ctime}}
            by {{ attributes.author }}
          </div>
          <div v-html="content" class="blog-content content"></div>
        </div>
      </div>
    </div>
  </div>
</template> 
```

现在，如果您导航到路径`blog/some-markdown-file`，您应该看到显示的内容和属性。

## 为博客文章生成路线

我们离拥有一个由 markdown 支持的静态生成的博客更近了一步，但是，在部署之前，我们必须多做一步。

默认情况下，Nuxt 只生成在`pages/`目录中找到的路由，但是它不生成每个动态页面，只生成索引。所以我们必须找到一种方法让它也能产生这样的路线:

*   `blog/first-post`基于文件`articles/first-post.md`
*   `blog/second-post`基于文件`articles/second-post.md`
*   等等

让我们再一次潜入水中。在顶部，我是这样配置的

```
// glob is a small module to read 'globs', useful to get
// a filtered file list
const glob = require('glob');
// we acquire an array containing the filenames
// in the articles directory
let files = glob.sync( '**/*.md' , { cwd: 'articles' });

// We define a function to trim the '.md' from the filename
// and return the correct path.
// This function will be used later
function getSlugs(post, _) {
  let slug = post.substr(0, post.lastIndexOf('.'));
  return `/blog/${slug}`;
} 
```

然后，编辑`nuxt.config.js`中的`generate`对象，添加通过上一段代码得到的路线:

```
module.exports = {
  [...]
  generate: {
    routes: function() {
      return files.map(getSlugs)
    }
  }
  [...]
}; 
```

## 包装完毕

如果我没有忘记什么，你应该至少有一个 Markdown 博客帖子查看器的相似之处，你应该能够成功地建立所有的页面，并将你的博客部署到你最喜欢的静态网站托管服务。

命令`npm run generate`将获取包含我们新静态生成的网站的`build`目录。

从这个指南，从我的网站，缺少了我将要实现的**至关重要的**部分，比如使用 Vuex 来存储所有的博客文章元数据，并使用这些信息来呈现文章列表。现在我只能写单篇博文，并把它们链接到某个地方。期待关于这些的更新或新帖子！

感谢阅读！

Jonathan Pielmayer 在 Unsplash 上拍摄的封面照片//这篇文章也会出现在我的博客上，届时它会有点功能性。