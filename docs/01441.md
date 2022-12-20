# 在您的 Vue & Nuxt 应用程序中缩小、生成 WebP 和 lazyload 图像

> 原文：<https://dev.to/ignore_you/minify-generate-webp-and-lazyload-images-in-your-vue-nuxt-application-1ilm>

如果标准图像优化流程对我来说不是如此艰难，我不会写这篇文章。起初，我只想把我最喜欢的 [`lazysizes`](https://github.com/aFarkas/lazysizes) 应用到我正在开发的应用程序中。我被困在模块和插件的概念中，无法弄清楚它到底是什么。下一步是图像优化，并将当前的 png 和 JPEGs 转换成 web PS——这变得非常棘手。

我们都阅读像这样的帖子来看一个工作示例，所以它在这里！

## 注射懒虫

`npm i lazysizes` —无需解释。

然后创建一个`vue-lazysizes.client.js`文件，放入`@/plugins/`文件夹。`.client`在名字中通知服务器这个插件不应该在服务器上运行——这很重要！这个文件应该只包含两行:

```
import lazySizes from 'lazysizes'
export default lazySizes 
```

Enter fullscreen mode Exit fullscreen mode

配置`nuxt.config.js` :

```
module.exports = {
  build: {
    extend (config, { isDev, isClient, loaders: { vue } }) {
      if (isClient) {
        vue.transformAssetUrls.img = ['data-src', 'src']
        vue.transformAssetUrls.source = ['data-srcset', 'srcset']
      }
    }
  },
  plugins: [
    '~/plugins/vue-lazysizes.client.js'
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到我们的“插件”被插入，在`build`键中我们扩展了 Webpack 配置，因此 vue-loader 可以转换`<img>`和`<source>`标签中的`data-srcset`、`data-src`和`srcset`属性中的别名——我们将来会需要它。

仅此而已！现在你可以像往常一样让图像变得“懒惰”:

```
<figure class="picture">
  <img data-src="~asseimg/image.png" class="lazyload" alt="Alternate text for the image">
</figure> 
```

Enter fullscreen mode Exit fullscreen mode

## 图像优化&转换为 WebP

但是把图片变小，加载 WebP 图片+ fallback 呢？经过几个小时的努力和多次尝试，我找到了出色的模块 [`nuxt-optimized-images`](https://github.com/bazzite/nuxt-optimized-images) 。我更喜欢用`mozjpeg`和`pngquant`分别进行 JPEG 和 PNG 优化。

这是它应该被烹饪的方式:

`npm i @bazzite/nuxt-optimized-images imagemin-mozjpeg imagemin-pngquant webp-loader --save-dev` —依赖项的安装。

配置`nuxt.config.js` :

```
module.exports = {
  modules: [
    '@bazzite/nuxt-optimized-images',
  ],

  optimizedImages: {
    inlineImageLimit: -1,
    handleImages: ['jpeg', 'png', 'svg', 'webp', 'gif'],
    optimizeImages: true,
    optimizeImagesInDev: false,
    defaultImageLoader: 'img-loader',
    mozjpeg: {
      quality: 85
    },
    optipng: false,
    pngquant: {
      speed: 7,
      quality: [0.65, 0.8]
    },
    webp: {
      quality: 85
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`optimizedImages`键的设置有争议，但这是我的选择。

在所有这些操作之后，你最终可以使用这个代码片段:

```
<figure class="picture">
  <picture>
    <source data-srcset="~asseimg/image.png?webp" type="image/webp">
    <source data-srcset="~asseimg/image.png" type="image/png">
    <img data-src="~asseimg/image.png" class="lazyload" alt="Alternate text for the image">
  </picture>
</figure> 
```

Enter fullscreen mode Exit fullscreen mode

注意，WebP 图像源是用`?webp`添加的。

有了这个伟大的模块，你可以更进一步，例如，创建小的 SVG-fallback，并在加载大图像时使用它。您可以创建响应图像，生成回退颜色(可能)等等。但是让它成为你今天的家庭作业。

感谢阅读，我希望在你像我一样花几天时间做实验之前，你能在谷歌上找到这篇文章。