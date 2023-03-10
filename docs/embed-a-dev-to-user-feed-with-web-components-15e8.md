# 使用 Web 组件嵌入开发人员到用户的提要

> 原文：<https://dev.to/bennypowers/embed-a-dev-to-user-feed-with-web-components-15e8>

Dev.to 用户 [@healeycodes](https://dev.to/healeycodes) 上周发表了一篇可爱的文章，展示了如何使用 dev.to API 在页面中嵌入文章。

[![healeycodes](img/1388c283a86bc3f3eda94e5e89b8daac.png)](/healeycodes) [## 将您的最新开发帖子嵌入到任何地方💌

### 安德鲁希利 1919 年 4 月 6 日 4 分钟阅读

#javascript #beginners #webdev #showdev](/healeycodes/embed-your-latest-dev-posts-anywhere-lj9)

他的作品启发我建立了一个(惊喜！)web 组件，这将使事情变得更加简单。让我们构建一个开发到 web 组件！

## 概述

我们的组件将有两个部分:

1.  `<dev-feed>`，一个列表组件
2.  `<dev-article>`、一篇文章组件

主组件，`<dev-feed>`将负责获取和整理文章，`<dev-article>`将负责显示每个帖子。

我们将从构建容器开始，并逐步深入到文章显示的细节。

## 第一步:搭建脚手架

让我们使用 [open-wc](https://open-wc.org) 的工具来开始我们的组件:

1.  运行`npm init @open-wc`
2.  选择`Scaffold a new project`
3.  选择`Lit Element Web Component`
4.  输入名称`dev-feed`
5.  打开你的编辑器`atom -a dev-feed`

您将会在`src` :
下看到两个文件

```
- dev-feed.js
- DevFeed.js 
```

Enter fullscreen mode Exit fullscreen mode

第一个，在 dash-case 中，是用户为了将定制元素注册到他们的页面而导入的文件。第二个在 PascalCase 中，包含元素类，从`LitElement`扩展而来。如果你不完全清楚我说的这些东西是什么意思，可以看看我在 [lit-element](https://dev.to/bennypowers/lets-build-web-components-part-5-litelement-906) 上的帖子。没关系，我会等的。你还好吗？没问题的...

你还会看到一个演示文件夹，里面有一个 index.html。当我们编写组件时，您可以在该文件上运行 owc-dev-server 来查看您的组件的外观。Open WC 的生成器已经为我们设置好了，所以我们可以直接运行。

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

我们将实践一点自述驱动的开发，或者演示驱动的开发。也就是说，我们将首先决定元素的外部 API，并将其写入我们的演示中；然后我们将努力使代码符合我们的标准。让我们使用内置的 [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 构造函数和 lit-html 一起为我们的演示编写一个简单的专门构建的反应式渲染器，就像一种赤壁故事书。

```
const properties = new Proxy({
  // initial values
  showDescriptions: false,
  sort: 'popularity',
  username: 'bennypowers'
}, {
  /** Render the demo when a value is set */
  set(obj, prop, value) {
    obj[prop] = value
    update();
    return true
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个代理拥有我们元素属性的模型，并且它将在我们的某个值被设置时调用一个`update`函数。这个`update`函数将依次调用 lit-html 的`render`函数来有效地更新 DOM。

```
const update = ({ showDescriptions, sort, username } = properties) => render(html`
  <dev-feed id="component"
      username="${username}"
      sort="${sort}"
      ?show-descriptions="${showDescriptions}"
  ></dev-feed>

  <input id="username"
      @change="${onUsernameChange}"
      value="${username}"/>
  <input id="show-descriptions" type="checkbox"
      @change="${onShowDescriptionsChange}"
      ?checked="${showDescriptions}"/>
  <select id="sort" @change="${onSortByChange}" value="${sort}">
    <option value="popularity">Popularity</option>
    <option value="date">Date, Descending</option>
    <option value="date-asc">Date, Ascending</option>
  </select>
`, document.body); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们提供了一些控件来设置组件的属性。事件处理程序([参见 repo 以获得来源](https://github.com/bennypowers/dev-feed/blob/ee96d3e3cb2e1ed52baa5e33eba8ac2da4fd34b2/demo/index.html#L96))每个都获取相关的值并将其设置在`properties`上，这通过代理触发呈现。尼斯（法国城市名）😎。

## 实现我们的 Feed 元素

既然我们的演示已经完成，是时候设置我们的 feed 元素的内部逻辑和模板了。我们将从一个简单的实现开始，逐步发展到最终产品，并在发展过程中不断刷新我们的演示应用程序。

第一步也是最容易的一步是定义我们观察到的属性。

```
static get properties() {
  return {
    loading: { type: Boolean },
    posts: { type: Array },
    showDescriptions: { type: Boolean, attribute: 'show-descriptions' },
    sort: { type: String, reflect: true },
    username: { type: String },
  }
}

constructor() {
 super();
 this.posts = [];
 this.sort = 'popularity';
} 
```

Enter fullscreen mode Exit fullscreen mode

注意为`showDescriptions`指定的`attribute`，这是因为 HTML 属性总是小写，所以这里我们显式地将破折号属性与 camelCase 属性链接起来。我们还在构造函数中设置了一些默认值，特别是对于`posts`属性，这将是我们从 dev.to 获取的文章的私有列表

接下来，让我们设置提要组件的模板。与文章相比，它有相当简单的标记:

```
render() {
  const { loading, posts, postTemplate, sort } = this;
  const parseAsTimestamp = s => new Date(s).getTime();
  const sorter = (
      sort === 'popularity' ? propGt('positive_reactions_count')
    : sort === 'date' ? mapPropGt(parseAsTimestamp, 'published_at')
    : sort === 'date-asc' ? mapPropLt(parseAsTimestamp, 'published_at')
    : identity
  );

  return html`
    <div ?hidden="${!loading}">${loadingTemplate}</div>
    <ul id="posts" ?hidden="${loading}"> ${posts
        .sort(sorter)
        .map(postTemplate)} </ul>
  `;
}

postTemplate(post) {
  return html`
    <li>
      <dev-article
          .article="${post}"
          ?show-description="${this.showDescriptions}"
      ></dev-article>
    </li>`;
} 
```

Enter fullscreen mode Exit fullscreen mode

`sorter`发生了什么事？嗯，早期的 dev.to API 还没有对`GET /api/articles`的高级控制，所以我们正在做一些客户端排序。对于这个项目，我决定用一些功能性的 JavaScript 实现文章排序。`mapPropGt`和`mapPropLt`都构成了一个名为`mapPropCompare`的函数，对于两个输入值，

1.  从每个输入中获取属性
2.  在该值上映射一些函数
3.  对这两个术语应用一些比较函数

这两个版本之间的唯一区别是小于变量在应用之前将前两个参数转换为比较函数。

```
const identity = x => x;

const sub = (x, y) => x - y;

const flip = f => (y, x, ...rest) => f(x, y, ...rest);

const mapPropCompare = curry((f, g, prop, x, y) => f(g(y[prop]), g(x[prop])));

const mapPropGt = mapPropCompare(sub);

const mapPropLt = mapPropCompare(flip(sub));

const propGt = mapPropGt(identity); 
```

Enter fullscreen mode Exit fullscreen mode

关于这种编程风格的简短介绍，请查看我的幻灯片[开始功能 JavaScript](https://bennypowers.dev/starting-functional-javascript) 。

### 抓取帖子

现在我们已经建立了基本的模板，让我们编写实际从 dev.to 获取帖子的代码。我们将编写四个方法来处理这个问题:一个生成 url，一个获取帖子，一个将结果分配给组件。

```
get apiEndpoint() {
  const { username } = this;
  if (!username) return null;
  const search = new URLSearchParams({ username });
  const API_ENDPOINT = new URL('api/articles', 'https://dev.to');
        API_ENDPOINT.search = search;
  return API_ENDPOINT;
}

async updated(changed) {
  if (changed.has('username')) this.fetchPosts();
}

assignPosts(posts) {
  this.posts = posts || [];
  this.loading = false;
}

async fetchPosts() {
  const handleAsJson = response => response.json();
  const { apiEndpoint, assignPosts } = this;
  if (!apiEndpoint) return;
  this.loading = true;
  return fetch(apiEndpoint)
    .then(handleAsJson)
    .then(assignPosts);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要在构造函数中绑定`assignPosts`和`postTemplate`，这样我们就可以析构它们并且[在一级](https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function)之间传递它们。如果我们不这样做，`postTemplate`将把它的`this`引用绑定到 posts 数组，`assignPosts`将绑定到 fetch 承诺；那就太愚蠢了。

```
this.postTemplate = this.postTemplate.bind(this);
this.assignPosts = this.assignPosts.bind(this); 
```

Enter fullscreen mode Exit fullscreen mode

对于 URL，我决定使用内置的`URL`和`URLSearchParams`构造函数。我们可以很容易地使用字符串插值，即`https://dev.to/api/articles?username=${username}`，但是这样做可以让我们在需要的时候很容易地添加更多的参数。也让我觉得我的钱花得值😉

### 去抖取请求

我们在 feed 组件中要做的最后一件事是向服务器发出去抖请求。去抖意味着推迟执行，直到从最后一次调用开始过了一段时间。当您有一个基于用户输入(如键入或滚动)触发的开销很大的操作(如通过网络获取数据，或某些需要大量绘图的 DOM 更新)时，这是一种有用的技术。实际上，我们告诉我们的组件:“当用户键入用户名时获取文章，但在提交发送请求之前，等待半秒钟以确保他们完成了键入。”

```
import { debounce } from './debounce.js';
/* ... */

constructor() {
  super();
  /* ... */
  this.fetchPosts = debounce(this.fetchPosts.bind(this), 500);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在重新加载我们的演示页面，我们将看不到任何东西，因为还没有定义`<dev-article>`组件。但是，如果我们检查元素的影子根，我们会看到几个`<dev-article>`元素，每个元素都有自己的`article` DOM 属性。

[![Screenshot from Firefox Dev Tools Showing the article DOM property of a dev-article element](img/10c690f7b1a03f4f1942e3cd1dc23b96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ywRGS4sJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nl0kwkys80m61dtebmfr.png)

接下来，我们将开始根据 dev.to 上的设计布局每篇文章。

## 实现我们的文章元素

尽管 feed 元素在逻辑上很长，在表示上很短，但是 article 元素是我们磨练语义 HTML 和 CSS-fu 的地方。

每个`<dev-article>`元素将在内部呈现一个`<article>`元素，还有一些语义 HTML 元素作为兄弟。我们将使用 CSS 网格来布局一切，而不添加额外的`<div>`和

```
render() {
  const {
    cover_image: coverImage,
    description,
    positive_reactions_count: positiveReactionsCount,
    published_at: publishedAt,
    tag_list: tagList,
    title,
    type_of: typeOf,
    url,
    user: {
      name,
      profile_image_90: avatar,
      username,
    },
  } = this.article;

  return html`
    <article aria-labelledby="title">
      <figure>
        <a id="cover" ?hidden="${!coverImage}" href="${url}" rel="norefer noopener nofollow">
          <img src="${coverImage}" role="presentation"/>
        </a>

        <figcaption>
          <a id="title" href="${url}" rel="noopener norefer">
            <h3>${title}</h3>
          </a>
        </figcaption>
      </figure>

      <a id="avatar" href="https://dev.to/${username}" rel="norefer noopener nofollow">
        <img src="${avatar}" alt="${name || username}'s Avatar"/>
      </a>

      <section id="metadata">
        <a href="https://dev.to/${username}" rel="norefer noopener nofollow">
          <span>${name || username} • <time>${formatDate(publishedAt)}</time></span>
          <span id="relative-time">(${formatHuman(publishedAt)})</span>
        </a>
        <ul id="tags">${tagList.map(tagTemplate)}</ul>

        <details ?open="${this.showDescription}">
          <summary hidden></summary> ${description} </details>
      </section>

      <span id="positive-reactions">
        <img
            src="https://practicaldev-herokuapp-com.freetls.fastly.net/assets/reactions-stack-4bb9c1e4b3e71b7aa135d6f9a5ef29a6494141da882edd4fa971a77abe13dbe7.png"
            alt="Circled heart on a stack of similar circles"
            title="Number of Positive Reactions"/> ${positiveReactionsCount} </span>

      <section id="actions">
        <button @click="${this.toggleDescription}" title="Show Description">💬</button>
      </section>
    </article>
  `;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以这是非常简单的语义 HTML，但是也有一些好东西:

*   让我们使用 lit-element 将 DOM 中其他地方的按钮绑定到我们的`<details>`元素的`open`状态。
*   我们将添加一个隐藏的`<summary>`元素，这样 UA 就不会向我们显示默认的 disclosure 小部件。
*   我们将使用 [`<time>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time) 元素(如果你已经知道这个元素的存在，给你 10 分)来显示发布日期。
*   我们将使用命名的网格区域来定义 CSS 中的大块布局。更多信息请参见最终代码。

## 最终代码

这是我们的组件，运行在 glitch 上。

[https://glitch.com/embed/#!/embed/dev-feed-element?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/dev-feed-element?previewSize=100&path=index.html)

今天你就可以在你的页面上使用`<dev-feed>`！

```
<script src="https://unpkg.com/dev-feed/dev-feed.js?module"></script>
<dev-feed username="bennypowers"></dev-feed> 
```

Enter fullscreen mode Exit fullscreen mode

或者用 npm 安装，用@pika/web 构建

```
npm i -S dev-feed
npx @pika/web 
```

Enter fullscreen mode Exit fullscreen mode

```
<script src="/web_modules/dev-feed.js"></script>
<dev-feed username="bennypowers"></dev-feed> 
```

Enter fullscreen mode Exit fullscreen mode

## 对未来的设想

这个很快就组装好了，所以还有很大的发展空间。该元素的 API 表面可以使用一些波兰(也许是关于显示描述或其他元数据，如用户社交链接)，所以如果你有任何想法，请评论，或打开问题或 PRs。

另一个需要重新考虑的问题是这个元素是如何获取数据的。没有理由将从 API 获取帖子的工作作为 element 类的一部分。它应该是自己的图书馆。嘿 [@healeycodes](https://dev.to/healeycodes) ，你觉得联合一些现代 devto.js 库抽象超过`fetch`并返回帖子承诺怎么样？

## 感谢阅读

感谢 [@westbrook](https://dev.to/westbrook) 在这篇文章中的投入，感谢 [@healeycodes](https://dev.to/healeycodes) 的开球。
希望你喜欢这篇文章，请在你的网站上使用`<dev-feed>`！