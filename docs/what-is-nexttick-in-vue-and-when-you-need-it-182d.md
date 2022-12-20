# Vue 中的$nextTick 是什么，何时需要

> 原文：<https://dev.to/raymondcamden/what-is-nexttick-in-vue-and-when-you-need-it-182d>

我大量使用 Vue 已经有一段时间了，本周我遇到了一个我从未见过的问题。这是一件有据可查且广为人知的事情(当我发微博时，我在大约 60 秒内得到了回复)，但我之前没有点击过。在我进入$nextTick 之前，让我解释一下我在做什么，哪里出错了。

我有一个页面上的隐藏表单，需要有一个动态动作值。考虑这个标记:

```
<div id="app" v-cloak>
  <form :action="myAction" method="post" ref="myForm">
    <button @click.prevent="testAction">Test</button>
  </form>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

还有这个代码:

```
Vue.config.productionTip = false;
Vue.config.devtools = false;

const app = new Vue({
  el:'#app',
  data: {
    myAction:null
  },
  methods:{
    testAction() {
      this.myAction = 'http://www.raymondcamden.com';
      this.$refs.myForm.submit();
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单，对吧？这里唯一有趣的事情可能是使用了`ref`和`this.$refs`来处理用 Vue 直接访问 DOM。我称之为花式，因为这不是我通常需要做的事情。那么当你测试这个的时候会发生什么？亲自尝试一下，看看:

[https://codepen.io/cfjedimaster/embed/omrPpP?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/omrPpP?height=600&default-tab=result&embed-version=2)

它不在我的服务器上发帖，而是直接将帖子发送到 CodePen，就在今天我发现他们支持回显数据，这很酷！(注意，在上面的嵌入式 CodePen 中，POST echo 不起作用。它可能不是嵌入的特性。)

好吧，到底哪里出了问题？好吧，如果你像我一样，你可能没有读过 Vue.js 文档的“内部”部分，特别是这部分:[异步更新队列](https://vuejs.org/v2/guide/reactivity.html#Async-Update-Queue)。

> 如果您还没有注意到，Vue 异步执行 DOM 更新。

如果你还没有注意到这一点，请举手。

[![Ray raising his hand](img/2d00d4c4dd6949b5cc0d52a6a878c9a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NDQe_1fP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/02/raisehand.jpg)

幸运的是，有一个简单的方法可以解决这个问题，如果你真的读了这篇文章的标题，你就会知道它是什么了。这个函数允许您提供一个回调函数，以便在 Vue 完成将您的更改传播到 DOM 时执行，并且可以安全地假设它反映了您的新数据。修复非常简单:

```
this.$nextTick(() => this.$refs.myForm.submit()); 
```

Enter fullscreen mode Exit fullscreen mode

如果胖箭头功能仍然让你有点困惑(这没什么错！)，下面是更简单的版本:

```
this.$nextTick(function() {
    this.$refs.myForm.submit();
}); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在下面的代码栏中看到正确的版本。

[https://codepen.io/cfjedimaster/embed/vbqzpb?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/vbqzpb?height=600&default-tab=result&embed-version=2)

所以为了回答“什么时候”的问题——我想我会说什么时候你需要确保 DOM 100%反映你的数据，在这种情况下这是很明显的——我需要我的表单帖子使用正确的 URL。在我使用 Vue 的所有时间里，这是我第一次需要如此精确的控制，但我相信我会遇到更多的例子。如果可以的话，请在下面的评论中分享一个你用过它的例子！