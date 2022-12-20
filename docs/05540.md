# 街区的道场

> 原文：<https://dev.to/odoenet/dojo-from-the-blocks-205m>

在 [Dojo 5](https://dojo.io/blog/2019/01/29/2019-01-29-Version-5-Dojo/) 中发布的一个低调特性是引入了块。积木与[道场建造时间渲染](https://learn-dojo.com/build-time-rendering-in-dojo/)齐头并进。

块允许您在构建过程中在节点环境中运行一些任意代码。

[构建时呈现](https://dev.to/odoenet/build-time-rendering-in-dojo-i6e)是一个很棒的工具，您可以使用它来生成静态内容，而不必担心任何服务器端组件会根据请求生成页面。

例如，您可以使用块来预处理您可能想要加载到页面中的图像，或者可能是转换 markdown 以供您的博客或网站使用的更常见的用例。块为您提供了在构建过程中运行通常在服务器环境中运行的代码的灵活性。

## 积木

也许我想在 Dojo 上建立我的博客，我想在 markdown 上写我的文章。我可以使用像[摊牌](https://github.com/showdownjs/showdown)这样的库将我的减价文件解析成 HTML。这里有一个非常基本的模块可以做到这一点。

```
// src/blocks/markdown.block.ts
import * as fs from 'fs';
import { resolve } from 'path';

import { Converter } from 'showdown';

const mdConverter = new Converter();

export default function (path: string) {
  path = resolve(__dirname, path);
  const file = fs.readFileSync(path, 'utf8');
  // convert Markdown to HTML
  const html = mdConverter.makeHtml(file);
  return html
}; 
```

Enter fullscreen mode Exit fullscreen mode

块是可以在小部件中使用的[元](https://github.com/dojo/framework/tree/master/src/widget-core#meta-configuration)的类型。我可以通过调用元来使用我的块，并使用所需的参数来运行它，比如我要解析的 markdown 文件的路径。

```
import WidgetBase from "@dojo/framework/widget-core/WidgetBase";
import { dom } from "@dojo/framework/widget-core/d";
import Block from "@dojo/framework/widget-core/meta/Block";
import { tsx } from "@dojo/framework/widget-core/tsx";

import fromMarkdown from "../blocks/markdown.block";

import * as css from "./styles/About.m.css";

export default class About extends WidgetBase {
  protected render() {
    const node = document.createElement("div");
    // Use my block
    const message = this.meta(Block).run(fromMarkdown)(
      "../../markdown/post2.md"
    );
    node.innerHTML = message;
    // Create a vnode to inject my HTML
    const vnode = dom({ node });
    return (
      <div>
        <h1 classes={css.root}>About Page</h1>
        {vnode}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我现在可以天真地将解析后的 markdown 作为 HTML 注入到我的页面中。理想情况下，我希望将 HTML 转换成真正的虚拟 dom 节点，但是我还没有做到这一步。

您可以很快看到这在构建期间对处理文件是多么有用，也许可以引入一些外部文件并在应用程序中使用它们。

## 图像处理

在我的应用程序中，我可能有一些图像，我想转换成 base64 字符串，这样我就可以嵌入它们。我可以使用像 [sharp](https://github.com/lovell/sharp) 这样的工具来调整我的图片大小。当我这样做时，我可以继续创建虚拟 dom 节点并在我的块中返回它们。

```
// src/blocks/imagebase64.block.ts
import { resolve } from 'path';
import { v } from '@dojo/framework/widget-core/d';
import * as sharp from 'sharp';

export default async function (path: string) {
  path = resolve(__dirname, path);
  // resize my images
  const images = [
    await sharp(path).resize(200).toBuffer(),
    await sharp(path).resize(300).toBuffer(),
    await sharp(path).resize(400).toBuffer(),
    await sharp(path).resize(500).toBuffer()
  ];

  return images.map((a) =>
    v('img', { src: `data:image/jpeg;base64, ${a.toString('base64')}`, alt: 'my dog sally' })
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

你可能会注意到，我能够在我的块中运行异步任务。这允许我做一些更有趣的事情，比如图像处理、获取数据，或者对数据集进行某种分析，以创建图表库可以使用的格式化 json！我只是在这里抛出一些想法！

## 总结

你可以在这里查看这个样本的源代码[，你也可以在这里](https://github.com/odoe/learning-dojo-blocks)查看一个现场演示[。](https://learning-dojo-blocks.surge.sh)

Dojo 块非常有趣，我认为它们为开发人员利用 Dojo 的构建时呈现提供了一个全新的功能级别。我看不出有什么理由不使用构建时渲染，而 Blocks 为你提供了一个全新的机会，让你为之疯狂。我目前正在用 Dojo 重写我的博客！

请务必[订阅时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！