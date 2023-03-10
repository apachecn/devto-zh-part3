# Vue 组件 FTW -烘烤

> 原文：<https://dev.to/raymondcamden/vue-components-ftw---toasted-386h>

在我开始这篇文章之前，先简单介绍一下背景。几天前，我读了一篇关于 Vue 和国际化的精彩文章([如何将国际化添加到 Vue 应用程序中](https://medium.freecodecamp.org/how-to-add-internationalization-to-a-vue-application-d9cfdcabb03b))，虽然这本身是一篇很棒的文章，但作者在结尾提到了一个小的、随机的小组件，以方便显示国旗( [vue-flag-icon](https://www.npmjs.com/package/vue-flag-icon) )。我真的对此很感兴趣，并认为开始研究我们作为 Vue 开发者的可用选项会很有趣。

考虑到这一点，我在 Twitter 上询问人们对我谈论组件的常规系列有何看法。这个想法是把重点放在小的，易于使用的组件上，这些组件可以很好地集成到现有的项目中。“小”当然是相对的，但在我看来，像 [Vuetify](https://vuetifyjs.com/en/) 这样的东西并不适用。(而且要明确一点，Vuetify 是相当牛逼的！)

我还有一个“规则”，我保留以后忽略它的权利。我想把重点放在支持 npm 安装和脚本标签使用(例如，将这个脚本标签添加到您的 HTML)文件的组件上。我想人们可能不同意我的观点，但我真的认为 Vue 组件支持“构建过程”Vue 应用程序(不好意思，这不是一个很好的短语)和简单的“我正在将 Vue 放入一个常规的 HTML 页面”用例是很重要的。

现在我会试着把它变成一个每周一次的系列，但是老实说，我认为它会更像是一个月两次。我会用 **Vue Components FTW** 作为标语，因为这是我的博客，我想怎么傻就怎么傻！

好吧，很抱歉这么长的序言！对于我的第一个 Vue 组件，我正在回顾[Vue-Toast](https://github.com/shakee93/vue-toasted)这是一个简单的“Toast”库。不知道“祝酒词”是什么？不要难过。在这种内容(网络，而不是你的厨房)中，吐司只是一个自动出现和(通常)自动消失的通知。类似于“你有新邮件！”。还记得收到电子邮件很酷的时候吗？

你可以在下面看到一个这样的例子——只需点击猫。

[https://codepen.io/cfjedimaster/embed/LqOxdY?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/LqOxdY?height=600&default-tab=result&embed-version=2)

该组件有一个简单的 API，但也支持相当多的现成选项:

*   自动驳回的能力。
*   给祝酒词加主题的能力。
*   能够将具有不同动作的按钮添加到 toast 中。
*   能够定义“全局”祝酒词，以便在整个应用程序中重用。(例如，这些甚至支持动态选项，因此您可以创建一个全局错误处理程序，但允许特定的错误消息。)
*   与流行的图标字体集成。

安装可以通过 npm 或添加脚本标签:

```
<script src="https://unpkg.com/vue-toasted"></script> 
```

Enter fullscreen mode Exit fullscreen mode

一旦添加，你就告诉 Vue:

```
Vue.use(Toasted) 
```

Enter fullscreen mode Exit fullscreen mode

那么它有多好用呢？一个永恒的，永无止境的不朽的吸血鬼吐司可以这样制作:

```
Vue.toasted.show('meow!'); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这个调用的结果是一个 toast 对象，稍后您可以使用它来销毁它。我会称之为`woodenStake`，但那就是我。

添加持续时间就像传递一个带有选项的对象一样简单:

```
Vue.toasted.show('life is short...', {
    duration: 2000
}); 
```

Enter fullscreen mode Exit fullscreen mode

是的，有很多不同的选择。下面是一个给 toast 添加动作按钮的例子:

```
Vue.toasted.show('Just Do It', {
    action: {
        text:'Done',
        onClick:(e, toast) => {
            toast.goAway(0);
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

定义全局祝酒词也很容易——记住，您也可以定义它们来为动态定制提供参数。(这个例子很大程度上取自文档。)

```
Vue.toasted.register('my_app_error', 'Oops.. Something Went Wrong..', {
    type : 'error'
});
// later in your code...
this.$toasted.global.my_app_error(); 
```

Enter fullscreen mode Exit fullscreen mode

最后，是一个使用图标包的例子。请注意，在执行此操作之前，您必须包含图标包。对于我的 CodePen 演示(你很快就会看到)，我只是在 CSS 面板中添加了 URL。

```
Vue.toasted.show('I forgot to DVR "Arrow"!', {
    duration:2000,
    icon:'dvr',
    type:'error'
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个演示以上所有内容的代码笔。它还演示了组件的一个有趣问题。如果您为“Foo”祝酒，组件将很好地调整它的大小以适应内容。如果吐司仍然可见，然后你吐司“我的王国啤酒”，你会注意到早期的吐司调整大小，以匹配新的大小。我猜这不是一个错误，但它让我有点惊讶。

[https://codepen.io/cfjedimaster/embed/OdgNrx?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/OdgNrx?height=600&default-tab=result&embed-version=2)

非常简单，非常有用，应该很容易投入到您的下一个 Vue 项目中。如果你以前用过[vue-toast](https://github.com/shakee93/vue-toasted)，请在下面的评论中告诉我。如果你喜欢这个系列(到目前为止)，也给我一个评论吧！