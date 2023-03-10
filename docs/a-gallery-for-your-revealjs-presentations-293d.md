# reveal.js 演示的图库

> 原文：<https://dev.to/mattdark/a-gallery-for-your-revealjs-presentations-293d>

5 年前开始用 **[reveal.js](https://revealjs.com)** 。这是一个使用 HTML 创建演示文稿的框架。它是微软 PowerPoint 或 LibreOffice Impress 的替代产品，但使用文本编辑器和浏览器。

演示文稿中的每张幻灯片都被放入一个 **`<section>`** 标签中，并且全部内容都在一个 **`<div>`** 标签与分配的类别**幻灯片**之间。

```
<div class="slides">
  <section>
    Slide 1
  </section>
  <section>
    Slide 2
  </section>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

根据 [reveal.js](https://github.com/hakimel/reveal.js) 的 **[GitHub](https://github.com/)** 库，您可以使用 **Markdown** 来格式化演示内容，方法是将 **data-markdown** 属性添加到您的**元素中，并将内容包装在 **`<textarea data-template>`** 中，如下例所示。** 

```
<section data-markdown>
  <textarea data-template>
    ## Page title
    A paragraph with some text and a [link (http://hakim.se).
  </textarea>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

如果您决定使用 Markdown，您可以用**创建一个文件。md** 扩展，添加**数据降价**属性如下。

```
<div class=”slides”>
  <section data-markdown="slides.md" data-charset="utf-8">
  </section>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

当你要创建一个新的演示文稿时，你需要将 **reveal.js** 的所有文件复制到另一个文件夹，创建一个 Markdown 文件并添加幻灯片的内容。每次你做演讲或参加研讨会时，你都需要找到保存你的演示文稿的文件夹，并在你最喜欢的浏览器中打开 index.html 的**文件。**

如果你把所有的幻灯片都放在一个地方会怎么样？带着这个问题，我在 2018 年 1 月开始开发一个 web 应用程序。最初开发用 **[Python](https://python.org/)** 和 **[Flask](http://flask.pocoo.org/)** 做后端，用 **[UIKit](https://getuikit.com/)** 做前端。

**[reveal.js Gallery](https://github.com/mattdark/reveal.js-gallery)** 的存在是为了更好地从一个地方组织和访问您的所有演示文稿。

这是一个周末项目，该应用程序的第一个版本显示了一个如下所示的表单，其中列出了您所有的演示文稿。

[![reveal.js gallery](img/36c36e385cf88f81980cd57fe8ac7291.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bm3BM0bq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A9S3kjNaHi2PSz9jgGgjsEw.png)

选择演示文稿并按下按钮“**打开**”后，幻灯片被加载到浏览器中。

[![reveal.js slides](img/62616a83758da5d85df61a7d599bcdc6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DVRtrHYe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AS0o1_3d7_qL7B1huOhStTQ.png)

然后在 2018 年 3 月，我花了 2 天时间，完成了从 **Python** 到 **[Rust](https://rust-lang.org/)** (Mozilla 的编程语言)的迁移。我用的是 **[Rocket](https://rocket.rs/)** ，这是一个 **Rust** 的 web 框架

那个月我在 **[Medium](https://medium.com/@mattdark/presentation-manager-written-in-rustlang-f36b73bb8dd2)** 上发表了一篇文章，列出了一些需要完成的任务，比如:

*   设计新的用户界面
*   编写文档

2018 年 12 月 25 日，我在 GitHub 上发送了对资源库的更改，这是一个新版本的应用程序，具有新的用户界面、新的许可证、行为准则、贡献指南和一个包含项目一般信息的自述文件。

## 变化

在用**蟒蛇**和**烧瓶**开发的第一个版本和用**铁锈**和**火箭**建造的最新版本之间，这个项目已经做了一些重要的改变。

*   一开始选择的许可证是 **GNU 通用公共许可证 v3.0** ，后来改成了 MIT 许可证，我认为这个许可证更适合这个项目，因为 **reveal.js** 和 **UIKit** 是在同一个许可证下发布的。

*   投稿指南。GitHub 存储库上的 **CONTRIBUTING.md** 文件中提供了关于如何为项目做贡献的信息。

*   行为准则。改编自 **[投稿人盟约](https://www.contributor-covenant.org/)**1.4 版，可在[https://www . Contributor-Covenant . org/version/1/4/code-of-conduct . html](https://www.contributor-covenant.org/version/1/4/code-of-conduct.html)查阅。

*   基于 **UIKit** 模板的新 UI。

*   。gitignore 文件已添加。

*   **reveal.js** 和 **UIKit** 文件被从 **/static** 中的 **css** 和 **js** 目录中移除。创建了一个 bash 脚本来获取这两个框架的最新发布版本。

*   与 Rust 2018 的兼容性以及对代码的改进。在 Cargo.toml 文件中进行的修改。

*   支持演讲者注释和多路复用。

*   通过在地址栏中写入完整路径，为每个演示文稿和访问分配单独的 URL。

## 新界面

新的用户界面(UI)是基于 **[仪表板](https://zzseba78.github.io/Kick-Off/dashboard.html)** 模板由 **[启动](https://github.com/zzseba78/Kick-Off)** 为 **UIKit** 。左侧边栏显示社交网络链接、个人资料图片和姓名。

[![reveal.js gallery UI](img/9c49f5e3f0159e5d2983f429d7f14eba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e6tX014M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mattdark/reveal.js-gallery/screenshots/screenshot/reveal.js-gallery.png)

### 配置

可以通过编辑 **/static/data/** 中的 **social.json** 文件，写入用户名而不是完整的 URL(例如“Twitter”:Twitter)来更新社交媒体档案。

```
[  {  "facebook":  "#",  "twitter":  "#",  "linkedin":  "#",  "github":  "#"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

通过修改第 29 行，可以在 **/src** 中的 **main.rs** 文件中更改名称。

```
 ...
      let name = "John Doe";
  ... 
```

Enter fullscreen mode Exit fullscreen mode

个人资料图片( **avatar.svg** )位于 **/static/img** 。用类似 **[Inkscape](https://inkscape.org/)** 的工具编辑这个文件。

## 特性

一、个人网址。您可以通过在您喜爱的浏览器的地址栏中写入其 URL 来访问演示文稿(例如[http://localhost:8000/presentation/slide 1](http://localhost:8000/presentation/slide1))。

二。个人主题。对画廊中的每个演示文稿使用不同的主题。

三。演讲者注释。增加了对演讲者注释的支持。按键盘上的' s '打开演讲者注释窗口。

四。多路复用。这是我最喜欢的 reveal.js 插件之一，它在 gallery 应用程序中进行了测试。说明将很快添加到自述文件中。

## 入门

这些指令将让您在本地机器上启动并运行项目的副本。

### 先决条件

一、安装 Rust，在 GNU/Linux 或 Mac 上:

```
curl https://sh.rustup.rs -sSf | sh 
```

Enter fullscreen mode Exit fullscreen mode

对于任何其他操作系统，进入 **[rustup.rs](https://rustup.rs/)** 。

这个项目是使用 Rocket 构建的，Rocket 是一个用 Rust 编写的 web 框架，它使用该语言的夜间版本。

二。每晚安装 Rust:

```
$ rustup install nightly 
```

Enter fullscreen mode Exit fullscreen mode

三。克隆 **reveal.js 图库**
的资源库

```
$ git clone https://github.com/mattdark/reveal.js-gallery.git 
```

Enter fullscreen mode Exit fullscreen mode

四。将夜间设置为默认

```
$ cd reveal.js-gallery
$ rustup override set nightly 
```

Enter fullscreen mode Exit fullscreen mode

动词 （verb 的缩写）将 UIKit 和 reveal.js 复制到静态目录:

```
$ sh installer.sh 
```

Enter fullscreen mode Exit fullscreen mode

### 新建演示文稿

I .用您的演示内容创建一个 Markdown 文件，指定一个可识别它的名称，并将其保存在/static/slides 中。该目录中有两个示例。而且你可以在[revealjs.com](https://revealjs.com/)上查看 reveal.js 的文档，在 [GitHub](https://github.com/hakimel/reveal.js) 上查看它的资源库。

*   注意:`---`用作水平分隔符，`----`用作垂直分隔符。要添加注释，只需将它们放在`Notes:`之后。你也可以对你的笔记使用 Markdown。

```
Slide 1

Note:
This is the first slide
 --- 
Slide 2

Note:
- This is the second slide 
```

Enter fullscreen mode Exit fullscreen mode

二。使用 reveal.js 主题，或者创建自己的主题并放在/static/reveal.js/css/theme 中。

三。对每个演示文稿的第一张幻灯片进行截图，并保存在/static/img/screen 中。我推荐 Firefox 中的“截图”选项。

四。将以下信息添加到/static/data 中的 slides.json。有一个 slides.json 示例文件。

*   **id** -分配用于识别每个演示的编号
*   **文件** -降价文件的名称
*   **标题** -演示的标题
*   **描述** -演示的描述
*   **style** -主题文件的名称
*   **url** -演示文稿的路径(如幻灯片 1)
*   **屏幕截图** -您的演示文稿的屏幕截图

### 跑步

在调试模式下:

```
$ cargo run 
```

Enter fullscreen mode Exit fullscreen mode

在释放模式:

```
$ cargo run --release 
```

Enter fullscreen mode Exit fullscreen mode

现在在你最喜欢的浏览器(我推荐 Firefox)中打开 [https://localhost:8000](https://localhost:8000) ，选择你的演示文稿。

您也可以通过在地址栏中写入完整路径来访问您的演示文稿(例如[https://localhost:8000/presentation/slide 1](https://localhost:8000/presentation/slide1))。**