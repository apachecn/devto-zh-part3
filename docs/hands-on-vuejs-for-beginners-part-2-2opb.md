# 初学者实用 Vue.js(第 2 部分)

> 原文：<https://dev.to/marinamosti/hands-on-vuejs-for-beginners-part-2-2opb>

上次(本系列第 1 部分的)我们想出了如何用常规的`<script>`标签将 **Vue** 添加到我们的 index.html 中，并且我们成功地将我们的第一个反应属性添加到页面中。今天，让我们学习如何通过用户输入来改变这个属性。

到目前为止，我们的代码如下所示:

```
<html>
    <head>
        Vue 101
    </head>

    <body>
        <h1>Hello!</h1>
        <div id="app">
          <p>My local property: {{ myLocalProperty }}</p>
        </div>

        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

        <script>
          const app = new Vue({
            el: '#app',
            data: {
              myLocalProperty: 'Im a local property value'
            }
          });
        </script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

# 监听用户事件

为了更好地展示 **Vue** 的反应，并学习如何对用户事件做出反应，我们将在我们的应用程序中添加一个按钮，该按钮将改变我们的`myLocalProperty`道具的值。

继续，首先给我们的`<div id="app">`添加一个新按钮。

```
<div id="app">
  <p>My local property: {{ myLocalProperty }}</p>
  <hr>
  <button>Click me</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们对这个按钮被点击有什么反应？

如果你来自一个有着 **jQuery** 背景的人，你的本能可能是尝试做这样的事情:`$('button').click();`然而，在 **Vue** 中有一条黄金法则。不要直接操作 DOM(页面 HTML 中的元素)。

没有进入超级复杂的细节， **Vue** 保持你的 HTML 的一个虚拟“拷贝”(在这种情况下，我们的 div 带有“app”id ),当属性改变时，🧙‍♀️ 🧙‍♂️自动计算出在哪里以及如何更新它。

如果您直接用 JavaScript 对 DOM 进行更改，那么每当 Vue 重新呈现内容时，您就有丢失这些更改和意外行为的风险，因为它不会意识到这些更改。

理论已经足够了，让我们继续点击。将这个**事件处理程序**添加到我们的按钮:

```
<button v-on:click="myLocalProperty = 'The button has been clicked'">
  Click me
</button> 
```

Enter fullscreen mode Exit fullscreen mode

这里正在发生一些事情。

在 **Vue** 中，我们有这些*【指令】*可以添加到我们的 HTML 内容中。

简单来说，一个指令就是一个 **Vue** 能够理解的 HTML 参数。

在这个特殊的例子中，我们告诉 **Vue** : *Vue* (v-)， *on* 用户的 *click* do this: `"myLocalProperty = 'The button has been clicked'"`，这只是一个内联声明来改变我们的属性值。

如果您现在在浏览器中打开您的`index.html`文件并点击按钮，您将会看到我们之前在代码中插入到`{{ }}`中的字符串将会对我们修改属性的按钮做出反应。

## 备选语法

在大多数地方，你很可能找不到在 HTML 上用`v-on:[eventname]`设置的事件，就像我们在这个例子中看到的那样，因为在 **Vue** 中，我们有一种非常方便的方式来处理这类事情。`@[eventname]`。

让我们将我们的`<button>` click even 改为使用这个简写:

```
<button @click="myLocalProperty = 'The button has been clicked'">Click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

# 方法

在大多数情况下，当像按钮的 *click* 这样的用户事件被触发时，你需要做的不仅仅是改变变量的值。所以让我们学习一下**方法**(也就是函数)。

继续我们的例子，让我们让按钮调用一个函数，这个函数将做一些非常简单的事情，它将通过向字符串追加一个随机数来改变`myLocalProperty`的值。

删除我们之前的`@click`实现，替换为:

```
<button @click="buttonClicked">Click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们没有在`"buttonClicked"`后面添加一个`()`。当我们不向函数传递任何参数时，可以省略这些参数。例如，`@click="changeName('Marina')"`。(稍后当我们查看条件渲染时，会有更多关于这方面的内容🙂)

现在我们已经准备好了点击执行`buttonClicked`的按钮，我们需要实际编写这个函数。

**Vue** 有一个专门的地方来编写我们的 **Vue 实例**可以使用的函数。这个地方就在我们之前传到我们`new Vue({})`线的`{ }`里面。

我们将创建一个`methods: {}`属性来保存一个填充了我们函数的对象。

```
<script>
  const app = new Vue({
    el: '#app',
    data: {
      myLocalProperty: 'Im a local property value'
    },
    methods: { // 1
      buttonClicked() { // 2
        const newText = 'The new value is: ' + Math.floor( Math.random() * 100); // 3

        this.myLocalProperty = newText; // 4
      }
    }
  });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细分析一下:

1.  我们在我们的 **Vue** 实例中声明了`methods`属性。正如我提到的，在这里你将放置所有的实例方法/函数。
2.  在`methods`对象`{ }`内部，我们声明了`buttonClicked()`，这是我们试图在`@click`监听器上调用的函数。我们不打算在这一点上使用任何参数，所以空`()`。
3.  我们将 0-1 的随机值乘以 100 的结果的向下舍入值`Math.floor`的值加入到一个字符串中，并将其存储在一个常数中。
4.  我们将新字符串的值赋给`myLocalProperty`。现在非常小心*这个*微小的细节🙃(蹩脚的双关语本意)。当我们给实例的`data`属性内的属性赋值时(在`data: {}`内的属性)，你**必须**通过`this.[prop-name]`访问它。

在方法的上下文中，关键字`this`指的是 **Vue** 实例。Vue 将在幕后表演一些魔术，这样你就可以通过做`this.property = value`来读/写你在`data`中的属性。

现在我们已经设置好了一切，重新加载你的`index.html`文件并点击你的按钮。每次单击按钮，每次执行`buttonClicked`函数时，包含`<p>`的内插`{{ }}`字符串的值都会更新。Vue 反应能力的魔力再一次开始发挥作用。

# 包装完毕

如果此时你在想，*嗯，这真的很简单*那么你就在正确的轨道上。我最喜欢这个框架的一点是它清晰的语法和简单性。它*刚刚工作*。但这不应该和认为 Vue 不强大混为一谈。

到目前为止，我们仅仅触及了 Vue 的皮毛，但是随着我们阅读这些文章，你会发现这些微小的构建模块将很快成为你下一个令人惊叹的应用的核心。

* * *

请继续关注第 3 部分！