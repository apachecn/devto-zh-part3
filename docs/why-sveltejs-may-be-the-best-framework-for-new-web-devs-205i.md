# 为什么 SvelteJS 可能是新网站开发者的最佳框架

> 原文：<https://dev.to/bholmesdev/why-sveltejs-may-be-the-best-framework-for-new-web-devs-205i>

任何从事网络开发几年的人可能每隔一天就会听到这个问题。

我对学习网页开发很感兴趣，但是我不知道如何开始。有什么建议吗？

十年前，这可能是一个简单的答案。只需制作一个`index.html`，在其中添加一些标签，用 CSS 将标题变成红色，然后用 JQuery(或者 plane ole JavaScript，取决于你问谁)来处理这些鼠标点击！

...哦，事情发生了多么大的变化。现在我们到处都是构建工具、客户端路由、带有花哨的运行时脚本的特殊框架、无处不在的绑定“this”、模板文字、CSS-in-JS...我们如何选择最重要的东西？🤷‍♀️:如果有人甚至不知道虚拟 DOM 是什么，我们就不能开始教 React 如何使用虚拟 DOM！

这导致了无数不同程度的“现在就打这个，我稍后再解释”的方法。一些人鼓励初学者立即学习 React 或 Vue，以开始现代实践，而另一些人则在山顶上大声疾呼，初学者应该总是从基础开始。说实话，这两种方法都有优点。前者可以让新手对热重装和组件感到兴奋，冒着留下太多未知的风险，而后者可以让初学者理解 DOM 操作是如何在幕后工作的，同时可能让人们远离我们已经抽象掉的 JS 的复杂性。

那么，我们需要的是一个中间立场。一种入门的方法，同时吸收现代概念，如组件驱动开发、作用域与级联 CSS、模板、声明性函数等。

## 进入:苗条

SvelteJS 是一个相当新的孩子，刚刚开始得到一些关注。有些人可能知道它是 JavaScript 2018 年最受欢迎的文章。对于简略的解释，Svelte 意味着框架，而不是真正的框架；它基本上是一个在构建阶段编译组件的工具，允许你在页面上加载一个`bundle.js`来呈现你的应用。这意味着没有虚拟 DOM，没有框架之上的框架，并且在运行时没有框架可以加载。

对于更有经验的开发人员来说，这些都是相当大的卖点，但是大多数初学者可能无法在不爆炸的情况下阅读最后一段。幸运的是，并不是这个编译魔法让 Svelte 对初学者如此友好。实际上是语法问题。

如果你以前从未见过苗条的组件，这里有一个非常基本的例子:

```
<p class="pretty">Here's some markup <strong>written by {name}!</strong></p>

<style>
    /* here's some scoped CSS */
    .pretty {
        color: red;
    }
</style>

<script>
    /* ...and a variable we can access in the markup */
    let name = "Ben";
</script> 
```

Enter fullscreen mode Exit fullscreen mode

让我们打开包装。首先，值得注意的是，所有这些都可以存在于一个常规的`.html`文件中，如果您愿意，也可以存在于一个`.svelte`文件中。此外，我们看到一些熟悉的标签让人想起无框架开发:`<style>`和`<script>`。遗憾的是，在这些标记中编写样式和 JS 对于正确构建作用域组件是必要的，但是它允许语法突出显示，而不需要额外的文本编辑器扩展，如 CSS-in-JS 解决方案。此外，Svelte 的构建步骤足够智能，可以在默认情况下限定任何特定于组件的样式，因此组件之间不会出现样式冲突。

您还将看到名为`name`的 JavaScript 变量的神奇之处。这对于 Svelte v3 来说是一个全新的概念，其中组件脚本中的任何变量都可以从标记中访问。因此，状态管理不需要学习框架特定的语法，所以没有 Angular `$scope`，没有 React `this.state`，也没有 Vue `data`。相反，我们可以在任何地方使用`let`来获得可赋值的状态值，只要这些值改变，cuing 就会重新呈现。

摆脱这种行话意味着制作一个组件几乎就像是在摆弄一支 CodePen，但是不需要考虑如何将您学到的声明性 JS 函数连接到某个 dom 查询选择器。不过不要太担心:Svelte 不会弄乱回调函数或窗口监听器，所以那些基本原理仍然存在。

这些组件的另一个好处是它们和传统组件一样可以导入。只需输入`.html`和 Svelte 知道如何打开它😊

