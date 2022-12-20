# 我是如何创造出世界上最糟糕的喜剧剧本《REPL》

> 原文：<https://dev.to/swlkr/the-world-s-worst-clojurescript-repl-client-4apd>

[在这里阅读之前的帖子](https://dev.to/swlkr/how-i-made-the-world-s-worst-clojurescript-repl-llg)

**TL；博士**
我尝试运行两个 clojure web 服务器并调用一个 JSON 端点，JVM 在我的 [$3.50/mo 512 MB RAM VPS 服务器](https://www.vultr.com/?ref=7614094)上崩溃了，所以我切换到 clojurescript 并为 atom 编写了自己的 prepl 客户端

### Atom 包开发

如果您从未进行过任何 atom 包开发，那么您将会经历一次疯狂的文档阅读之旅！他们让开始变得非常容易。当然，一开始我甚至不知道从哪里开始。

顺便说一句，你从这里开始。

有趣的是，post 已经 3 年了，所以我想，从那时到现在已经发布了多少个 atom 版本，这是不可能的，但它确实有效。

我最初很困惑，因为我一直在搜索“atom 包开发”，但博文的标题是“… ATOM 插件”。

经典的半生不熟的 intro 编码教程(其中我写过很多)。

好了，现在我大概明白了发生了什么，文件放在哪里，是时候去文档了，学习如何从一个编辑器中获取文本，并将其放入另一个编辑器中，为此我查阅了无所不知、无所不知、无所不为的 atom 文档。

我最初使用了[getwordbursor](https://atom.io/docs/api/v1.18.0/TextEditor#instance-getWordUnderCursor)函数，它很棒，但是我需要在 clojure 表单中获得“最外层的表单”,就像这样👇

```
(defn  say-hello  [s]  ; <—— outer most form is `defn`  (println  "hello"  s)) 
```

Enter fullscreen mode Exit fullscreen mode

...我所做的并不奏效。

所以我做了每一个伟大的艺术家都会做的事情，我从 atom 中杰出的 clojure repl 那里偷了 T1:T2 proto-repl T3。

很明显，这段代码比我最初写的要好得多，所以我决定把它放在我的项目中，然后我就出发去比赛了！

```
console.log('cljs-repl:send');

let editor;
if (editor = atom.workspace.getActiveTextEditor()) {
  let range = EditorUtils.getCursorInClojureTopBlockRange(editor);
  let s = editor.getTextInBufferRange(range);
  console.log(s);
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是现在使用 proto-repl 代码的全部代码，它在 clojurescript 中获得最外层的形式。在这之后，我一跳一跳地远离了一个完全运行的 clojurescript prepl 客户端，因为 [prepl 部分为您完成了所有繁重的工作](https://blog.jakubholy.net/how-to-use-clojure-1.10-prepl/)。

在这一点上，我现在要做的就是

1.  连接到正在运行的 clojurescript prepl 套接字
2.  把代码发过来
3.  解析返回的 EDN
4.  ..并在新窗格中显示它！

等等，解析 EDN，我在用 javascript 写这个插件/包/什么的，怎么用 javascript 解析 EDN？

你猜怎么着[在聊天中放一些 fs](https://knowyourmeme.com/memes/press-f-to-pay-respects)因为这个 js 要拜拜了。

Clojurescript 可以解析 EDN，没问题。

### 从 javascript 切换到 clojurescript

听起来很恐怖，重写一切，(全部 50 行什么的😅)和一个我完全不了解的新事物重新开始。我看过很多博客帖子，上面有一行又一行的 edn 和复杂的安装程序，用 figwheel 编译 clojurescript？太吓人了。幸运的是，clojurescript 人员让事情变得非常简单，但是很难判断您是否刚刚开始。下面是让 clojurescript 在 node.js 中运行的秘诀。

**第一步。制作一个名为 build.clj**
的文件

```
; build.clj  (ns  build  (:require  [cljs.build.api  :as  b]))  (b/build  "src"  {:output-to  "whatever/whatever.js"  :output-dir  "target"  :optimizations  :simple  :target  :nodejs  :output-wrapper  true  :pretty-print  false  :hashbang  false  :main  'your-app.core}) 
```

Enter fullscreen mode Exit fullscreen mode

**第二步。跑吧**

```
clj build.clj 
```

Enter fullscreen mode Exit fullscreen mode

**第三步。利润**

就是这样，如果你真的想变得花哨，并且不想在每次更改时都重新运行，你可以创建一个相同的手表文件:

```
; watch.clj  (ns  watch  (:require  [cljs.build.api]))  (cljs.build.api/watch  "src"  {:main  'your-app.core  :output-dir  "target"  :target  :nodejs  :output-wrapper  true  :pretty-print  false  :hashbang  false  :optimizations  :simple  :output-to  "whatever/whatever.js"}) 
```

Enter fullscreen mode Exit fullscreen mode

…并改为运行`clj watch.clj`。现在，当您更改`src`目录中的任何 clojurescript 文件时，js 将被输出到`:output-to`目录中。

那很容易。

这样一来，我终于可以完成我的 prepl 客户了:

**1。连接到正在运行的 clojurescript prepl 套接字**

```
(def  element  (.createElement  js/document  "div"))  (->  element  .-classList  (.add  "cljs-repl"))  (def  modalPanel  (->  js/atom  .-workspace  (.addModalPanel  #js  {:item  element  :visible  false})))  (def  input  (.createElement  js/document  "input"))  (.setAttribute  input  "style"  "padding: 7px")  (set!  (.-type  input)  "input")  (->  input  .-classList  (.add  "input-text"))  (->  input  .-classList  (.add  "native-key-bindings"))  (set!  (.-value  input)  "localhost:5555")  (aset  input  "onkeydown"  (fn  [event]  (condp  =  (.-key  event)  "Escape"  (.hide  modalPanel)  "Enter"  (connect  input)  "")))  (.appendChild  element  input)  (defn  connection  []  (.show  modalPanel)  (.focus  input))  (defn  connect  [input]  (let  [value  (.-value  input)  [url  port]  (.split  value  ":")]  (->  client  (.connect  port  url  (fn  []))))) 
```

Enter fullscreen mode Exit fullscreen mode

**2。通过**
发送代码

```
(def  client  (net/Socket.))  (defn  send  []  (let  [editor  (->  js/atom  .-workspace  .getActiveTextEditor)  range  (.getCursorInClojureTopBlockRange  EditorUtils  editor)  s  (str  (.getTextInBufferRange  editor  range)  "\n")]  (helpers/log  s)  (->  client  (.write  s)))) 
```

Enter fullscreen mode Exit fullscreen mode

**3。解析 EDN**

```
(->  client  (.on  "data"  (fn  [s]  (helpers/log  (pr-str  s))  (let  [data  (cljs.reader/read-string  (str  s))]  (->  js/atom  .-workspace  (.observeTextEditors  #(update-repl  %  data))))))) 
```

Enter fullscreen mode Exit fullscreen mode

**4。在新窗格中显示它！**

```
(defn  update-repl  [editor  m]  (when  (=  "CLJS REPL"  (.getTitle  editor))  (when  (contains?  m  :form)  (do  (.insertText  editor  (str  (:form  m)))  (.insertNewline  editor)))  (.insertText  editor  (str  (:val  m)))  (.insertNewline  editor))) 
```

Enter fullscreen mode Exit fullscreen mode

### 一切都棒极了

说这是世界上最糟糕的笑话 REPL 是非常负面的，所以我想在这篇文章的结尾说一些积极的东西，现在我做到了。一切都棒极了。

如果你想做一个比我更好的回复，你可以站在普通尺寸的我的肩膀上，抢先一步:[https://github.com/swlkr/cljs-repl](https://github.com/swlkr/cljs-repl)