# 使用 Web 组件构建 Instagram/Whatsapp stories 克隆🔥

> 原文：<https://dev.to/gugadev/building-an-instagram-stories-component-using-web-components-2gia>

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [古加捷夫](https://github.com/gugadev) / [斯托瑞斯](https://github.com/gugadev/storify)

### 基于 Web 组件和 Web 动画 API 构建的 Instagram/Whatsapp stories 克隆。🔥

<article class="markdown-body entry-content container-lg" itemprop="text">

# wc <g-emoji class="g-emoji" alias="globe_with_meridians" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f310.png">🌐</g-emoji>故事

Instagram/Whatsapp 故事，如基于 Web 组件和 Web 动画 API 构建的故事

## 民众

| [![](img/89b01f4193ba33dea4af488374a74635.png)](https://github.com/gugadev/storify) 
香草 JS | [![](img/a382a8e4d965e75f4942ceb27c945766.png)](https://github.com/gugadev/storify) 
棱角分明 | [![](img/f5401c26784fee6bd5757991096cc23d.png)](https://github.com/gugadev/storify) 
有什么反应 | [![](img/af32d3c6f632efe02e856f87ae7a4b4c.png)](https://github.com/gugadev/storify) 
视图 |
| --- | --- | --- | --- |
| [链接](https://gugadev.github.io/wc-stories) | [链接](https://angular-yhsvse.stackblitz.io/) | [链接](https://react-zf2tfr.stackblitz.io/) | [链接](https://vue-et4qky.stackblitz.io/) |

## 浏览器支持

| [![IE / Edge](img/0d7406188dca3af88f338de22a3d7b43.png)](http://godban.github.io/browsers-support-badges/) 
IE / Edge | [![Firefox](img/ff9cc1628eb13a3a3281081c389d5b25.png)](http://godban.github.io/browsers-support-badges/) 
火狐 | [![Chrome](img/14a8c8b5bedb6533375b6062749af19c.png)](http://godban.github.io/browsers-support-badges/) 
铬 | [![Safari](img/acc403ea226add6eeb284ed36db195ad.png)](http://godban.github.io/browsers-support-badges/) 
游猎 | [![Opera](img/24055376a7eaea42846d151d51746d1e.png)](http://godban.github.io/browsers-support-badges/) 
戏曲 |
| --- | --- | --- | --- | --- |
| IE11，边缘 | 最近 10 个版本 | 最近 10 个版本 | 最近 10 个版本 | 最近 10 个版本 |

* * *

## <g-emoji class="g-emoji" alias="package" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4e6.png">📦</g-emoji>安装

```
npm i @gugadev
/wc-stories
```

Enter fullscreen mode Exit fullscreen mode

## <g-emoji class="g-emoji" alias="bulb" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4a1.png">💡它的用途是什么？</g-emoji>

只是好玩而已<g-emoji class="g-emoji" alias="slightly_smiling_face" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f642.png">🙂</g-emoji>。我喜欢学习和编码，所以，每次我有空的时候，我都会挑选一些疯狂的想法或者从其他项目中得到灵感，然后把它做出来。<g-emoji class="g-emoji" alias="yum" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f60b.png">😋</g-emoji>

## <g-emoji class="g-emoji" alias="unicorn" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f984.png">🦄</g-emoji>灵感

当我看到 Mohit 的项目时， [react-insta-stories](https://github.com/mohitk05/react-insta-stories) ，立刻想知道使用 **Web 组件**做同样的事情会有多复杂。所以，我做了这个。谢谢你，莫希特！<g-emoji class="g-emoji" alias="smiley" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f603.png">😃</g-emoji>

## <g-emoji class="g-emoji" alias="gear" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2699.png">⚙️</g-emoji> 它是如何工作的？

有三个组件协同工作:

*   `<wc-stories-story>`:这个组件显示一个图像。图像的最大尺寸是…

</article>

[View on GitHub](https://github.com/gugadev/storify)

> **注:**本帖不贴组件样式，重点是逻辑。然而，你可以在 [Github 回购](https://github.com/gugadev/wc-stories)中找到它们。

## 🦄灵感

几天前，我发现了一个来自 *Mohit Karekar* 的名为 [react-insta-stories](https://github.com/mohitk05/react-insta-stories) 的项目。我认为这是有趣的建立相同的想法，但使用 Web 组件代替。所以，我拿起我的电脑，开始编码。😉

## 设置

在任何项目中，您需要做的第一件事是设置开发环境。在一个常规的前端项目中，我们将最终使用 Webpack 作为 transpiler 和 bundler。此外，我们将使用 [lit-element](https://github.com/polymer/lit-element) 来编写我们的 Web 组件和 PostCSS 进行造型，并使用一些插件，如 *cssnano* 。

🛠️开发依赖:

```
yarn add --dev webpack webpack-cli webpack-dev-server uglifyjs-webpack-plugin html-webpack-plugin clean-webpack-plugin webpack-merge typescript tslint ts-node ts-loader postcss-loader postcss-preset-env cross-env cssnano jest jest-puppeteer puppeteer npm-run-all 
```

Enter fullscreen mode Exit fullscreen mode

⚙️运行时依赖:

```
yarn add lit-element core-js @types/webpack @types/webpack-dev-server @types/puppeteer @types/node @types/jest @types/jest-environment-puppeteer @types/expect-puppeteer 
```

Enter fullscreen mode Exit fullscreen mode

我们的源代码必须在`src/`文件夹中。此外，我们需要创建一个`demo/`文件夹，并将一些图像放入其中。

### Webpack

让我们将 Webpack 配置分为三个部分:

*   `webpack.common.ts`:为两种环境提供共享配置。
*   `webpack.dev.ts`:仅用于开发的配置。
*   `webpack.prod.ts`:仅用于生产的配置。在这里，我们必须进行一些调整，如捆绑优化。

让我们看看那些文件。

**webpack.common.js**

```
import path from 'path'
import CleanWebpackPlugin from 'clean-webpack-plugin'
import webpack from 'webpack'

const configuration: webpack.Configuration = {
  entry: {
    index: './src/index.ts'
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, 'dist')
  },
  resolve: {
    extensions: [
      '.ts',
      '.js'
    ]
  },
  module: {
    rules: [
      {
        test: /\.(ts|js)?$/,
        use: [
          'ts-loader'
        ],
        exclude: [
          /node_modules\/(?!lit-element)/
        ]
      },
      {
        test: /\.pcss?$/,
        use: [
          'css-loader',
          'postcss-loader'
        ]
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(['dist'])
  ]
}

export default configuration 
```

Enter fullscreen mode Exit fullscreen mode

这个文件包含了基本的配置，比如，`entry`和`output`设置，规则和一个插件，用于在每次编译之前清理输出文件夹。

**webpack.dev.js**

```
import webpack from 'webpack'
import merge from 'webpack-merge'
import HtmlWebpackPlugin from 'html-webpack-plugin'
import common from './webpack.common'

const configuration: webpack.Configuration = {
  mode: 'development',
  devtool: 'inline-source-map',
  devServer: {
    contentBase: './demo',
    publicPath: '/',
    compress: true,
    port: 4444
  },
  plugins: [
    new HtmlWebpackPlugin({
      filename: './demo/index.html'
    })
  ]
}

export default merge(common, configuration) 
```

Enter fullscreen mode Exit fullscreen mode

开发配置只添加了 webpack-dev-server 设置和一个额外的插件，以使用 HTML 文件作为为开发服务器提供的*index.html*。

**webpack.prod.js**

```
import webpack from 'webpack'
import merge from 'webpack-merge'
import UglifyPlugin from 'uglifyjs-webpack-plugin'
import common from './webpack.common'

const configuration: webpack.Configuration = {
  mode: 'production',
  devtool: 'source-map',
  optimization: {
    minimizer: [
      new UglifyPlugin({
        sourceMap: true,
        uglifyOptions: {
          output: { comments: false }
        }
      })
    ]
  }
}

export default merge(common, configuration) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们的生产配置只是调整了一些🚀使用 uglifyjs-webpack-plugin 包的优化选项。

这就是所有的 webpack 配置。最后一步是在我们的 *package.json* 中创建一些脚本来运行开发服务器并生成一个⚙️ *生产版本* :

```
"start":  "cross-env TS_NODE_PROJECT=tsconfig.webpack.json webpack-dev-server --config webpack.dev.ts",  "build":  "cross-env TS_NODE_PROJECT=tsconfig.webpack.json webpack --config webpack.prod.ts", 
```

Enter fullscreen mode Exit fullscreen mode

### PostCSS

我们需要在项目的根目录下创建一个包含以下内容的`.postcssrc`文件，以正确处理我们的*。pcs 文件:

```
{  "plugins":  {  "postcss-preset-env":  {  "stage":  2,  "features":  {  "nesting-rules":  true  }  },  "cssnano":  {}  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 打字稿

最后，我们需要创建一个`tsconfig.json`文件来配置我们的类型脚本环境:

```
{  "compilerOptions":  {  "target":  "es5",  "module":  "commonjs",  "allowJs":  true,  "esModuleInterop":  true,  "moduleResolution":  "node",  "emitDecoratorMetadata":  true,  "experimentalDecorators":  true,  "sourceMap":  true,  "removeComments":  true  },  "include":  [  "src/"  ],  "exclude":  [  "node_modules/"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

另外，创建一个`tsconfig.webpack.json`文件，该文件将由 ts-node 使用 Typescript:
运行 Webpack

```
{  "compilerOptions":  {  "target":  "es5",  "module":  "commonjs",  "esModuleInterop":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 🏭结构

让我们把事情简单化。我们需要编写三个组件:

*   容器
*   故事
*   进度条

容器是逻辑将被写入的地方。在这里*我们控制哪个图像应该可见，哪个不可见*，我们还需要处理*前一个*和*后一个*的点击。*故事*组件是*组件，图像将在这里显示*，而*进度条*组件是*组件，我们可以在这里可视化当前图像的定时*。

## 📦`<story>`组件。

这个组件很简单，它只包含一个内部有一个`img`的`div`。该图像的包装是必要的，使动画。

让我们在`stories/`文件夹下创建一个`index.ts`文件，内容如下:

```
import {
  LitElement,
  html,
  customElement,
  property
} from 'lit-element'
import styles from './index.pcss'

@customElement('wc-stories-story')
class Story extends LitElement {

  /**
   * @description image absolute or relative url
   */
  @property({ type: String }) src = ''
  /**
   * @description checks if an image is available to show
   */
  @property({ type: Boolean }) visible = false

  render() {
    return html`
      <div class="${this.cssClass}">
        <img src="${this.src}" />
      </div>
      <style> ${styles.toString()} </style>
    `
  }

  get cssClass() {
    return [
      'stories__container__story',
      this.visible ? 'visible' : ''
    ].join('  ')
  }
}

export { Story } 
```

Enter fullscreen mode Exit fullscreen mode

使用 lit-element 的 Web 组件的结构很简单。您需要实现的唯一**强制方法是`render`。此方法必须返回将被隐藏的 html 内容。**

这个组件接受两个属性。第一个是要显示的图像的相对或绝对 URL(`src`)，第二个是通知组件何时应该显示的标志(`visible`)。

您将意识到每个组件从一个独立的`.pcss`文件中导入它的样式，该文件包含 PostCSS 代码。多亏了 *postcss-loader* 和*style-loader*webpacks loaders，这才成为可能。

就这样🙂很简单，对吧？让我们看看下一个组件。

## 📦`<progress>`组件

这个组件很小，但是很有趣。这个模块的职责是为每个图像提供一个动画。动画只是一个进度条，用的是**网络动画 API** ！

```
import {
  LitElement,
  html,
  property,
  customElement
} from 'lit-element'
import styles from './index.pcss'
/* Array.from polyfill. The provided by Typescript
 * does not work properly on IE11.
 */
import 'core-js/modules/es6.array.from'

@customElement('wc-stories-progress')
class Progress extends LitElement {

  /**
   * @description count of images
   */
  @property({ type: Number }) segments = 0

  /**
   * @description current image index to show
   */
  @property({ type: Number, attribute: 'current' }) currentIndex = 0

  /**
   * @description progress' animation duration
   */
  @property({ type: Number }) duration = 0

  /**
   * @description object that
   * contains the handler for onanimationend event.
   */
  @property({ type: Object }) handler: any = {}

  /**
   * Current animation
   */
  private animation: Animation

  render() {
    const images = Array.from({ length: 5}, (_, i) => i)

    return html` ${
        images.map(i => (
          html`
            <section
              class="progress__bar"
              style="width: calc(100% / ${this.segments || 1})"
            >
              <div id="track-${i}" class="bar__track">
              </div>
            </section>
          `
        ))
      } <style> ${styles.toString()} </style>
    `
  }

  /**
   * Called every time this component is updated.
   * An update for this component means that a
   * 'previous' or 'next' was clicked. Because of
   * it, we need to cancel the previous animation
   * in order to run the new one.
   */
  updated() {
    if (this.animation) { this.animation.cancel() }

    const i = this.currentIndex
    const track = this.shadowRoot.querySelector(`#track-${i}`)

    if (track) {
      const animProps: PropertyIndexedKeyframes = {
        width: ['0%', '100%']
      }
      const animOptions: KeyframeAnimationOptions = {
        duration: this.duration
      }
      this.animation = track.animate(animProps, animOptions)
      this.animation.onfinish = this.handler.onAnimationEnd || function () {}
    }
  }
}

export { Progress } 
```

Enter fullscreen mode Exit fullscreen mode

该组件具有以下属性:

*   `duration`:动画的持续时间。
*   `segments`:图像的计数。
*   `current`:要显示的当前图像(索引)。
*   `handler`:包含`onanimationend`事件处理程序的对象。

*处理程序*属性是一个文字对象，包含一个名为`onAnimationEnd`的函数(您将在最后一个组件中看到它)。每次当前动画结束时，该函数在父组件上执行，更新当前索引并显示下一个图像。

此外，我们将当前动画存储在一个变量中，以便❌在需要制作下一条动画时取消当前动画。否则每一个动画都会一直可见。

## 📦`<stories>`组件

这是我们的最后一个组件。这里，我们需要处理图像流，以确定必须显示哪个图像。

```
import {
  LitElement,
  customElement,
  property,
  html
} from 'lit-element'
import styles from './index.pcss'
import { Story } from '../story'
import '../progress'

@customElement('wc-stories')
class WCStories extends LitElement {

  /**
   * @description
   * Total time in view of each image
   */
  @property({ type: Number }) duration = 5000

  /**
   * @description
   * Array of images to show. This must be URLs.
   */
  @property({ type: Array }) images: string[] = []

  /**
   * @NoImplemented
   * @description
   * Effect of transition.
   * @version 0.0.1 Only support for fade effect.
   */
  @property({ type: String }) effect = 'fade'

  /**
   * @description
   * Initial index of image to show at start
   */
  @property({ type: Number }) startAt = 0

  /**
   * @description
   * Enables or disables the shadow of the container
   */
  @property({ type: Boolean }) withShadow = false

  @property({ type: Number }) height = 480

  @property({ type: Number }) width = 320

  /**
   * Handles the animationend event of the
   * <progress> animation variable.
   */
  private handler = {
    onAnimationEnd: () => {
      this.startAt = 
        this.startAt < this.children.length - 1
        ? this.startAt + 1
        : 0
      this.renderNewImage()
    }
  }

  /**
   * When tap on left part of the card,
   * it shows the previous story if any
   */
  goPrevious = () => {
    this.startAt = 
      this.startAt > 0
      ? this.startAt - 1
      : 0
    this.renderNewImage()
  }

  /**
   * When tap on right part of the card,
   * it shows the next story if any, else
   * shows the first one.
   */
  goNext = () => {
    this.startAt = 
      this.startAt < this.children.length - 1
      ? this.startAt + 1
      : 0
    this.renderNewImage()
  }

  render() {
    return html`
      <wc-stories-progress
        segments="${this.images.length}"
        duration="${this.duration}"
        current="${this.startAt}"
        .handler="${this.handler}"
      >
      </wc-stories-progress>
      <section class="touch-panel">
        <div @click="${this.goPrevious}"></div>
        <div @click="${this.goNext}"></div>
      </section>
      <!-- Children -->
      <slot></slot>
      <style> ${styles.toString()} :host {
          box-shadow: ${
            this.withShadow
            ? '0 14px 28px rgba(0,0,0,0.25), 0 10px 10px rgba(0,0,0,0.22);'
            : 'none;'
          } height: ${this.height}px;
          width: ${this.width}px;
        }
      </style>
    `
  }

  firstUpdated() {
    this.renderNewImage()
  }

  /**
   * Iterate over children stories to know
   * which story we need to render.
   */
  renderNewImage() {
    Array.from(this.children).forEach((story: Story, i) => {
      if (story instanceof Story) {
        story.visible = this.startAt === i
      }
    })
  }
}

export { WCStories } 
```

Enter fullscreen mode Exit fullscreen mode

我们的主要组件通过一些属性接受初始配置:

*   `duration`:图像可见的时间。
*   `startAt`:启动时显示的图像。
*   `height`:不言自明。
*   `width`:不言自明。
*   `withShadow`:启用或禁用投影。

此外，它还有一些控制过渡流的方法:

*   `goPrevious`:显示上一张图片。
*   `goNext`:显示下一幅图像。
*   `renderNewImage`:遍历 stories 组件，通过索引和`startAt`属性之间的比较，决定必须显示哪个图像。

所有的故事都是这个组件的子组件，放在一个槽中:

```
<!-- Children -->
<slot></slot> 
```

Enter fullscreen mode Exit fullscreen mode

当影子 DOM 被构建时，所有的子对象都将被插入到插槽中。

## 🚀该跑了！

在项目根目录下的`demo/`文件夹中创建一个`index.html`文件，内容如下:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <!-- Open Sans font -->
  <link href="https://fonts.googleapis.com/css?family=Noto+Sans" rel="preload" as="font">
  <link href="https://fonts.googleapis.com/css?family=Noto+Sans" rel="stylesheet">
  <!-- CSS reset -->
  <link href="https://necolas.github.io/normalize.css/8.0.1/normalize.css" rel="stylesheet">
  <!-- polyfills -->
  <script src="https://unpkg.com/web-animations-js@2.3.1/web-animations.min.js"></script>
  <script src="https://unpkg.com/@webcomponents/webcomponentsjs@2.2.7/custom-elements-es5-adapter.js"></script>
  <script src="https://unpkg.com/@webcomponents/webcomponentsjs@2.2.7/webcomponents-loader.js"></script>
  <!-- our script -->
  <script defer src="index.js"></script>
  WC Stories
  <style>
  .container {
    display: flex;
    justify-content: center;
    padding: 50px;
  }
  </style>
</head>
<body>
  <main class="container">
    <wc-stories height="480" width="320" withShadow>
      <wc-stories-story src="img/01.jpg"></wc-stories-story>
      <wc-stories-story src="img/02.jpg"></wc-stories-story>
      <wc-stories-story src="img/03.jpg"></wc-stories-story>
      <wc-stories-story src="img/04.jpg"></wc-stories-story>
      <wc-stories-story src="img/05.jpg"></wc-stories-story>
    </wc-stories>
  </main>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

按住这个位置，创建一个名为`img/`的文件夹，里面粘贴一些图片。请注意，您需要将每个图像映射为一个`<wc-stories-story>`组件。在我的例子中，我有 5 个图像，分别叫做 01.jpg 的*、*、02.jpg 的*等等。*

一旦我们完成了这一步，我们就可以启动我们的开发服务器了。运行`yarn start`命令，进入 *localhost:4444* 。你会看到像这样的东西。

## ✈️奖金:权威证明

web 组件的主要目标是**创建可重用的 UI 块，它可以在任何基于 Web 的平台上工作**，这当然**包括前端框架**。所以，让我们看看这个组件是如何在主流框架上工作的:React、Angular 和 vue。

### 做出反应

[https://stackblitz.com/edit/react-wcstories?embed=1&view=preview&](https://stackblitz.com/edit/react-wcstories?embed=1&view=preview&)

### 视图

[https://stackblitz.com/edit/vue-wcstories?embed=1&view=preview&](https://stackblitz.com/edit/vue-wcstories?embed=1&view=preview&)

### 棱角分明

[https://stackblitz.com/edit/angular-wcstories?embed=1&view=preview&](https://stackblitz.com/edit/angular-wcstories?embed=1&view=preview&)

酷！是作品！😃 🎉

* * *

## 🤔结论

建议:**学习、采纳、使用、编写** Web 组件。你可以把它和普通的 JS 或者框架一起使用。**是本地的和标准化的**，容易理解和书写吗🤓，强大💪并有出色表现的⚡.