```
<div>
    <Wizardry />
</div>
<script>
    import Wizardry from './wizardry.html'
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### 整齐，但等一下...

一些读者可能会像我一样发现这个概念令人兴奋，但其他人可能已经准备好了向初学者扔干草叉。这不会让他们搞不清楚 DOM 操纵到底是如何工作的吗？

答案是...也许吧。但是当一个人刚刚起步的时候(至少从个人经验来看)，为了更快地做出很酷的东西，接受一点抽象也是可以的。

此外，就像 Java 和 JS 这样的语言通过垃圾收集抽象出了指针管理一样，感觉上几乎所有现代 web 开发工具都抽象出了 DOM 操作，除了初学者可能不需要面对的更高级的情况。顺便说一句，如果你对什么是指针管理感到困惑，我想这证明了我的观点😛因此，与其强迫初学者操作 DOM 或掌握特定于框架的状态包装器，为什么不让他们直接从标记中访问变量呢？现在他们可以学习组件状态的基本原理，而不会陷入混乱。

## 好吧，我看到了你的基本例子，但是 Svelte 必须有一些特定于框架的古怪之处才能让一切正常工作

诚然，这是真的，但这比你想象的要少得多。一种简单的语法是用于显示 DOM 元素的循环和条件。这很像 JSX 从`map`返回元素的方式，但是没有所有的嵌套括号，初学者(和我自己)很容易迷失。这是一个从数组中生成 a 元素列表的基本函数:

```
<ul>
    {#each profiles as profile}
    <li>{profile.name}: {profile.role}</li>
    {/each}
</ul>

<script>
    const profiles = [
        {name: 'Wes Bos', role: 'React extraordinaire'},
        {name: 'Chris Coyier', role: 'Father of CodePen'},
        {name: 'Cassidy Williams', role: 'Letting you know it's pi time'}
    ] </script> 
```

Enter fullscreen mode Exit fullscreen mode

同样，我理解对语法的任何批评，但我喜欢的是它是多么容易理解。我们没有在 HTML 中嵌套 JavaScript，我们只是说，嘿，让我遍历这个数组，并为每个数组创建一个元素。

还有另一个值得一提的奇怪的事情，我承认我对此不太感兴趣:将道具传递给组件。是的，它功能齐全，简单易学，但语法对某些人的口味来说有点太神奇了。要处理道具，我们只需将道具传递给组件，无论它被导入到哪里...

```
<!-- somewhere.html -->
<Profile coolGuy="Scott Tolinski" /> 
```

Enter fullscreen mode Exit fullscreen mode

...并将该变量作为导出“字母”

```
<!-- profile.html -->
<p>{coolGuy}</p>
<script>
    export let coolGuy = '';
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我完全理解，如果像这样滥用“导出”会关闭一些功能，但它至少遵循了初学者概念化模块的方式:我们导出我们应该在组件外部访问的内容，并导入我们希望在组件中显示的内容。

## 我也许能克服那种奇怪的感觉...但是构建步骤呢？

所以另一个关于让初学者开始使用框架的批评是需要使用包管理器。这意味着...*使用终端喘气*！

听着，我明白，打开那个东西可能会很吓人，它的等宽字体和那种怪异的“cd”可以跳转目录。但是公平地说，当您只需要一个命令就可以运行时，这真的不是一个巨大的障碍。此外，VS 代码中的[集成终端使得开始使用非常简单；它甚至会把你拖到当前的项目目录中！](https://code.visualstudio.com/docs/editor/integrated-terminal)

Svelte 实际上为[提供了一个可下载的起点](https://v3.svelte.technology/repl?version=3.0.0-beta.20&example=hello-world)，这是一个很好的开箱即用的东西，但是我[制作了我自己的入门模板](https://github.com/Holben888/svelte-starter-template)，它只是使用构建工具 Rollup 来进行实时重载。事实上，配置文件不到 30 行长！对于一个基本的苗条项目，这些是你需要的所有目录和文件:

```
/public
    index.html
/src
   index.html
app.js
rollup.config.js
package.json 
```

Enter fullscreen mode Exit fullscreen mode

只需在`package.json`中添加一个运行构建步骤的命令，就万事俱备了！你当然可以说，如果初学者不接触其他框架需要的所有额外模块和文件，它们都不是问题，但在我看来，新手需要考虑的额外东西越少越好。

## 好吧，很好，很酷，适合初学者。但它是一个稳定的框架吗？

对于像 Svelte 这样年轻的框架来说，这是一个非常相关的问题。我展示的所有例子都使用了 Svelte 版本 3 的语法，在撰写本文时,~~仍处于测试阶段，与 ReactJS 和 VueJS 等框架巨头相比,~~的追随者相对较少。因此，尽管令人兴奋，我还是会再等几个月，然后再急于用它来教授代码研讨会。尽管如此，Svelte 为版本 3 的文档提供了一个真正简洁的[页面](https://svelte.dev/docs)，可以让初学者轻松进入框架，而不会被一页又一页的解释弄得不知所措。

因此，让我们回顾一下使用 Svelte 学习 web 开发的一些主要卖点:

*   这是一个基于组件的框架，没有额外的插件
*   它处理状态管理，没有所有通常的缺点
*   它使用作用域样式而不需要 CSS-in-JS(所以没有编辑器扩展或古怪的语法)
*   它只需要一个非常简单的构建脚本就可以开始了
*   基本项目中几乎不需要任何文件

当然，如果我还没有用这个帖子说服你，那完全没问题；所有好的帖子都会引发一些争议！但我希望它至少向你展示了学习苗条身材是多么的酷和简单。

## 学点小东西？

太棒了。如果你错过了，我发布了一个[我的“网络魔法”时事通讯](https://tinyletter.com/bholmesdev)来探索更多像这样的知识金块！

这个东西解决了 web 开发的[【首要原则】](https://www.swyx.io/first-principles-approach/)。换句话说，是什么让我们所有的 web 项目运转起来的所有 janky 浏览器 API、弯曲的 CSS 规则和半可访问的 HTML？如果你正在寻找超越框架的*，这是给你亲爱的网络巫师的🔮*

 *[立即在此订阅](https://tinyletter.com/bholmesdev)。我保证永远教，永远不会垃圾邮件❤️*