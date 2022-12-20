# 在 Dojo 中构建简单的应用程序

> 原文：<https://dev.to/odoenet/building-a-simple-app-in-dojo-34ai>

我一直在思考如何在 hello world 应用程序或 Todo 应用程序之外构建一个基本的 Dojo 应用程序。在 [dojo/examples](https://github.com/dojo/examples) repo 中有一些非常好的样本。然后我遇到了[这个用于搜索表情符号的 react 应用](https://github.com/ahfarmer/emoji-search)，他不需要经常搜索表情符号，所以我知道我找到了我的演示。另外，[代码沙箱](https://codesandbox.io/)上的 dojo 模板现在默认使用 TSX/JSX。

因为 dojo template 应用程序默认使用 JSX，所以这个示例几乎是 react 示例的完全一对一。我不会一行一行地深入这个应用程序的细节，但是我想涵盖它所展示的一些核心概念。

### 获取元

Dojo 中的 Meta 是关于小部件的元信息。相当元吧？

当您构建 Dojo 小部件时，您永远不会接触到应用程序的输出 HTML。没有小部件方法来获取对 DOM 的引用。这可以防止您无意中更改 Dojos 虚拟 DOM 引擎引用的 DOM 元素，这是很糟糕的。所以不要太疯狂了。但是在应用程序中访问 DOM 节点是有正当理由的。在我的表情符号应用程序中，我使用了一个名为 [clipboardjs](https://clipboardjs.com/) 的小库，让我可以从我的应用程序中将表情符号复制到我的剪贴板上。这个库需要我传递一个 DOM 节点，它将使用复制数据到剪贴板。

你可以通过一个[元](https://dojo.io/docs/index.html#doc--dojo__framework__v5_0_1__src__widget-core__README_md___meta-configuration)在 Dojo is 中获得这些信息。Dojo 为您提供了一些现成的元，如[维度](https://dojo.io/docs/index.html#doc--dojo__framework__v5_0_1__src__widget-core__README_md___dimensions)、[动画](https://dojo.io/docs/index.html#doc--dojo__framework__v5_0_1__src__widget-core__README_md___animations)、[交集](https://dojo.io/docs/index.html#doc--dojo__framework__v5_0_1__src__widget-core__README_md___intersection)等等。您可以使用`@dojo/framework/widget-core/meta/Base`实现自己的定制元来访问 DOM 节点。

```
// src/widgets/ElementMeta.ts
import { Base as MetaBase } from "@dojo/framework/widget-core/meta/Base";

class ElementMeta extends MetaBase {
  get(key: string): Element {
    const node = this.getNode(key);
    return node as Element;
  }
}

export default ElementMeta; 
```

Enter fullscreen mode Exit fullscreen mode

元实现了一个`get()`方法，该方法将通过给定的键获取 DOM 节点并返回该 DOM 节点。现在，在我使用 clipboardjs 的应用程序中，我可以将我的 meta 与小部件的`this.meta()`方法结合使用，以获得一个被引用的 DOM 节点。

```
// src/widgets/EmojiResultsRow.tsx
import { tsx } from "@dojo/framework/widget-core/tsx";
import { WidgetBase } from "@dojo/framework/widget-core/WidgetBase";

import * as css from "./styles/EmojiResultsRow.m.css";

import ElementMeta from "./ElementMeta";
import * as Clipboard from "clipboard";

export interface EmojiResultsRowProperties {
  title: string;
  symbol: string;
}

export class EmojiResultsRow extends WidgetBase<EmojiResultsRowProperties> {
  clipboard: Clipboard = null;

  onAttach() {
    // use my meta to get a DOM node
    const element = this.meta(ElementMeta).get(this.properties.title);
    this.clipboard = new Clipboard(element);
  }
  onDetach() {
    this.clipboard.destroy();
  }

  protected render() {
    const { title, symbol } = this.properties;
    const codePointHex = symbol.codePointAt(0).toString(16);
    const src = `//cdn.jsdelivr.net/emojione/assets/png/${codePointHex}.png`;
    // provide a `key` property to my widget element to
    // reference with my meta
    return (
      <div
        key={title}
        classes={[css.root, "copy-to-clipboard"]}
        data-clipboard-text={symbol}
      >
        <img alt={title} src={src} />
        <span classes={[css.title]}>{title}</span>
        <span classes={[css.info]}>Click to copy emoji</span>
      </div>
    );
  }
}

export default EmojiResultsRow; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我可以使用我的自定义元来获取由我的小部件创建的 DOM 节点。这使得对输出 DOM 节点的访问更加灵活，但也避免了我无意中搬起石头砸自己的脚。如果我打破了我的 DOM，这完全是我的错。

### 核心部件

Dojo 提供了一套[小部件](https://github.com/dojo/widgets/)，您可以在自己的应用程序中使用。这包括像[时间选择器](https://github.com/dojo/widgets/blob/master/src/time-picker/README.md)、[选择](https://github.com/dojo/widgets/blob/master/src/select/README.md)和[布局控件](https://github.com/dojo/widgets/#layout-widgets)这样的项目。对于我的应用程序，我对可以用于搜索的输入感兴趣。每次更新 input 元素时，我都想过滤应用程序中显示的表情列表。所以我将包装一个 [TextInput](https://github.com/dojo/widgets/blob/master/src/text-input/README.md) 小部件，这样我就可以管理一些本地状态并将输入的值传递给一个过滤方法。

```
// src/widgets/SearchInput.tsx
...
export class SearchInput extends WidgetBase<SearchInputProperties> {
  @watch() private searchValue = "";

  private onChange(value) {
    if (!value) {
      return;
    }
    this.searchValue = value;
    const { handleChange } = this.properties;
    handleChange(value);
  }

  protected render() {
    return (
      <div classes={[css.root]}>
        <div>
          <TextInput
            placeholder="Search for emoji"
            value={this.searchValue}
            onInput={this.onChange}
          />
        </div>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，我可以在这里使用常规的`<input type="text" />`,但是 TextInput 非常方便，因为它已经有一个我可以使用的直接传递输入值的`onInput`方法，而不是我需要做的事件`event.target.value`,因为我很懒，我真的很欣赏它。然后，我需要使用一个`keyup`事件，并可能对不同的键进行一些处理，以确定我是否想要获取我的值，为什么要这么麻烦，因为 Dojo 已经提供了一种很好的方法来做这件事。

我还利用 [`@watch`](https://dojo.io/docs/index.html#doc--dojo__framework__v5_0_1__src__widget-core__README_md___internal-widget-state) 装饰器来管理小部件中的本地状态。我在这里更详细地谈到了这个方法[。这使得随时管理我的输入值变得非常简单。](https://learn-dojo.com/watch-for-property-changes-in-widgets/)

您可以在这里看到完整的应用程序。

您可以看到，在 Dojo 中构建应用程序为您提供了一些安全性和灵活性，让您可以拼凑出构建可靠、出色的应用程序所需的一切。Dojo 不再仅仅是一个工具包，它是一个成熟的框架，可以提供很多功能！

请务必[订阅时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！