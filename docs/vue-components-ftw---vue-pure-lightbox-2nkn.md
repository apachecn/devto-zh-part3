# Vue 组件 FTW-vue-纯灯箱

> 原文：<https://dev.to/raymondcamden/vue-components-ftw---vue-pure-lightbox-2nkn>

这篇文章是一系列文章的一部分，这些文章着眼于可以添加到 Vue.js 应用程序中的简单易用的组件。你可以在这里观看整个系列[，并给我留下建议！](https://www.raymondcamden.com/tags/vue+components+ftw/)

今天简单的 Vue 组件是 [vue-pure-light](https://github.com/DCzajkowski/vue-pure-lightbox) ，一个*非常*轻便简单的“灯箱”组件。如果你不知道 lightbox 是什么，它是一个 UI/UX 功能，一张图片可以占据整个屏幕，让你专注于它。你可能在房地产列表或艺术网站上看到过。该组件支持 npm 安装以及直接在你的 HTML:
中加入 CSS 和 JS 标签

```
<!-- In <head> -->
<meta rel="stylesheet" href="https://unpkg.com/vue-pure-lightbox/dist/vue-pure-lightbox.css">
<!-- In <body>, after Vue import -->
<script src="https://unpkg.com/vue-pure-lightbox/dist/vue-pure-lightbox.js"></script> 
```

一旦安装完毕，您就可以在您的应用程序中使用`<lightbox>`标签。总共有三个参数——一个用于缩略图(初始图像),一个用于图像 URL 数组，还有一个替代文本值。

仅此而已。你也可以提供一个定制的加载器，但是我发现这个现成的很容易使用。这是作者提供的一个 CodePen 例子:

[https://codepen.io/DCzajkowski/embed/rzOErW?height=600&default-tab=result&embed-version=2](https://codepen.io/DCzajkowski/embed/rzOErW?height=600&default-tab=result&embed-version=2)

请特别注意 CSS 面板。虽然文件提到有定制的风格，但实际上并没有列举出来。这里的 CSS 面板是一个关于你可以定制什么的便利参考。而且，他用猫，所以我爱他。结案了。

稍微高级一点的例子怎么样？(我真正的意思是“稍微”…)我从下面的标记开始:

```
<div id="app" v-cloak>

  <lightbox
    thumbnail="https://www.placecage.com/200/200"
    :images="images"
  >
     <lightbox-default-loader slot="loader"></lightbox-default-loader> 
  </lightbox>

</div> 
```

如果你不认识缩略图的 URL，我使用的是 [PlaceCage](http://www.placecage.com/) ，一个完全由 Nicolas Cage 图片组成的占位符图片服务。我已经指定我的图像来源于 Vue 实例中的数据，所以让我们来看看。

```
Vue.config.silent = true;

Vue.use(Lightbox);

const app = new Vue({
  el:'#app',
  data() {
    return {
      images:[]
    }
  },
  mounted() {
    for(let i=0;i<10;i++) {
      this.images.push(`https://www.placecage.com/c/${600 + (i*10)}/${600 + (i*10)}`);
    }
  }
}) 
```

在这种情况下，我刚刚从服务中创建了 10 个动态大小的图像。您可以在此运行此示例:

[https://codepen.io/cfjedimaster/embed/aXMwGG?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/aXMwGG?height=600&default-tab=result&embed-version=2)

因此，在写这篇博文的时候，我发现我最喜欢的占位符服务 [placekitten](https://placekitten.com/) 已经恢复运行了！去他妈的尼古拉斯凯奇！这是上一个有小猫的例子的分支。好多了！

[https://codepen.io/cfjedimaster/embed/RvOeWo?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/RvOeWo?height=600&default-tab=result&embed-version=2)

那不是更好吗？一如既往-如果你对这个系列有任何意见或建议，请在下面给我留言。