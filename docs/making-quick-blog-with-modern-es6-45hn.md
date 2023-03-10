# 用现代 ES6 制作快速博客

> 原文：<https://dev.to/tonis2/making-quick-blog-with-modern-es6-45hn>

浏览器引擎已经有了巨大的发展，我想分享一些如何快速构建一个`JavaScript`单页面应用程序的想法，
使用很酷的浏览器特性，比如

> [异步功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
> [自定义元素 V1](https://developers.google.com/web/fundamentals/web-components/customelements)
> [ES6 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)

这将是一个非常基本的项目，它可能需要 15 分钟才能运行，我们不需要安装任何`node_modules`只需编写代码，嘣它就准备好了。

你可以在这里查看`live`成绩[，在这里](https://tonis2.github.io/Custom-elements-blog/)查看完整`code`T4

为了开发你应该使用一个`server`为你的`index.html`服务，你可以使用任何你喜欢的东西，比如用`node.js`你可以使用 [http-server](https://www.npmjs.com/package/http-server)

好了，我们开始吧

* * *

## 基本设置

首先让我们创建一些基础文件，我们确实需要`index.html`，所以让我们从那里开始。

在你的项目文件夹中创建一个名为`index.html`的新文件

并给它添加一些内容

`index.html`

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    My blog
    <link rel="stylesheet" href="Application/styles/index.css">
</head>
<body>
   <div id="root"></div>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/webcomponentsjs/1.2.0/webcomponents-lite.js"></script>
<script type="module" src="Application/App.js"></script>
</html> 
```

我们有一些基本的东西，比如，**标题**，**样式表**，**根**，以及我们的**脚本**标签，我还将`webcomponents-lite.js`作为一个 polyfill 包含进来，以增加对`webcomponents`的浏览器支持，因为它们在一些浏览器上仍然缺乏[支持](https://caniuse.com/#search=custom%20elements)。

让我们把注意力集中在`script`标签上，我们在那里有`type=module`，如果我们想在我们的`JS`文件中激活`ES6 modules`支持，我们需要使用它，所以不要忘记它。

```
<script type="module" src="Application/App.js"></script> 
```

接下来，让我们创建我们的文件夹结构。

让我们把它做成这样

*   应用
    *   成分
    *   模块
    *   风格
    *   App.js

很好，现在让我们继续写作

* * *

## JavaScript 文件设置

我们的应用程序从`App.js`开始，所以让我们先创建它。

把这一切复制到你的`App.js`

```
import Router from "https://unpkg.com/navigo@6.0.2/src/index.js";
import { HTML } from "https://unpkg.com/kelbas";

const router = new Router();

router.on("/", () => {
  const element = HTML`<h2>Hello</h2>`;
  document.querySelector("#root").replaceWith(element);
});

router.on("/post/:id", params => {});
router.resolve(); 
```

目前，当我们打开我们的应用程序时，我们应该看到 *Hello*

我将解释一下我们的图书馆是做什么的。

对于`Router`我们将使用 [navigo](https://github.com/krasimir/navigo) ，这是一个非常轻量级的浏览器路由库。

然后我们有了由我自己创建的 [HTML](https://github.com/tonis2/kelbas) 库，它扩展了使用 [ES6 模板字符串](https://wesbos.com/template-strings-html/)来创建`HTML`元素的想法。

接下来，在我们的`components`文件夹中，让我们创建 3 个文件`home.js`、`post.js`和`index.js`

所以我们的组件文件夹看起来像这样

*   成分
    *   home.js
    *   索引. js
    *   post.js

在我们的`index.js`中，让我们只`export`另外两个文件，这样我们以后就可以很容易地导入我们所有的组件。

`index.js`

```
export { default as home } from "./home.js"
export { default as post } from "./post.js" 
```

在我们开始构建我们的*页面*之前，我们将添加一个东西。

在我们的模块文件夹中，让我们创建一个名为`Element.js`的文件

*   应用
    *   模块
        *   Element.js

我们的`Element.js`将会是原生`HTMLElement`的包装器，我推荐使用一个简单的包装器，这样我们就可以用`Custom elements`为我们的开发增压

在新创建的`Element.js`中添加

`Element.js`

```
export default class Element extends HTMLElement {

    //Element is connected
    async connectedCallback() {
        this.install.apply(this)

        this.beforeRender()
        const element = await this.render()
        this.appendChild(element)

        this.installed()
    }

    //Element is removed
    disconnectedCallback() {
        this.uninstall()
    }

    //Update element
    async update() {
        const element = await this.render()
        this.beforeUpdate()
        this.lastChild.replaceWith(element)
        this.afterUpdate()
    }

    //You can use these function to run your functions between component state changes, like with react.js
    install() { }

    installed() { }

    uninstall() { }

    beforeUpdate() { }

    afterUpdate() { }

    beforeRender() { }
} 
```

* * *

## 创建 Web 组件页面

现在我们已经完全设置好了我们的项目，我们可以开始实现页面，因为这是一个简短的教程，我将使用 [JSON-Placeholder](https://jsonplaceholder.typicode.com/) 作为我的`data`。

我们只要打个电话就能把`posts`拉进来

```
fetch('https://jsonplaceholder.typicode.com/posts/')
  .then(response => response.json()) 
```

让我们给我们的`/components/home.js`增加一些`JavaScript`

`home.js`

```
import { HTML } from "https://unpkg.com/kelbas"
import Element from "../modules/Element.js"

export default class Home extends Element {
    async getPosts() {
       return await fetch("https://jsonplaceholder.typicode.com/posts").then(res => res.json())
    }

    open(id) {
        this.router.navigate(`/post/${id}`)
    }

    async render() {
        const posts = await this.getPosts()
        return HTML`<section id="posts-container"> ${posts.map((post) => {
                            return HTML`<div class="post" onclick=${this.open.bind(this, post.id)}>
                                                <h3 class="post-title">${post.title}</h3>
                                                <p class="post-body">${post.body}</p>
                                        </div>`
                         })} </section>`
    }
} 
```

首先我们添加我们需要的库，我们`import`之前创建的`Element.js`以及我之前提到的 [HTML](https://github.com/tonis2/kelbas) 解析器。

如果你已经用`React.js`开发过，那么这看起来很熟悉，在我看来这很棒，因为我们不需要捆绑`JSX`来渲染它，它基本上有相同的`API`。

我们有一个[异步](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)函数来拉`data`、
然后在`render`函数中我们首先等待数据，然后显示检索到的`data`，非常简单。
我们可以很容易地在这里添加一个`Loading...`指示器，但为了简单起见，这次我忽略了它。

还有函数`open`，目前只是添加，我们会在后面的`App.js`中实现`this.router`

接下来让我们添加一些`JavaScript`到我们的`/components/post.js`

`post.js`

```
import { HTML } from "https://unpkg.com/kelbas"
import Element from "../modules/Element.js"

export default class Post extends Element {
    async getPost() {
       return await fetch(`https://jsonplaceholder.typicode.com/posts/${this.post}`).then(res => res.json())
    }

    async render() {
        const {title, body} = await this.getPost()

        return HTML`<section id="post-container">
                        <h2>${title}</h2>
                        <p>${body}</p>
                    </section>`
    }
} 
```

这与`home.js`基本相同，但它将加载单个 post 数据，注意`getPost`函数中的`this.post`值，我们将在`App.js`将它作为`id`添加到`router`中

现在让我们回到我们的`App.js`，我们将实现我们新创建的组件并更新路线

将所有这些复制到您的`App.js`

`App.js`

```
 import Router from "https://unpkg.com/navigo@6.0.2/src/index.js";
import { HTML } from "https://unpkg.com/kelbas";

const router = new Router();

import * as component from "./components/index.js";

for (let key in component) {
  component[key].prototype.router = router;
  customElements.define(`${key}-element`, component[key]);
}

router.on("/", () => {
  const element = HTML`<home-element id="root"></home-element>`;
  document.querySelector("#root").replaceWith(element);
});

router.on("/post/:id", params => {
  const element = HTML`<post-element id="root"></post-element>`;
        element.post = params.id
  document.querySelector("#root").replaceWith(element);
});

router.resolve(); 
```

首先我们`import`我们所有的`components`来自`/Application/components/index.js`

然后，我们将我们的`router`附加到我们所有的`components`上，这样我们就可以很容易地从`component`
中更改路线，我们还将 customElements 定义为`file-name + element`，这样我们的`home.js`就变成了`custom-elemets`注册表中的`home-element`。

在定义了`customElements`之后，你可以将`<home-element></home-element>`附加到你的`index.html`上，它就会工作了，多酷啊。

我们也更新了我们的路线，那里发生的是当`route`被解雇时，我们将创建我们的`HTML` `customElement`，我们之前的`imported`和`defined`。

然后我们将在`index.html`中的`<div id="root"></div>`元素上使用 [replaceWith](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/replaceWith) 。

还要确保给你的元素一些类似于`id`或`key`的东西，这样我们就可以在每次路线改变时找到我们想要替换的元素，这就是为什么`home-element`和`post-element`也有`id=root`的原因。

现在是最后一部分，让我们添加我们的设计。

## 添加样式

在您的`styles`文件夹中

*   应用
    *   风格

让我们添加`index.css`并用一些简单的`css`填充它

`index.css`

```
@import "https://unpkg.com/mustard-ui@latest/dist/css/mustard-ui.min.css";
@import "https://cdnjs.cloudflare.com/ajax/libs/normalize/8.0.0/normalize.min.css";

body {
  width: 60%;
  margin: 0 auto;
  background: #e67e22;
}
#posts-container {
  display: grid;
  grid-template-columns: 1fr;
  grid-row-gap: 20px;
  margin: 50px 0;
}

#posts-container .post {
  background: #ecf0f1;
  padding: 10px 15px;
  cursor: pointer;
}

#post-container {
  display: grid;
  grid-template-columns: 1fr;
  grid-row-gap: 20px;
  margin-top:50px;
}

#post-container h2 {
  color: #ecf0f1;
}

#post-container p {
  color: #ecf0f1;
  background: #d35400;
  padding: 20px;
} 
```

如果你遇到了麻烦，你可以在这里查看完整的代码

我希望我的教程是有帮助的，并在评论中给出你的想法。