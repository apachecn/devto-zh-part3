# 如何在自己的服务器上托管谷歌字体

> 原文：<https://dev.to/kelli/how-to-self-host-google-fonts-on-your-own-server-d4i>

谷歌建议你使用他们在网站上提供的代码，在他们的 [FAQ](https://developers.google.com/fonts/faq) 中使用谷歌字体，但是如果你想完全控制显示的字体，在你自己的服务器上自托管谷歌字体是一个选择。

## 为什么要自宿主谷歌字体

有人可能会认为，使用谷歌字体的网站上显示的字体在选择字体后不会改变，但它可能会在你不知情的情况下改变。因为 Google 提供最新版本的字体，所以该字体可能会在没有任何通知的情况下随时更改为新版本([发生这种情况的问题示例](https://github.com/google/fonts/issues/1307))。我怀疑会有剧烈的变化，但有可能谷歌提供的字体会有你不喜欢的变化。

性能是开发者自托管谷歌字体的另一个原因，然而一些开发者说自托管谷歌字体更快，一些人说不是。一方面，你在世界各地都有谷歌优化的服务器，另一方面，你很少去第三方服务器。这有点简化了，但也许这是你必须自己测试的事情之一。

在我的例子中，我尝试自托管一个谷歌字体，以帮助解决用 Edge 加载我的网站时发生的 FOUT(无样式文本闪烁)问题。它没有解决我的问题，但我很高兴我在试图解决它的同时学会了如何自托管谷歌字体。

## 从哪里获得自托管的谷歌字体

你在[fonts.google.com](https://fonts.google.com/)找到的字体会有一个下载按钮，让你下载字体，但是你只能得到扩展名为`.ttf`的字体文件。对于现代浏览器，你可能会想要使用压缩字体的`.woff2`和`.woff`版本，它们可能会加载得更快。你可以从[google-webfonts-helper.herokuapp.com](https://google-webfonts-helper.herokuapp.com)获得`.woff`和`.woff2`版本的谷歌字体，这不是一个官方的谷歌网站，但是可以从[谷歌/字体 GitHub 知识库自述文件](https://github.com/google/fonts)链接到一个地方，在那里可以获得关于自托管的帮助。

## 自托管谷歌字体的步骤

*注意:在自托管字体之前，最好阅读字体许可证，并确保您可以遵循字体的许可证要求。请参阅下面的“注意遵守字体许可”。*

*   进入 [google-webfonts-helper](https://google-webfonts-helper.herokuapp.com) 并选择您想要下载的字体。
*   在“复制 CSS”下，选择“现代浏览器”来支持使用`.woff`和`.woff2`的当前浏览器，或者选择“最佳支持”来支持旧浏览器。
    *   如果你需要帮助来决定选择哪一个以及如何定制`@fontface`块来匹配你的站点的支持级别，请参见[CSS-Tricks-Using @ font-face](https://css-tricks.com/snippets/css/using-font-face/)。
*   复制给定字体的 CSS，并将其添加到适合您站点的样式表中。
    *   如果需要，您可以将[字体显示](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display)属性添加到`@font-face`块中，以帮助控制支持它的浏览器上的 FOUT(非样式文本的闪烁)和 FOIT(不可见文本的闪烁)(如果您不熟悉 CSS 字体呈现控件的`font-display`和[caniuse.com，请参见](https://caniuse.com/#feat=css-font-rendering-controls)[面向大众的字体显示](https://css-tricks.com/font-display-masses/)，以了解浏览器支持的信息)。注意 IE 和 Edge 是不支持它的浏览器之一。
*   在“下载文件”下，点击按钮下载字体的 zip 文件，然后提取文件并将其添加到您的项目中。

### 注意遵循字体许可

注意:这是我找到的关于谷歌字体许可的信息，希望对你有所帮助，但我不是律师，这也不是法律建议。你应该自己阅读字体许可信息，因为你有责任遵守它。

当你从 [google-webfonts-helper](https://google-webfonts-helper.herokuapp.com) 下载文件时，它不包含带有字体许可的文本文档。在下载按钮下面，写着“查看[谷歌字体开源字体归属](https://fonts.google.com/attribution)了解该字体使用的具体许可。”这个链接并不指向该字体的实际文本文档许可证，所以我深入查找了一下。

根据 [google/fonts repo](https://github.com/google/fonts) ，它在目录中为每个字体家族都有一个许可证，所以你可能想在这个 repo 中找到你的字体的许可证并阅读它。我照做了，这让我想在目录中添加一份我正在使用的字体的许可证副本。

#### 举例

由于我在寻找使用 SIL 开放字体许可的 Quattrocento Sans 字体，我在 [google/fonts repo](https://github.com/google/fonts) 中的`ofl/quattrocentosans`下找到了许可文本文件。许可文件被命名为`OFL.txt`，在顶部有这个特殊字体的版权信息。

我看了执照，上面写着一件事

> 2)字体软件的原始或修改版本可以与任何软件捆绑、
> 再分发和/或销售，前提是每个副本
> 包含上述版权声明和本许可证。这些可以是作为独立的文本文件、人类可读的标题包含的
> ,或者是文本或
> 二进制文件中适当的机器可读元数据字段中的
> ,只要这些字段能够被用户容易地查看。

因为我要把字体放在 GitHub repo 中，这可能算作重新发布，所以我把字体的许可文件的副本`OFL.txt`放在我的 repo 中的字体目录中。

## 总结

自托管谷歌字体可能不适合所有人，但如果你真的在乎一个字体永远不会在你不知情的情况下改变到一个新版本，那么它可能是要走的路。如果你想自己托管谷歌字体来让你的网站更快，你可能需要做一些测试，看看它是否真的更快。

无论你是出于什么原因自托管谷歌字体，你都可以从 [google-webfonts-helper](https://google-webfonts-helper.herokuapp.com) (非官方网站)下载，并按照步骤将它们添加到你的网站。阅读并遵循所用字体的许可也是一个好主意。