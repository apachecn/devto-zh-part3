# Eevees，测验，和单曲，哦，我的天！

> 原文：<https://dev.to/maxwell_dev/eevees-quizzes-and-singletons-oh-my-1blh>

经过一年多的煎熬，我的一个副业终于完成了。这是我很久以前就想做的口袋妖怪粉丝的一小部分:口袋妖怪个性测验！更具体地说，一个“你是什么进化”的测验。您可以在此网站查看(并参加)测验。

[![The homepage of the "What Eeveelution Are You" quiz site.](img/3ee9bbe03a36d94efe2030b8948b5bf4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--re3D4N1g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u15qiwm130mgcx5a2ywr.png)

(郑重声明，我是太阳精灵，并为此欣喜若狂。)

这对我来说是一个重要的兼职项目，因为这是我第一个使用 Ember.js 的个人项目。这是我目前工作中使用的 JavaScript 框架，也是我越来越喜欢的一个框架。这个项目利用了 Ember 的惯例，有一套完整的测试，让我一下子就能搞定。

我不想让你深入了解整个应用程序，因为我知道这些天 Ember 在 React 和 Vue 旁边黯然失色。但是我将着眼于 Ember 中最重要的部分，来做一个类似这样的功能测试:**服务，或者更一般地称为 Singletons。**它们在管理应用程序的状态方面发挥了巨大的作用，对于任何编写 JavaScript 应用程序的人来说，了解它们是非常有用的。

## 什么是 Ember 服务？

根据 [Ember 关于服务的文档](https://guides.emberjs.com/release/applications/services/):

> 服务是一个成员对象，它在应用程序的持续时间内存在，并且可以在每个部分都可用。

服务是 Ember 版本的单例设计模式(我在这里将它们称为服务)。应用程序中只有一个实例在每个页面上都存在。对其进行的任何更改都会影响所有页面和组件。我使用了一个服务来控制和组织所有与测验问题和进度相关的数据。

[![A drawing of all the Eevee evolutions sitting together](img/818664248399e3ca078faa1fd98b491f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eeFx9pCh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d27zfkz8w8ajmqdhayx2.jpg)

例如，该服务中的一个属性是当前问题。如果这在我的应用程序中的任何地方发生变化，每个页面都会发生变化，因为它们都使用相同的实例。当你在测验中翻到另一页时，你仍然可以从你停下的地方继续。

正如您所猜测的，这使得服务和单例非常适合管理状态。我最近工作的一个应用程序完全使用一个服务来管理 API 调用时的加载状态。所有 API 调用都可以在开始时将加载状态设置为 true，并在完成时返回 false。

组件可以在任何地方查看这个实例，以切换它们的加载视图。加载栏知道何时隐藏或显示自己，并且总是同步最近的更改。

说到 API，服务本身非常适合管理 API 调用。他们可以记住特定的电话，并保存结果以备后用。服务也可以对返回的数据进行必要的过滤或分类，从而节省大量的时间和精力。

## 服务贯穿整个问答比赛

这是抽象的服务，所以让我们展示一些简短的例子，看看他们是如何帮助做这个测试的。

### 运行 Eevee 进度条

让我们从我最喜欢的测验部分开始，屏幕上运行的视频会随着用户的回答而移动！

测验服务已经将用户的进度计算为十进制值。用于每个问题的控制器获取该值，并将其转换为百分比。服务中对它的任何更新都会立即在这里更新，这里的`quiz.progress`是服务的值。

```
progress: computed('quiz.progress', function() {
  return Math.ceil(this.get('quiz.progress') * 100);
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个`progress`值然后被传递到`progress-bar`组件中。它用它作为 Eevee 在屏幕上移动的“宽度”。

```
<div style="width: {{progress}}%" class="eq-progress-bar__fill">
  <img class="eq-progress-bar__image" src={{runningGIFPath}} alt="" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

服务很容易获得如下(萌)效果。

[![A GIF showing different Eevees running across the screen as a user completes the quiz.](img/efa0e40b4c95cb88141e1858f066c396.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iRFCgQLS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rmhid0jrkknrg9cgtr94.gif)

### 完成测验后更改菜单

问答软件的一个简单技巧是记住用户什么时候已经完成了。如果是这样，它会引导他们从测验走向结果。这项服务很容易做到。首先，应用程序控制器通过注入测验服务获得所需的数据。

```
export default Controller.extend({
  quiz: service(),
  completed: reads('quiz.completed'),
  winner: reads('quiz.winner')
}); 
```

Enter fullscreen mode Exit fullscreen mode

该服务知道它是否完成以及谁是赢家，因此它将这些信息传递给应用程序控制器。应用程序模板使用它来更改导航。这里，`completed`和`winner`是从服务中提取的值。

```
<li class="eq-nav__list-item" data-test="Nav-QuizLink">
  <strong>
    {{#if completed}}
      {{link-to 'See Your Results' 'results.pokemon' winner class="eq-nav__link"}}
    {{else}}
      {{link-to 'Take the Quiz!' 'quiz' class="eq-nav__link"}}
    {{/if}}
  </strong>
</li> 
```

Enter fullscreen mode Exit fullscreen mode

像这样将服务链接到模板是它们非常适合管理状态的部分原因。我用同样的方法告诉用户何时继续测验，或者在结果页面中标记他们的结果。Ember 可以让你快速无缝地将它们连接在一起。

### 使用 Cookies 记忆已完成的测验

上面的代码在一个浏览器会话中运行良好，但是如果用户关闭了窗口并稍后回来呢？Ember 有一个插件`ember-cookies`，可以让你在用户的浏览器中保存和读取 cookies。它本身就是一个服务，用于测验服务！

是的，服务可以在其他服务中使用。我会等一会儿，让你清醒一下。

启动 app 时，服务的`init`钩子运行。它检查是否存在任何 cookies 来表明用户已经完成了测验，以及他们的结果是什么。如果有，它会在服务属性中分配所需的值。

```
init(){
  this._super(...arguments);

  let cookies = this.get('cookies');
  // This saves the cookies service to a variable for convenience

  if (cookies.exists('winner')) { this.set('winner', cookies.read('winner')); }
  if (cookies.exists('completed')) { this.set('completed', cookies.read('completed')); }
  // These update service properties based on the user's cookies
} 
```

Enter fullscreen mode Exit fullscreen mode

## 参加小测验！

我希望你喜欢这个关于 Ember 服务的迷你速成课程，并扩展到单例模式。服务是 Ember 中我最喜欢的部分之一，但是经常被教程所忽略。所以我很乐意帮助那些不熟悉它的人了解它们。毕竟，管理状态是 JavaScript 框架的全部内容！

说了这么多，如果你感兴趣，请试试这个小测验！请在下面评论您的结果，以及未来版本的任何潜在变化。

主要是结果，以免挫伤我的成就感。或者我作为太阳精灵人的快乐感。

[![A cute drawing of an Espeon](img/67bf3ca013d7a5067eab2c32829a4c1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IRCTJwh9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0owpdqolf8jgyfidowml.jpg)