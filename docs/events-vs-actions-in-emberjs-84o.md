# Ember.js 中的事件与操作

> 原文：<https://dev.to/jimsy/events-vs-actions-in-emberjs-84o>

最近，我和我的团队一起开发了一个 Ember 组件，它需要对 [JavaScript 事件](http://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Events)做出反应，他们对 JavaScript 事件和 Ember 的[动作系统](https://guides.emberjs.com/v3.7.0/templates/actions/)之间的区别表示了一些困惑。我决定在这里写一些基本的东西。

## 吹泡泡

JavaScript DOM 事件的一个基本行为是冒泡。让我们关注一个`click`事件，尽管事件的类型是任意的。假设我们有这样一个 HTML 页面:

```
<html>
<body>
  <main>
    <p>Is TimeCop a better time travel movie than Back To The Future?</p>
    <button>Yes</button>
    <button>No</button>
    <button>Tough Call</button>
  </main>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

假设我在浏览器中加载这个页面，并单击“艰难呼叫”按钮(这个页面上的三个正确答案之一)，然后浏览器沿着 DOM 向下移动，找到鼠标指针下的元素。它查看根元素，检查 click 事件的坐标是否在该元素的区域内，如果是，则迭代该元素的子元素，重复测试，直到找到包含事件坐标且没有子元素的元素。在我们的例子中，它是屏幕上的最后一个`button`元素。

一旦浏览器识别出被点击的元素，它就检查是否有点击事件监听器。这些可以通过使用`onclick` HTML 属性(不推荐)、设置元素对象的`onclick`属性(也不推荐)或使用元素的`addEventListener`方法来添加。如果元素上存在事件处理程序，它们将被一个接一个地调用，直到其中一个处理程序告诉事件停止传播，事件被取消或者我们用完了事件处理程序。然后浏览器移动到元素的父元素，重复这个过程，直到事件被取消或者父元素用完。

## 掌握它

事件处理程序是简单的 javascript 函数，接受单个[事件](http://developer.mozilla.org/en-US/docs/Web/API/Event)参数(除了得到额外参数的`onerror`)。 [MDN 的事件处理程序文档非常全面，你应该读一下](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Event_handlers)。

有一些棘手的因素涉及函数的返回值；经验法则是，如果您想要取消事件，则返回`true`,否则不返回任何内容。`beforeunload`和`error`处理程序是这个规则的例外。

## 少一点交谈

Ember 动作在概念上类似于事件，并且由事件触发(默认为`click`)，但是它们以不同的方式传播。Ember 的第一条规则是“数据向下，行动向上”。这意味着数据从路由“向下”通过控制器进入视图(通过它们的`model`钩子)。视图发出动作，这些动作通过控制器“上升”到路线。

让我们看一个简单的例子。首先是路由器:

```
import Router from '@ember/routing/router';

Router.map(function() {
  this.route('quiz', { path: '/quiz/:slug'})
});

export default Router; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的竞猜路线:

```
import Route from '@ember/routing/route';

export default Route.extend({
  model({ slug }) {
    return fetch(`/api/quizzes/${slug}`)
      .then(response => response.json());
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的测验模板:

```
<p>{{model.question}}</p>
{{#each model.answers as |answer|}}
  <button {{action 'selectAnswer' answer}}>{{answer}}</button>
{{/each}} 
```

Enter fullscreen mode Exit fullscreen mode

### 快速抛开关于路由的话题

当我们加载我们的测验页面时，Ember 首先进入`application`路线并调用它的`model`钩子。由于我们还没有在我们的应用程序中定义一个应用程序路径，Ember 为我们生成了一个默认的路径，它不会从它的模型钩子返回任何东西。假设我们进入了`/quiz/time-travel-movies` URI，路由器将进入`quiz`路由并调用模型钩子，我们假设它返回我们测验的 JSON 表示。这意味着`application`和`quiz`路线同时处于“活动”状态。这是 Ember 的一个非常强大的特性，尤其是当路由开始被深度嵌套的时候。

### 多吹泡泡

当一个动作被触发时，Ember 会沿着链向上冒泡；首先到测验控制器，然后到`quiz`路由，然后到父路由，依此类推，直到它找到一个动作处理程序或到达应用程序路由。这种冒泡行为非常酷，因为这意味着我们可以在路由树的顶部附近处理常见的动作(例如登录或退出动作)，并在需要的地方处理更具体的动作。

值得注意的是，如果你没有一个动作的处理程序，Ember 会抛出一个错误，所以在我们上面的例子中，它会爆炸，因为我们没有在控制器或路径中处理我们的`selectAnswer`。

### 寂寞难耐的成分

Ember 的“数据下降，行动上升”格言在组件级别上失效。Ember 组件应该是 UI 状态的原子单元，不会泄漏副作用。这意味着我们从组件中发出动作的选择被有意地限制了。除了没有冒泡行为之外，动作的行为确实与组件中的预期完全一样。这意味着在组件模板中指定的动作，如果在组件的 javascript 中没有相应的定义，将导致 Ember 抛出错误。

允许组件发出动作的主要方法是使用 ember 所谓的“闭包动作”来传递你的动作，作为组件的已知属性上的可调用函数，例如:

```
{{my-button onSelect=(action 'selectAnswer' answer) label=answer}} 
```

Enter fullscreen mode Exit fullscreen mode

```
import Component from '@ember/component';
import { resolve } from 'rsvp';

export default Component({
  tagName: 'button',
  onSelect: resolve,

  actions: {
    selectAnswer(answer) {
      return this.onSelect(answer);
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这特别好，因为您可以在其他地方重用组件，而不必为新的用例修改它。这个想法是对依赖注入模式的一种改编。

### 最终成分

组件响应浏览器事件有三种主要方式。最简单的是使用`action`手柄辅助对象来响应您的特定事件，例如:

```
<div {{action 'mouseDidEnter' on='mouseEnter'}} {{action 'mouseDidLeave' on='mouseLeave'}}>
  {{if mouseIsIn 'mouse in' 'mouse out'}}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，在响应大量不同事件时，这可能有点笨拙。如果您希望您的整个组件对事件做出反应，而不仅仅是其中的元素，那么它也不太好用。

让组件响应事件的第二种方法是在组件中定义回调。这是通过用您希望处理的事件的名称在组件上定义方法来实现的。如果你想拥有一个名为`click`或`submit`的属性，那就糟了。关于组件事件处理程序，您需要知道两件事；它们的名字被骆驼化了([完整列表在此](https://guides.emberjs.com/v3.7.0/components/handling-events/#toc_event-names))，返回类型被规范化了。如果你想取消活动，请返回`false`。返回其他任何内容都没有影响。

```
import Component from '@ember/component';

export default Component({
  mouseIsIn: false,

  mouseDidEnter(event) {
    this.set('mouseIsIn', true);
    return false;
  },

  mouseDidLeave(event) {
    this.set('mouseIsIn', false);
    return false;
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

第三种方式是使用`didInsertElement`和`willDestroyElement`组件生命周期回调，在组件插入 DOM 和从 DOM 移除时手动管理事件。

```
export default Component({
  mouseIsIn: false,

  didInsertElement() {
    this.onMouseEnter = () => { this.set('mouseIsIn', true); };
    this.onMouseLeave = () => { this.set('mouseIsIn', false); };
    this.element.addEventListener('mouseenter', this.onMouseEnter);
    this.element.addEventListener('mouseleave', this.onMouseLeave);
  },

  willRemoveElement() {
    this.element.removeEventListener('mouseenter', this.onMouseEnter);
    this.element.removeEventListener('mouseleave', this.onMouseLeave);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，使用最后两种方法中的任何一种，如果您认为这样更干净，您都可以使用`this.send(actionName, ...arguments)`来触发组件上的事件。

## 结论

如您所见，动作和事件相似但不同。在最基本的层次上，事件用于对 *UI* 状态进行改变，动作用于对*应用*状态进行改变。像往常一样，这不是一个硬性规定，所以当你问自己是否应该使用事件或动作时，就像所有其他工程问题一样，正确的答案是“视情况而定”。