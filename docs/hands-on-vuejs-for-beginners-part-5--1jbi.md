# 初学者实用 Vue.js(第 5 部分)

> 原文：<https://dev.to/marinamosti/hands-on-vuejs-for-beginners-part-5--1jbi>

这一次，我们将(最后)看看组件！所以拿些☕️，让我们开始吧。

这是今天文章的白板😁

```
<html>

<head>
  Vue 101 </head> 
<body>
  <div id="app">

  </div> 
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script> 
  <script>
    const app = new Vue({
      el: '#app',
      data: {

      }
    });
  </script> </body> 
</html> 
```

Enter fullscreen mode Exit fullscreen mode

# 组件基础知识

我们将直接进入组件创建，因为这是真正有趣的东西开始的地方。不过放心，这只是勉强刷到了组件的力量。此外，我们仍然需要学习`computed`属性和`watchers`，这也将是一个巨大的帮助。但是下次我们会在今天学习的基础上讨论这个问题。

组件是 **Vue.js** 框架的核心部分，它们是构建反应式丰富应用程序的乐高积木——对我们来说幸运的是，它们非常容易学习和使用！

把组件想象成你的网页/应用程序中你想要使用一次或多次的任何元素。它可以小到一个按钮、一个输入，也可以大到你的整个菜单栏甚至整个页面的视图。

创建一个组件的好处是，你只需编写一次它如何显示的逻辑(HTML/CSS)和它如何与用户交互的逻辑(JS)——然后你就可以在整个应用程序中使用它。

像往常一样，我们将从最简单的例子开始，一个按钮。
我们先定义一下这个组件的**蓝图**或者**模板**，我们称之为`awesome-button`，应该如此！

让我们定义一个新的`awesome-button`组件。将这段代码复制到您的`new Vue`声明上面。

```
Vue.component('awesome-button', {
  template: `<button @click="clickHandler">Click me for some awesomeness</button>`,
  methods: {
    clickHandler() {
      alert('YAAAS 😎');
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过我们在第一天添加的`Vue.js`脚本标签提供给我们的`Vue`对象有这个我们在这里调用的`component`方法。它允许我们创建一个新的组件，正如预期的那样。我们设置的第一个参数是一个*字符串*，它将是我们组件的名称。

第二个参数是一个 JavaScript `object`，令人惊讶的是，它实际上是我们一直用于主`Vue instance`的相同类型的配置对象！这对你意味着什么？您已经知道如何为这个新组件分配属性和方法。

在上面的例子中，您会注意到一个不同点——`template`属性。在这里，我们声明了一个**字符串**(注意，我们还使用了**反勾**字符来包装它，以便我们可以在需要时声明多行，而不必连接多个字符串)，这个**字符串**将保存我们实际的`this` **组件**的 HTML 代码。在这个特殊的例子中，一个简单的`<button>`标签就足够了。

如果你现在重新加载你的页面，什么都不会发生。还记得之前我告诉你这只是蓝图/模板吗？是时候在我们的页面上实际呈现它了。

前往我们放置所有标记的`<div id="app">`，并创建一个新的`<awesome-button>`元素。

您的 HTML 现在应该看起来像这样:

```
<div id="app">
  <awesome-button></awesome-button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

加载页面，现在您将实际看到按钮呈现在页面上。在页面上放置几个或者十个以上的`awesome-button`标签。现在你开始看到组件的力量，虽然在这一点上，我认为我们可以把它推一推。

*奖励:*如果你是好奇型的，看看你的`page source`，用你的浏览器开发工具把它与`inspect`功能进行比较。当页面加载时， *Vue.js* 使用`<awesome-button>`标签作为占位符，放置我们模板的解析内容。

# 二级——有用一点的东西

让我们重温一下上周的例子，用我们的`games`数据再玩一些。

首先，让我们将这些游戏重新添加到主 **Vue** 实例中的`data()`中。

