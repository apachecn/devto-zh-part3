# Vue 初学者完全指南

> 原文：<https://dev.to/aspittel/a-complete-beginners-guide-to-vue-422n>

Vue.js 是一个前端框架，针对渐进式集成进行了优化。这意味着你可以拥有一个只集成了几个 Vue 组件的大型应用——或者你可以从头开始，完全在 Vue 生态系统中工作。

另一个让 Vue 与众不同的地方是，与许多框架相比，它的学习曲线更低。如果你知道 HTML、CSS 和 JavaScript，而不是必须理解复杂的主题，你已经非常接近了！

像任何框架一样，它给你的前端添加了一个结构和实用程序，这样你的应用程序在增长时更容易扩展，更有条理，你也不必经常“重新发明轮子”。

Vue 也非常酷，因为它的生态系统整合得非常好——许多通常是第三方库的实用程序都是由 Vue 核心维护者构建的，比如 [Vue 路由器](https://router.vuejs.org/)和 [Vuex](https://vuex.vuejs.org/) 。

在这篇文章中，我们将探索 Vue 的主要特性，并一起创建一个应用程序！

这就是我们将要建立的，尽管有更多的交互功能。like 按钮会根据用户的点击从心形切换到红心。此外，当有人在文本框中键入时，字符数会递减计数。

[https://codepen.io/aspittel/embed/oVMYqG?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/oVMYqG?height=600&default-tab=result&embed-version=2)

继续检查上面的 HTML 和 CSS 代码，我们将使用我们的 Vue 代码构建 HTML。

## 设置 Vue 应用

现在，我们将使用 Vue CDN -我们想要一个最小化的设置。将来，您可能想要一个更广泛的环境，在这种情况下，您可以使用 [Vue CLI](https://cli.vuejs.org/) 。

转到 Codepen 上的`settings`按钮，切换到 JavaScript 选项卡，在 CDNjs 上搜索 Vue。这将 Vue 库添加到我们的项目中，因此我们可以使用 Vue 提供的所有方法和特性。

现在，我们需要创建一个 Vue 实例，并将其附加到我们的 HTML 中，以便完全集成 Vue！

让我们创建一个存储我们的`Vue`实例的`const`。

```
const app = new Vue() 
```

Enter fullscreen mode Exit fullscreen mode

当我们创建这个 Vue 应用程序时，我们将传递一个对象，现在它将拥有我们所有的配置和应用程序逻辑。

我们要添加到该对象的第一件事是`el`——这是我们希望成为 Vue 应用基础的元素。在这种情况下，元素带有`status`类。

```
const app = new Vue({
  el: ".status"
}) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将添加我们的`data`。为了测试这一点，让我们添加`tweetText`作为数据——所以我们现在有`Hello World!`的地方将成为一个变量。接下来，我们会用不同的文本发布更多的推文，所以让推文的那部分动态起来是有意义的。

```
const app = new Vue({
    el: ".status",
    data: {
        tweetText: "Hello World!"
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

当我们想要添加更多的动态数据(或者将在我们的 Vue 应用中改变的数据)时，我们将向这个`data`对象添加更多的属性。

现在，我们可以在 HTML 中使用新创建的数据，并以这种方式插入变量！如果你曾经使用过手柄或其他模板语言，就有点像那样。

如果你去硬编码的“Hello World！”在 HTML 中，我们现在可以用从我们的 Vue 数据中提取的`{{tweetText}}`来替换它！

```
<p class="tweet-text">
  {{ tweetText }}
</p> 
```

Enter fullscreen mode Exit fullscreen mode

尝试在 Vue 中改变你的`tweetText`，它也会在你的输出中改变！

让我们集思广益一下，看看我们还有哪些数据会在我们的应用程序过程中发生变化。

*   心会在喜欢和不喜欢之间切换
*   当我们键入时，我们剩余的字符会减少

让我们继续为我们的`data`对象添加属性。

```
data: {
    tweetText: "Hello World!",
+    charactersRemaining: 280,
+    liked: false } 
```

Enter fullscreen mode Exit fullscreen mode

我们还将在 HTML 中使`charactersRemaining`动态化。

```
<span class="characters-remaining">
  {{ charactersRemaining }} characters remaining
</span> 
```

Enter fullscreen mode Exit fullscreen mode

我们将暂时搁置`liked`属性，稍后我们将回到这个问题上。

## 方法

现在我们已经有了数据，我们需要让它根据用户的动作进行更新。

我们将为我们的 Vue 对象添加另一个属性——这个属性将存储我们的方法。

```
const app = new Vue({
    el: ".status",
    data: {
        tweetText: "Hello World!",
        charactersRemaining: 280,
        liked: false
    },
    methods: {}
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们的应用程序有两个“动作”——当用户输入时，切换类似的动作和改变字符剩余数。让我们先做字符计数。

我们将首先向我们的方法对象添加一个函数:

```
methods: {
    countCharacters: function() {

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们考虑一下这个函数的逻辑:我们需要计算用户在`textarea`中输入了多少字符。然后，我们需要从 280(或我们的字符限制)中减去该计数。

让我们为评论文本创建一个数据属性，然后每当用户在`textarea`中键入时更新它。

```
 data: {
    tweetText: 'Hello World!',
    charactersRemaining: 280,
+    commentText: '',
    liked: false
  }, 
```

Enter fullscreen mode Exit fullscreen mode

```
<textarea placeholder="tweet your reply" v-model="commentText"></textarea> 
```

Enter fullscreen mode Exit fullscreen mode

`v-model`是一个*指令*，它将我们的数据属性与用户输入到`textarea`中的内容进行同步。所以不管他们输入多少，`commentText`都会匹配他们输入的内容。退一步说，*指令*是 Vue 提供的 HTML 属性，它们的前缀是`v-`。

好，现在回到我们的方法。我们可以在我们的方法中用`this.myDataAttribute` ( [这里有](https://codeburst.io/all-about-this-and-new-keywords-in-javascript-38039f71780c)一个关于 JavaScript 的`this`的很好的参考)。

所以，我们可以用下面的逻辑更新`charactersRemaining`:

```
methods: {
    countCharacters: function() {
        this.charactersRemaining = 280 - this.commentText.length
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要确保每次用户在`textarea`中输入内容时`countCharacters`都会运行。

幸运的是，Vue 有`v-on`指令，我们可以在它后面添加事件，这样我们就可以在每次事件发生时运行这个方法。在这种情况下，每当用户在`textarea`中输入内容时，`v-on:input="countCharacters"`就会运行`countCharacters`方法。

```
<textarea
  placeholder="tweet your reply"
  v-model="commentText"
  v-on:input="countCharacters"
></textarea> 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在让我们退后一步，继续研究我们的`toggleLike`方法。

我们首先需要将方法添加到我们的`methods`对象中。

```
methods: {
    ...
    toggleLike: function () {

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

方法的主体应该将`this.liked`更改为与当前相反的形式。所以:

```
toggleLike: function () {
    this.liked = !this.liked
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要采取行动。

在我们的`reactions` div 上，让我们添加一个事件监听器。

```
<div class="reactions like" v-on:click="toggleLike">
  ...
</div> 
```

Enter fullscreen mode Exit fullscreen mode

是时候介绍另一个 Vue 特性了:条件句！

## 条件句

Vue 允许我们用`v-if`指令有条件地呈现数据。

让我们在我们的`reactions` div 中添加以下 span-wrapped 表情符号:

```
<span v-if="liked">♥️</span> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的红心表情符号只有在`liked`是`true`时才会出现。让我们也添加一个`v-else`到我们的心轮廓表情符号，这样它只有在`liked`是`false`时才会渲染。

```
<span v-if="liked">♥️</span> <span v-else>♡</span> 
```

Enter fullscreen mode Exit fullscreen mode

耶！现在我们喜欢工作！

如果你对以上步骤有任何问题，这里有一个我们目前所拥有的代码笔。

[https://codepen.io/aspittel/embed/WmKRNb?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/WmKRNb?height=600&default-tab=result&embed-version=2)

既然我们已经停止了交互，我们如何创建更多功能相同但数据不同的推文呢？组件！

## 组件

与其他前端框架类似，Vue 应用被分解成组件。我们将组件组合在一起，以创建完整的用户界面。一个好的经验法则是，如果用户界面的一部分被多次使用，它应该被分解成一个组件。

在生产应用程序中，我们的 tweet 可能会被分成几个子组件——我们可能有一个评论文本区组件，一个 like 功能组件，一个个人资料图片组件，等等。但是，现在，我们只是将完整的推文制作成一个组件，这样我们就可以轻松地创建更多的推文。

首先，让我们将逻辑从我们的 Vue 实例移到一个组件中。

`Vue.component`的第一个参数是组件的名称，在本例中是“tweet”。我们还将数据转换成返回对象的函数。这允许我们拥有多个`tweet`组件实例，每个都有单独的数据。

```
Vue.component("tweet", {
  data: function() {
    return {
      charactersRemaining: 280,
      commentText: "",
      liked: false
    }
  },
  methods: {
    countCharacters: function() {
      this.charactersRemaining = 280 - this.commentText.length
    },
    toggleLike: function() {
      this.liked = !this.liked
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要组件的`template`——或者组件将呈现的 HTML。我们将获取所有现有的 HTML 并粘贴到组件的模板属性中。

```
template: `<div class="status">
  <div class="tweet-content">
    <img src="https://pbs.twimg.com/profile_images/1070775214370373633/borvu2Xx_400x400.jpg" class="logo" alt="Vue Vixens DC logo">
    <div class="tweet">
      <a href="https://twitter.com/vuevixensdc">Vue Vixens DC</a>
      <span>@VueVixensDC · Mar 20</span>
      <p class="tweet-text">
        {{ tweetText }}
      </p>
      <div class="reactions">
        <span v-on:click="toggleLike" class="like">
          <span v-if="liked">♥️</span>
          <span v-else>♡</span>
        </span>
      </div>
    </div>
  </div>
  <div class="comment-bar">
    <textarea placeholder="tweet your reply" v-model="commentText" v-on:input="countCharacters">
    </textarea>
    <span class="characters-remaining">
      {{ charactersRemaining }} characters remaining
    </span>
  </div>
</div>` 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了一个 Vue 组件！

我们需要补充的另一件事是:每条推文的文本都是不同的。我们将通过`props`为每个单独的 tweet 传递不同的 tweet 文本——这允许我们将数据从组件外部传递到组件。现在，我们将只指定我们的组件有一个相关联的属性。

```
Vue.component('tweet', {
  props: ['tweetText'],
...
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然需要一个 Vue 应用程序，所以让我们把它添加回我们的 JavaScript:

```
new Vue({ el: "#app" }) 
```

Enter fullscreen mode Exit fullscreen mode

酷，现在我们的 JavaScript 设置好了，我们只需要处理我们的 HTML。在我们的 Vue 实例中，我们现在正在寻找 id 为`app`的元素，所以让我们创建它。

```
<div id="app"></div> 
```

Enter fullscreen mode Exit fullscreen mode

在我们新的 Vue 应用程序中，我们将添加一些 tweet 组件的实例。

```
<div id="app">
  <tweet tweet-text="hello world!"></tweet>
  <tweet tweet-text="hi!"></tweet>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

注意我们如何传入我们的`tweetText` prop - Vue 将 JavaScript camel case 转换成 HTML 中的 kebab case。除此之外，我们的道具看起来像 HTML 属性。

现在我们的组件应该可以运行了！

[https://codepen.io/aspittel/embed/YgjNjb?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/YgjNjb?height=600&default-tab=result&embed-version=2)

还有一件事，通常我们不是在 HTML 中硬编码每条 tweet，而是想循环遍历一个数据结构，并为每一项创建一个 tweet 组件。让我们看看如何在 Vue 中做到这一点！

我们将进入我们的 Vue 应用程序实例，并添加一些 tweet 数据。

```
new Vue({
  el: "#app",
  data: {
    tweets: [
        { id: 1, tweetText: "hello world!" }, 
        { id: 2, tweetText: "hi!" }
    ]
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将使用另一个 Vue 指令，`v-for`来遍历 tweets 数组并为每个创建一个`tweet`实例！

```
<div id="app">
  <tweet
    v-for="tweet in tweets"
    v-bind:key="tweet.id"
    v-bind:tweet-text="tweet.tweetText"
  ></tweet>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们在这里使用了两次`v-bind`——它允许我们动态更新 html 属性(或者在属性中使用变量)。当你使用`v-for`时，推荐使用键——它允许 Vue 更好地识别子元素([更多](https://vuejs.org/v2/guide/list.html#key))。

厉害！现在我们可以通过向`tweets`数组添加一个元素来创建更多的 tweets！

这是所有的代码。

[https://codepen.io/aspittel/embed/oVMBmO?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/oVMBmO?height=600&default-tab=result&embed-version=2)

## 更多资源学习 Vue

首先，有许多很酷的特性可以添加到我们刚刚构建的小部件中。你可以在不同的推特上制作不同的个人资料图片，以及日期和用户数据。您也可以禁用或突出显示我们的文本区溢出的文本。您甚至可以使用 Twitter API 来使用真实的 tweets，甚至让评论发布工作！

这里有一些更棒的继续学习 Vue 的资源:

*   dev 上的 vixens 视图
*   [莎拉·德拉斯纳的 Vue 系列](https://css-tricks.com/intro-to-vue-1-rendering-directives-events/)
*   [Vue 文档](https://vuejs.org/)

而且，如果你对更多像这样的“初学者指南”感兴趣，我也有关于 [CSS](https://dev.to/aspittel/css-from-zero-to-hero-3o16) 和 [React](https://dev.to/aspittel/a-complete-beginners-guide-to-react-2cl6) 的指南！