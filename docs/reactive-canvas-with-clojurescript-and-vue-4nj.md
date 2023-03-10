# 带有 ClojureScript 和 Vue 的活性画布

> 原文：<https://dev.to/deciduously/reactive-canvas-with-clojurescript-and-vue-4nj>

# 或者我是如何学会停止担忧和抛弃习惯指令的

自从用 TypeScript 和 Vue 写了我的帖子 [Reactive Canvas 之后，我发现了](https://dev.to/deciduously/reactive-canvas-with-typescript-and-vue-1ne9) [`glue`](https://github.com/Gonzih/glue) ，一个在 [ClojureScript](https://clojurescript.org/) 中定义 [Vue](https://vuejs.org/) 组件的库。曾经的潮人，我不得不给它一个旋转。这篇文章详细介绍了与那篇文章相同的功能，但是使用了 ClojureScript 而不是 TypeScript。

## 设置

首先，你需要安装一个 [JDK](https://openjdk.java.net/install/) 。您还需要获得 [`leiningen`](https://leiningen.org/) ，它为 Clojure/ClojureScript 提供包管理和构建工具。

一旦你安装了上面的，导航到你的项目目录并发布:`lein new figwheel rxcanvas-cljs`。导航到你的新文件夹`rxcanvas-cljs`并打开`project.clj`。我们只需要做一个改变。找到你的`:dependencies`键，让它看起来像这样:

```
 :dependencies  [[org.clojure/clojure  "1.9.0"]  [org.clojure/clojurescript  "1.10.238"]  [org.clojure/core.async  "0.4.474"]  [glue  "0.1.3-SNAPSHOT"]] 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚在列表中添加了`glue`。如果您的版本号不完全匹配，不要太担心——这只是在撰写本文时模板附带的版本号。

现在我们执行`lein figwheel`。第一次运行将是最长的，因为它收集了依赖项。加载后，打开浏览器进入`localhost:3449`。当页面加载时，你应该看到 REPL 提示出现在你的终端上——试着发出`(js/alert "Hello from ClojureScript")` :

```
// ...
[Rebel readline] Type :repl/help for online help info
ClojureScript 1.10.238
dev:cljs.user=> (js/alert "Hello from ClojureScript") 
```

Enter fullscreen mode Exit fullscreen mode

您应该会在浏览器中看到请求的警报。在开发过程中让它一直运行，当你准备好关闭时，在 REPL 提示符下键入`:cljs/quit`。

如果你对 [`figwheel`](https://figwheel.org/) 不熟悉，花点时间熟悉一下空白项目布局。这里没有太多。`dev`目录只是设置了一些方便的功能，我们的 HTML 和 CSS 会住在`resources/public`。它已经为你预装了一个`.gitignore`和一个`README.md`。我们所有的逻辑都将存在于`src/rxcanvas_cljs/core.cljs`中。

## 添加模板

我们没有使用单文件组件。目前这将涉及一些非平凡的 DIY 管道。还没有等效的方法来为我们进行解析——您可以先编写第一个！如果我错了，下面有人来管管。

我们只是要保持我们的模板独立。打开`resources/public/index.html`。figwheel 模板带有一个 id 为`app`的`div`。我们将保留 div，但替换内容:

```
<div id="app">
  <rxcanvas></rxcanvas>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`<template>`标签来定义可调整大小的点组件。将它放在`app` div 之上，紧接着开始的`<body>`标签:

```
<template id="rxcanvas">
  <div>
    <span>{{ size }}</span>
    <input type="range" min="1" max="100" step="5" id="size" @change="drawDot">
    <label for="size">- Size</label>
    <p><canvas id="rx"></canvas></p>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 中有两处更改。首先，我用`@change="drawDot"`替换了`range`标签中的`v-model="size"`。这个方法将处理更新我们的状态。我还抛弃了`<canvas>`标签中的自定义指令，而只是分配一个 id。

## 加点口齿不清

现在我们来看看好东西。打开`src/rxcanvas_cljs/core.cljs`。首先，我们需要用`glue`提供的覆盖内置的`atom`，并将库的其余部分纳入范围。将以下内容添加到文件顶部的`ns`表单中:

```
(ns  rxcanvas-cljs.core  (:refer-clojure  :exclude  [atom])  (:require  [glue.core  :as  g  :refer  [atom]])) 
```

Enter fullscreen mode Exit fullscreen mode

保留文件顶部的`(enable-console-print!)`行——如果我们愿意，这允许我们使用浏览器控制台通过`println`进行输出——但删除其他所有内容。

我们将从挂载点开始:

```
(defonce  app  (g/vue  {:el  "#app"}) 
```

Enter fullscreen mode Exit fullscreen mode

这将从`index.html`中定位`<div id="app">`，并将我们的 Vue 内容安装到它上面。我们还需要确保它不断刷新自己-添加以下:

```
(defn  on-js-reload  []  (g/reset-state!)) 
```

Enter fullscreen mode Exit fullscreen mode

ClojureScript 不像 TypeScript 那样面向对象，所以我们将只定义一个普通的旧函数来处理画布绘制逻辑，而不是一个`Dot`类。把这个放在你的`app`定义之上:

```
(defn  draw  [radius  canvas]  (let  [canvas-dim  (*  2  radius)]  ;; resize canvas  (set!  (.-width  canvas)  canvas-dim)  (set!  (.-height  canvas)  canvas-dim)  ;; draw the shape  (let  [ctx  (.getContext  canvas  "2d")  center-x  (/  (.-width  canvas)  2)  center-y  (/  (.-height  canvas)  2)]  (set!  (.-fillStyle  ctx)  "rgb(0,0,0)")  (.clearRect  ctx  0  0  (.-width  canvas)  (.-height  canvas))  (.beginPath  ctx)  (.arc  ctx  center-x  center-y  radius  0  (*  2  (.-PI  js/Math))  false)  (.fill  ctx)  (.stroke  ctx)))) 
```

Enter fullscreen mode Exit fullscreen mode

Interop 非常简单——只需将方法放在 s 表达式的第一个位置。您可以通过类似于`(.-PI js/Math)`的语法来获取和设置属性。人们很容易对超常规语法上瘾。

现在我们准备定义组件本身。对于`glue`，我们使用`defcomponent`，就在`draw`的下面:

```
(g/defcomponent  :rxcanvas  {:template  "#rxcanvas"  :state  (fn  []  {:size  (atom  10)})  :methods  {:draw-dot  (fn  [this  state  _]  ;; update the state  (reset!  (:size  state)  (.-value  (.querySelector  js/document  "#size")))  ;; grab the new value and the canvas for drawing  (draw  @(:size  state)  (.querySelector  js/document  "#rx"))  )}}) 
```

Enter fullscreen mode Exit fullscreen mode

我们使用键`:state`而不是`data()`，但是它仍然返回一个函数。我们已经将`size`显式存储在一个`atom`中，ClojureScript 的机制允许在其他不可变语言中的可变性。正如我们所讨论的，这个特殊的`atom`来自`glue`，并且内置了一些额外的优点以方便在 Vue 组件中使用。通过它，我们可以使用简单的形式如`(:size state)`来访问`size`。

还要注意——在我们的模板中，我们定义了方法名`drawDot`，在我们的 ClojureScript 中，它被称为`draw-dot`。这是`glue`正在[处理](https://github.com/Gonzih/glue/blob/8df738c2e6256b914998bb1537f85ff4bef2e4e5/src/glue/core.cljs#L50)的另一部分！

我们需要`@(:size state)`中的`@`操作符来获取我们对`draw`的调用中`atom`的当前值。

就是这样！现在我们的画布将会随着滑块的变化而改变大小和重绘。

完整的代码可以在这里找到[。](https://github.com/deciduously/rxcanvas-cljs)