```
const app = new Vue({
  el: '#app',
  data: {
    games: [
      { name: 'Super Mario 64', console: 'Nintendo 64', rating: 4 },
      { name: 'The Legend of Zelda Ocarina of Time', console: 'Nintendo 64', rating: 5 },
      { name: 'Secret of Mana', console: 'Super Nintendo', rating: 4 },
      { name: 'Fallout 76', console: 'Multiple', rating: 1 },
      { name: 'Super Metroid', console: 'Super Nintendo', rating: 6 }
    ]
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

就像以前一样，请随意用您喜欢的标题更新这些内容。

然而，这一次，我们将创建一个`game-card`组件，这将更有意义地显示我们的数据。

准备好了吗？

```
Vue.component('game-card', {
  props: ['gameData'],
  template: `
    <div style="border-radius: .25rem; border: 1px solid #ECECEC; width: 400px; margin: 1rem; padding: 1rem;">
      <h2>{{ gameData.name }} - <small>{{ gameData.console }}</small></h2>

      <span v-for="heart in gameData.rating">❤️</span>

      <button @click="increaseRating">Increase Rating</button>
    </div>
  `,
  methods: {
    increaseRating() {
      // this.game.rating++ ?
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

不要不知所措，你已经知道所有/大部分-所有这些😃！

我们正在创建一个新的`Vue.component`，并将其命名为`game-card`。让我们先跳过`props`，看看`template`。

这里没有什么新东西，除了你可能已经注意到我们正在访问一个`gameData`属性的属性，这个属性没有在`data`中定义，而是在`props`属性中定义。

之后，我们声明了我们的`methods`对象，其中包含了`increaseRating`方法。我特意注释掉了`this.game.rating++`,这可能是你想要处理这个特殊函数的方式，但是它不起作用！现在该说说`props`了。

# 组件道具

我们可以在自定义组件上保留的属性之一叫做`props`。最简单的形式是，它将接受一个定义变量的**字符串**数组。在前面的例子中，我们告诉组件蓝图/模板，我们希望它知道一个名为`game`的属性。

`Props`将允许我们*将*信息从外部传递到我们的组件中！让我们来看看实际情况，这样会更容易理解。

首先，让我们在我们的应用程序中添加一堆`<game-card>`项。我们将像以前一样使用一个`v-for`循环，但是这次我们将在我们的定制组件之上进行循环！

```
<div id="app">
  <awesome-button></awesome-button>
  <hr>
  <game-card v-for="game in games" :game-data="game" :key="game.name"></game-card>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

那就有点儿`game`被折腾了，所以我们来详细看看。

第一步，我们创建我们的`<game-card>`组件，就像我们之前讨论的那样。

之后，我们像上周一样添加`v-for="game in games"`循环。这创建了一个`game`变量，它将在循环中保存当前游戏，我们可以马上使用它！

最后，我们给模板的**属性**，`gameData`赋值，在本例中是循环中的`game`变量。注意，我们使用连字符`game-data`而不是骆驼大小写，因为 HTML 不区分大小写。如果你很难理解这一点，试着从客观的角度去思考。我们正在做类似于`game-card.props.gameData = game`的事情

不要忘记`:key`！

这里有一个很大的陷阱要提到，我们将`game`传递给我们的`game-data`道具，但是在它后面有一个`:`。你注意到了吗？

当我们给一个组件实例分配一个属性时，有两种方法。按照我们的例子，我们可以在前面加上`:`(这是`v-bind:`的简写！).这将确保我们在`="<here>"`之后传递的数据被 JavaScript 用作变量，或者是实际的一段**代码**。

如果你输入的是`gameData="game"`，那么 *Vue* 会把这个当作分配给`gameData`道具**字符串** `"game"`。所以大概是:`game-card.props.gameData = "game"`！

继续，从理论上休息一下，实际上在你的浏览器中运行它。您将会看到，正如您所料，我们的整个`<game-card>`组件的模板正在为我们的每一个`game`进行渲染。

最棒的是，如果我们要对 HTML 进行更改，它将在我们应用程序的任何地方更新。

同样，**最重要的是**，*组件*允许你**包含**特定组件的逻辑。让我们重温一下`game-card`的`increaseRating()`方法。

# 组件数据对道具

组件的道具实际上可能是一个非常冗长的主题，但是有一个非常重要的经验法则，你必须时刻记住。永远不要从组件内部修改属性。

事实上，如果你试图这么做， *Vue* 会抛出各种警告，在控制台里对你大喊大叫——因为这会导致意想不到的行为。这里是文档，如果你想阅读它:[vue 2 中的变异道具是一个反模式](https://vuejs.org/v2/guide/migration.html#Prop-Mutation-deprecated)。

那么，我们如何在组件的存储器中修改`rating`的值呢？关键在问题本身！我们需要将这个`prop`复制到我们的`data`中，这样我们就可以修改它了。

让我们首先将我们的`data`添加到我们的`game-card`组件中，并为它分配一个新的不冲突的名称(如果命名相同，props 和 data props 将会冲突)，然后用 prop 的值预先填充它。

```
data() {
  return {
    game: {...this.gameData}
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意一些事情，但在此之前，如果你还不知道`{...gameData}`在做什么，它是一个 spread 操作符。我不会在这里详细介绍，我会尽快发布一篇简短的文章，但基本上我们是在制作`gameData`道具的的*副本，因为我们不想从孩子那里修改它。*

`data`属性的`return`:

当我们了解到`data`属性时，我告诉过你它需要保存一个具有我们需要的所有属性的对象，这就是我们为我们的主 **Vue 实例**所做的。然而对于**组件**我们实际上需要通过添加`()`和第二个`return`实际对象来使其成为一个函数。

但是为什么呢？！😫

简单地说，您的组件可以有一个或多个实例，对吗？

每个实例将需要一个*唯一的*数据对象！你不希望在所有这些之间共享一个单独的`data`对象，否则它们会共享同一个`title`——这样整个应用程序就失去了意义。

所以让它成为一个**函数**和*返回*对象背后的全部原因是，每次*创建*一个`game-cards`时， **Vue** 可以调用这个函数。这样，每个人都将获得一个独特的数据对象来玩！

访问我们的道具:

当我们创建`game`的**数据**属性时，我们给它赋值`this.gameData`，所以这里需要学习一些东西。`props`也可以通过`this`在你的组件脚本中访问**吗，就像你的本地状态道具来自`data`。所以在这里，我们将`game`设置为等于`gameData`属性。**

这意味着现在我们必须更新我们的 HTML，所以在组件内部切换`game`的`gameData`引用，就像这样:

```
<div style="border-radius: .25rem; border: 1px solid #ECECEC;">
  <h2>{{ game.name }} - <small>{{ game.console }}</small></h2>

  <span v-for="heart in game.rating">❤️</span> 
  <button @click="increaseRating">Increase Rating</button> </div> 
```

Enter fullscreen mode Exit fullscreen mode

在您的浏览器中再次运行，您应该会得到相同的结果。

最后，我们到了可以让我们的`increaseRating`方法工作的时候了！转到该方法，将注释替换为以下内容:

```
methods: {
  increaseRating() {
    this.game.rating++
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

随着每一次点击，我们将增加组件的**内部** `data`属性，该属性保存`game`的等级，而不是道具。

* * *

关于组件有很多理论需要学习，我只是刚刚开始触及表面，但是希望你开始对为什么像 **Vue** 这样的框架如此受欢迎以及使用起来如此有趣有一个更清晰的了解。

从现在开始，我们将开始研究我认为的中间主题，比如`computed`属性、`watchers`、`events`等。所以希望你很快就能兴奋地了解 Vue 的肉的部分。

如果你需要，这里是今天的完整代码，感谢阅读！🤗😋

```
<html>

<head>
  Vue 101
</head>

<body>
  <div id="app">
    <awesome-button></awesome-button>
    <game-card v-for="game in games" :game-data="game" :key="game.name"></game-card>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

  <script>
    Vue.component('awesome-button', {
      template: `<button @click="clickHandler">Click me for some awesomeness</button>`,
      methods: {
        clickHandler() {
          alert('YAS. 😎');
        }
      }
    });

    Vue.component('game-card', {
        props: ['gameData'],
        data() {
          return {
            game: {...this.gameData}
          }
        },
        template: `<div style="border-radius: .25rem; border: 1px solid #ECECEC; width: 400px; margin: 1rem; padding: 1rem;">
          <h2>{{ game.name }} - <small>{{ game.console }}</small></h2>

          <span v-for="heart in game.rating">❤️</span>

          <button @click="increaseRating">Increase Rating</button>
        </div>`,
        methods: {
          increaseRating() {
            this.game.rating++
          }
        }
      });

    const app = new Vue({
      el: '#app',
      data: {
        games: [
          { name: 'Super Mario 64', console: 'Nintendo 64', rating: 4 },
          { name: 'The Legend of Zelda Ocarina of Time', console: 'Nintendo 64', rating: 5 },
          { name: 'Secret of Mana', console: 'Super Nintendo', rating: 4 },
          { name: 'Fallout 76', console: 'Multiple', rating: 1 },
          { name: 'Super Metroid', console: 'Super Nintendo', rating: 6 }
        ]
      }
    });
  </script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode