# 用 VuePress 和 Tailwind.css 构建一个漂亮的网站

> 原文：<https://dev.to/frontendfoxes/build-a-beautiful-website-with-vuepress-and-tailwindcss--3a03>

最近我一直在做网站。有什么大不了的，对吧？作为一名 web 开发人员，这是我的专长。然而，对我来说，这是一个方向的转变。在过去的几年里，我主要从事移动应用程序的开发，网络工作是后来才想到的，因为我开发了快速营销网站来配合我的移动应用程序(比如这个超级基本的 Wordpress 网站，用于我的 [Snappy Squirrel](http://www.snappysquirrel.com/) 应用程序，以及这个非常旧的应用程序网站，用于[练习伙伴](http://www.practicebuddyapp.com))。显然，随着我的应用程序 repos 和互联网上到处都是快速‘n’肮脏的 web 开发实验的遗迹，是时候升级创建可维护和可持续的网站了。

以前，Wordpress 是这类网站的救星。它既适用于小型营销网站，也适用于大型博客。但是我更喜欢轻量级的，用我日常使用的语言编写的(例如，不是 Php)，这样更容易定制。因此，这些年来我一直在尝试各种静态站点生成器，包括最近为我自己的博客开发的 Hexo。不幸的是，[jenlooper.com](http://www.jenlooper.com)更新起来相当痛苦，并且基于现成的 Hexo 主题有相当沉重的设计。对我来说，它似乎还是“Wordpressy ”,它的设计在帮助好奇的用户找到我想要展示的内容方面做得并不好。

[![Current Web Site](img/bb8d32338676a7c23ead69f02f5b2aea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0lp2020v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ilex6c2mopxsk85tndf6.png)

那么，作为一名 Vue.js 开发人员，让网站尽快运行起来的最有效方法是什么？VuePress 立即跃入脑海。虽然 VuePress 主要用作静态站点生成工具来创建好看的文档(一个例子是 [VuePress 自己的站点](https://vuepress.vuejs.org/))，但如果我们将它用于技术博客、应用营销站点或任何其他我们需要快速启动和运行的实用网站，会怎么样呢？

## 使用 Vuepress 开始运行

安装 VuePress 后(全局或本地，根据文档)，你可以添加一些文件，瞧，你有了自己的博客或文档，只需在文件夹中添加一个 README.md 文件，并在终端中从根目录键入`vuepress dev`。再添加几个文件，你就会看到一些非常漂亮的东西:

[![Up and running with VuePress](img/9acc6f7413d1d664036d4358009e0b92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FshmJxJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/70i84hvv36erb4d276k8.png)

我创建了一个非常简单的 Vuepress 站点，如上所示，总共有两个文件夹和三个文件:

[![Simplest Site ever](img/dcab7c5da30087da2ce7387f782a2c3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wWDdKWxy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bv2gt7kfm5e1dcl1mx8m.png)

> 这个极其简单的 VuePress 网站在[这里](https://github.com/jlooper/vuepress-test)可用:安装 VuePress，cd 到这个 repo，然后输入`vuepress dev`。

VuePress 的唯一问题是，和 Wordpress 一样，用这些工具生成的网站看起来很老套:“VuePressy”，我喜欢这么说。如果我们可以使用 VuePress 强大的静态网站生成功能，并设计一个看起来完全定制的网站，会怎么样？进入 [Tailwind.css](http://tailwindcss.com) ，这个网站上的新成员因其不带偏见的风格而受到网络开发者的喜爱，因为**就是这样工作的**。

## 为什么要顺风？

Tailwind 是另一种 CSS 框架，它自称是“一个实用的 CSS 框架，用于快速构建定制的用户界面。”与 Vuetify、Bootstrap、布尔玛或市场上其他主要的样式框架不同，Tailwind 不是像 UI 工具包一样给你的网站添加组件。它不固执己见，也没有默认的主题或内置组件。如果你想完全控制你的网站的外观，同时又想避免手工编写所有的 CSS，那么 Tailwind 可能很适合你。

[![NativeScript-Vue.org - a Tailwind-styled website](img/3c425b32686ffa40fe214883e4ac54bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KFL78tV3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j2ntgkr9ygpvd1vkgc4v.png)

> Bonus - Tailwind.css 也可以在移动设备上运行，使用 NativeScript-Vue 移动应用程序。请继续关注教程。

让我们通过创建一个样板 VuePress 站点来开始这个项目。您可以使用我在上面创建的示例作为模板。

## 安装 Tailwind.css

现在，是时候**安装 Tailwind.css** 了。因为 Tailwind 需要作为 npm 包安装，所以我们需要在根目录下有一个 package.json 文件。在项目的根文件夹中创建一个，并添加以下元素，编辑以个性化项目:

```
{
    "name": "MyBlog",
    "version": "1.0.0",
    "description": "A new Blog",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/your-github-handle/blog.git"
    },
    "keywords": [
        "keywords"
    ],
    "author": "your-name",
    "license": "MIT",
    "bugs": {
        "url": "https://github.com/your-github-handle/blog/issues"
    },
    "homepage": "https://github.com/your-github-handle/blog/#readme",
    "devDependencies": {
        "tailwindcss": "^0.7.3",
        "autoprefixer": "^9.4.2",
        "postcss-import": "^12.0.1",
        "postcss-loader": "^3.0.0"
    }
} 
```

`Yarn install`或`npm install`包装。这是怎么回事？嗯，我们添加了 Tailwind 包，还添加了 autoprefixer、postcss-import 和 postcss-loader，因为这些包是 VuePress 使用 Tailwind css 所必需的。

下一步是创建一个顺风配置文件。通过在根文件夹中键入`./node_modules/.bin/tailwind init tailwind.js`来完成此操作。让我们看看这个命令生成的文件。我们马上注意到它非常长，超过 900 行。其中有一些设置，比如顺风的默认颜色。你会发现像`'red': '#e3342f'`这样的东西。您可以在 css 中使用这些预设来快速设置元素的样式:`<p class="text-red">`。因为这个文件太长了，你可能想删除它的一部分，使它变轻一点；根据您的喜好自定义该文件。例如，你可以不带注释安装它，这样会缩短它。

> 提示:按照这些指令使用 PurgeCSS 压缩文件。谢谢提示， [@ohffs](https://dev.to/ohffs) ！

接下来，我们需要使这些风格能够被你的网站所采用。我们将在 VuePress 的上下文中使用该文件，因此让我们为自定义主题设置 VuePress。

## 创建自定义 Vuepress 主题

VuePress 自带默认的外观，我们知道我们想要覆盖它，所以我们必须在`.vuepress`中创建一个名为`theme`的新文件夹和一个名为`Layout.vue`的文件。在该文件夹中添加一个名为`styles`的文件夹，并在该文件夹中添加一个`theme.styl`文件。您的文件夹结构现在看起来像这样:

[![A little more complexity](img/ef56187d1ed3578b6466aef60ee6eb22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I860ijq3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ucsgvctrb72u31a91dve.png)

> 为什么要通过 theme.styl 文件使用手写笔？你不必使用手写笔，但它在 VuePress 文档中使用，所以我继续使用这种模式。

## 编辑 Vuepress 支持顺风

现在，您需要为 VuePress 提供顺风。有几件事必须做，使他们一起玩得很好。

首先，在`.vuepress/config.js`中，编辑代码以使用 postcss 导入 tailwindcss 插件和配置文件。现在，整个 config.js 文件如下所示:

```
module.exports = {
    postcss: {
      plugins: [require('tailwindcss')('./tailwind.js'), require('autoprefixer')],
    },
    title: 'Hello VuePress',
    description: 'Just playing around'
  } 
```

接下来，编辑`.vuepress/theme/styles/theme.styl`以导入顺风飞行前检查和实用程序类。将这两行添加到。风格文件:

```
@tailwind preflight;
@tailwind components;
@tailwind utilities; 
```

最后，编辑`.vuepress/theme/Layout.vue`,这样新的布局文件可以选择新的样式:

```
<template>
<div>
    <p class="text-red">hi</p>
</div>
</template>
<style lang="stylus">
    @import './styles/theme.styl';
</style> 
```

重启你的开发服务器(Ctrl-C 来停止它，然后`vuepress dev`来重启它)并看一看。它不是很漂亮，但是你可以看到一个以前没有的独特风格:红色的文字。

通过试用这个文件，了解 Tailwind 如何设计您的标记样式。如果您稍微编辑一下标记:

```
<div class="rounded shadow-lg w-1/2 m-10 p-5 bg-green">
    <p class="text-red text-lg text-center font-sans">I'm a red text!</p>
</div> 
```

您可以看到一张彩色卡片已添加到您的网页:

[![A card](img/7732903275e9720d0c85ef08caec75f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4zsulLkc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zhrmm4fvc5jyzmtceh3c.png)

您添加了:

*   一个包含几个类的容器 div 产生了一个圆形的盒子(`rounded`)
*   带着大阴影(`shadow-lg`)，
*   宽度设置为视口的一半(`w-1/2`)，
*   周围有 2.5 雷姆的余量(`m-10`)
*   四周填充 1.25 雷姆(`p-5`)，
*   以绿色背景结束(`bg-green`)。

在内部，您将文本的样式设置为:

*   红色(`text-red`)，
*   大号的(`text-lg`)，
*   居中(`text-center`)，
*   用无衬线字体(`font-sans`)。

了解顺风的工作原理了吗？它让您对自己的外观和感觉有很大的控制权，同时提供了合理的默认值。

> 该网站的这个版本可以在这里查看。

## 在顺风环境下充分利用 VuePress

既然您已经很好地控制了样式，那么您需要开始做出一些架构决策。你的网站会有多少。vue 文件，带有普通的 HTML 标记和硬编码文本，VuePress 友好的降价是多少？这两种架构经常看起来是对立的，因为它看起来像是通过 HTML 标签进行样式降价的反模式。我发现这有助于想象你的顺风风格。vue 文件作为 VuePress markdown 的容器或外壳。如果你坚持把所有的文本都放在 markdown 中，维护你的博客会更容易，尤其是如果你决定本地化你的内容。

在开发我网站的新版本时，我不得不不断地做出这样的决定。我们的目标是希望将文本放在 markdown 中，将 HTML 放在。vue 文件，让我们进一步发展我们的自定义网站主题。

在接下来的几个整合这两种资源的技巧中，我将转向我重写《NativeScript-Vue.org》的经历。我也在重建 jenlooper.com，看起来像这样:

[![Home page](img/859b3be6d9f23ac689053df2050c44fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--COxgV4Iw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/36njqo1i1h8ixgel1fxj.png)

[![Inner page](img/123933e758908c947d176daec5c121dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zvQcS4em--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qk9lcoktiz61osaltnf3.png)

## 挑战:采用 VuePress 和顺风的多种布局

一旦你创建了一个非常简单的 VuePress 网站，只有几条路线和一个基本的主题布局，你很快就可以利用 VuePress 和 Tailwind 的优势，而不会完全迷路。让我们通过创建一个完全自定义外观的网站，有两种不同的布局，即我的网站，目前正在重建。你可以在这里跟随这个项目[。](https://github.com/jlooper/jenlooper.com)

这个个人网站的想法是展示我的项目，并为我的文章提供空间，这些文章分散在几个网站上(Telerik Developer Network、NativeScript Blog、Progress Blog 和 Dev.to)。初始页面将是项目卡的展示，内页将为链接和各种其他信息页面提供一个区域。这些组件至少可以分为两种布局——home layout 和 BlogLayout。此外，还有一些嵌套在其中的组件，即 Cards 组件和 Nav 和 Footer。相当标准的东西:HomeLayout 将包含卡片，而 BlogLayout 不包含。这里的挑战是使 markdown 能够填充网站的所有区域，以保持文本和 HTML 标记之间的重要分离。

首先要做的是在`.vuepress/theme` : `components`和`layouts`中创建两个新文件夹。创建任何新组件。vue 文件，并将它们放在 components 文件夹中，在 layouts 文件夹中进行同样的操作。我的`.vuepress`文件夹的结构现在看起来像这样:

[![Folder Structure](img/342c89eb01745e127c78bab1b83a35fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OVc6HYhR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23mhksnhsxf1qnmpbty7.png)

然后，扩展`.vuepress/theme/Layout.vue`支持多种布局。关键是从强制的 Layout.vue 文件中删除`<Content/>`标签，并使用一个更智能的组件标签，该标签根据 markdown frontmatter 中指定的布局进行更改。然后，Layout.vue 文件中的`<template>`块变成这样:

```
<template>
<div class="wrapper">  
    <div class="markdown-body m-10 rounded"> 
        <Nav/>            
        <component :is="layout"></component>
        <Footer/>  
    </div> 
</div>
</template> 
```

然后，添加一个`<script>`块来导入定制组件和新的布局文件。对于我的网站，它看起来像这样:

```
<script>
import Nav from './components/Nav.vue';
import Footer from './components/Footer.vue';
import HomeLayout from './layouts/HomeLayout.vue';
import BlogLayout from './layouts/BlogLayout.vue';
export default {
    components: { Nav, Footer, HomeLayout, BlogLayout },
    computed: {
        layout() {
        return this.$page.frontmatter.layout || 'HomeLayout'
        }
  },
}
</script> 
```

computed 属性`layout()`检查给定页面的 frontmatter 的指定布局，并相应地分配它。现在，您可以开始以更精细的方式定制您的布局。

因为我们绕过了 VuePress 通过 frontmatter 创建布局的标准方式，所以我们必须为页面上的每个元素指定顺风样式。这是你为避免样板文件所付出的代价，但这是值得的！

## 构建您的布局

让我们看看我将用于大多数内页的布局。我的站点中的 BlogLayout.vue 页面现在包含 VuePress 显示 markdown 所需的`<Content/>`标签。例如，在这个页面中，我添加了一些样式来包围生成的 markdown:

```
 <template>
        <div class="blog-body bg-white rounded m-10 text-blue-darkest">
            <div class="p-10"> 
                <h1 class="pb-5 text-blue-darkest">{{$page.frontmatter.title}}</h1>
                <Content/>
            </div>
        </div>
    </template>
    <script>
    export default {
        name: 'BlogLayout'
    }
    </script> 
```

VuePress 然后可以找到给定路线的降价文件——默认情况下，它们以一个文件夹加一个 README.md 文件的形式出现。例如，我的“关于”页面使用 BlogLayout 文件进行显示。`/about`中的 README.md 文件只包含一点 frontmatter 和一些文本:

```
---
title: About Me
layout: BlogLayout
---    
Welcome! I'm a Google Developer Expert for Web Technologies and a Senior             Developer Advocate at Progress with over 15 years' experience as a web and mobile developer, specializing in creating cross-platform mobile apps. I'm a multilingual multiculturalist with a passion for hardware hacking, mobile apps, Vue.js, machine learning and discovering new things every day. I'm also the founder and CEO of Vue Vixens, an initiative promoting diversity in the Vue.js community. Contact me here or on Twitter @jenlooper.

Our Privacy Policy can be found [here](/privacy-policy)

Looking to contact me? Email me at jen @ ladeezfirstmedia.com. My resume can be found [here](https://standardresume.co/JenLooper). 
```

VuePress 设计网站，Tailwind 处理其样式。但是，如果您不太满意，并且想要为 VuePress 生成的`<p>`标签指定样式，该怎么办呢？

你可以在你的 markdown 中添加 HTML 标签，但是记住，我们尽量不这么做。相反，让 markdown 单独存在，使用 Tailwind 的`@apply`指令，通过向`theme.styl`文件:
添加一个片段，将 Tailwind 样式分配给生成的 HTML

```
 @css {
        .blog-body p {
            @apply .pb-5;
        }
    } 
```

> 注意，通常你可以添加`@apply`到没有`@css`标记的 CSS 类中，但是手写笔文件似乎有一个问题，比如`@apply`并不总是被使用。

## 例 1:关于

在使用 frontmatter 和 HTML 之间找到良好平衡的另一个例子是我在`.vuepress/theme/components/Cards.vue`中构建的卡片界面。在这里，由于这些卡片显示在主页上，所以我使用了从根 README.md 文件派生的内容。Frontmatter 是无限灵活的，所以我在 README 中创建了一个新的卡片内容数组:

```
 cards: [
        {'title': 'Vue Vixens', 'image': 'vuevixens_logo.png', 'blurb': 'Vue Vixens offers free workshops and meetups for foxy people who identify as women. Join us at a local skulk or in a tech conference!','link':'https://www.vuevixens.org'},
        {'title': 'Elocute', 'image': 'elocute_logo.png', 'blurb': 'Elocute is a mobile and web app designed to replace the language lab. Students and teachers of second languages, rejoice!','link':'http://www.elocute.me'},
       ...
    ] 
```

为了显示这些内容，我遍历了`Cards.vue`中的数组，挑选出每个元素并用样式包围起来:

```
 <template>
    <div class="flex flex-wrap pt-5 m-2 justify-center"> 
        <div v-for="item in $page.frontmatter.cards" class="w-1/4 m-2 cursor-pointer bg-white max-w-sm rounded shadow-lg text-center" @click="goToRoute(item.link)">
            <img class="pt-5" :src="img/'+item.image+''" :alt="item.title">
            <div class="px-6 py-2">  
                <div class="text-blue-darkest font-bold text-xl mb-2">{{item.title}}</div>
                    <p class="text-blue-darker">
                        {{item.blurb}}
                    </p>
                </div>  
        </div>   
    </div>
    </template> 
```

## 例 2:导航

您不必局限于使用页面级的降价内容；对于我的 Nav.vue 导航组件，我转向我的站点级`config.js`文件，它包含一个我用来构建通用导航的 Nav 对象:

```
 <span v-for="item in $site.themeConfig.nav" >
        <a :href="item.link" class="block m-4 lg:inline-block lg:mt-0 no-underline text-white hover:text-orange uppercase">
          {{item.text}}
        </a>
    </span> 
```

## 结论

从现在开始更新我的网站将是一个梦想。不要再为可维护性和脆弱的设计找借口！一旦你的布局到位，你已经决定了你的减价文件的内容，你就有了一个非常坚实的架构来构建漂亮的、可伸缩的、易于维护的网站。尽情享受，并在下面添加您关于与 VuePress 和 Tailwind 结合的提示和技巧。