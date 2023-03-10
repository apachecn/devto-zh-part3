# 建造有拉链和没有拉链的树

> 原文：<https://dev.to/greencoder/building-trees-with-and-without-zippers-3po6>

在他 2018 年 11 月的[博客帖子和视频](https://lambdaisland.com/blog/2018-11-26-art-tree-shaping-clojure-zip)中，阿恩·布拉瑟尔解释了在`clojure.zip`命名空间中功能拉链背后的想法。他描述了如何使用它们来导航现有的树形数据结构，以及如何修改它们。

有一点他没有明确提到，那就是当你没有树但需要建一棵树时，它们是一个极好的选择。

# 从序列到树

例如，假设您有以下数据序列:

```
(def  elements  [{:type  :todo-list  :name  "Things I should do today"}  {:type  :text  :value  "Buy some bread"}  {:type  :url-list  :title  "Read blogs"}  {:type  :url  :value  "https://vincent.404.taipei"}  {:type  :url  :value  "https://lambdaisland.com/blog"}  {:type  :text  :value  "Look for some interesting Clojure blogs."}  {:type  :url-list-end}  {:type  :foo  :value  :bar}  {:type  :todo-list-end}  {:type  :life  :description  "Take the time to relax"}]) 
```

现在，假设您真正想要的是这样的树结构:

```
[{:type  :todo-list  :name  "Things I should do today"  :children  [{:type  :text  :value  "Buy some bread"}  {:type  :url-list  :title  "Read blogs"  :children  [{:type  :url  :value  "https://vincent.404.taipei"}  {:type  :url  :value  "https://lambdaisland.com/blog"}  {:type  :comment  :value  "Look for some interesting Clojure blogs."}]}  {:type  :foo  :value  :bar}]}  {:type  :life  :description  "Take the time to relax"}] 
```

为了进行转换，直觉上您需要:

*   找到序列中每个块的开头和结尾，
*   为每个块建立一个树节点，
*   将每个块的子序列的元素作为子元素放在该块中，
*   寻找要处理的其他块。

## 递归算法

[![Recursion](img/cbee94f767d76bc0c1a47c28ba923a69.png "From XKCD")](https://res.cloudinary.com/practicaldev/image/fetch/s--r3j-czqA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vincent.404.taipei/img/zipper/recursion.png)

假设当我们想要处理数据时，我们已经有了完整的数据序列，我们可以通过以这种方式迭代序列来递归地构建我们的树:

```
(defn  build-tree  [data-sequence]  (letfn  [;; Returns [built-children unconsumed-sequence]  (append-children  [children  [element  &  next-sequence  :as  data-sequence]]  ;; End of the tree construction.  (if  (nil?  data-sequence)  [children  nil]  (case  (:type  element)  ;; If we need to open a new block.  (:todo-list  :url-list)  (let  [[sub-node-children  unconsumed-sequence]  (append-children  []  next-sequence)]  (append-children  (conj  children  (assoc  element  :children  sub-node-children))  unconsumed-sequence))  ;; If we need to close the current block.  (:todo-list-end  :url-list-end)  [children  next-sequence]  ;; If we want to add a leaf node to the tree.  (append-children  (conj  children  element)  next-sequence))))]  (first  (append-children  []  data-sequence))))  ;; Transforms the sequence into a tree.  (build-tree  elements) 
```

这种实现很难理解，因为它同时做了太多的事情:

*   构建一个树，节点和子节点。
*   跟踪我们在序列中的位置，递归调用的**入和出**。
*   迭代序列并处理终止。

它也有局限性:

*   混乱的代码流，难以阅读和维护，3 个不同的递归调用以复杂的方式组合在一起。
*   不检查像`:url-list`和`:url-list-end`这样的元素之间的匹配，为此需要一个额外的参数(唉！).
*   不是增量算法。

那种类型的递归函数需要花很多时间来编写和调试。这对作者没有好处，对将来某个时候不得不阅读它的同事也没有好处。

我们当然可以——因此我们应该做得更好。

## 改进

重要观察:

> 在读取数据序列的同时构建树。

我们可以围绕序列的形状来组织它，而不是围绕树的形状来组织树的结构。毕竟，线性思考比在树上思考要容易得多。

在 Clojure 中处理序列的惯用方法是使用 [reduce](http://clojuredocs.org/clojure.core/reduce) 操作。为了构建我们的树，它看起来像这样:

```
(reduce  (fn  [tree  data-element]  ;; Add the element to the existing tree, return the updated-tree.  (some-magic  tree  data-element))  empty-tree  data-sequence) 
```

一旦我们有了一个在归约过程中更新树的算法，我们就知道我们的算法是增量的，因为归约函数是一个增量过程。

```
(reduce  f  :init  [1  2  3])  ;; The above can be rewritten as:  (->  :init  (f  1)  ; process one data  (f  2)  ; process another data  (f  3))  ; and so on, and so on 
```

## 增量算法

我们需要用一些真实的代码替换上面的`some-magic`函数。既然我们没有魔法，那就用分析思维来补偿吧。

### 跟踪节点编辑

向树中添加一个元素很容易，但是知道在哪里添加它并不容易。递归算法知道在哪里添加它们，因为它隐式地跟踪当前正在编辑的节点。

为了模拟这一点，我们需要**跟踪节点**。

### “我是你爸爸”

递归算法的缺点是没有简单的方法来访问正在构建的树的其他现有节点。

理想情况下，**整棵树在任何时候都可以被**访问，特别是靠近被编辑节点的**节点。**

### 解拉链

[![](img/70f8b636169d2d3b676aa2ff94a150d4.png "Zipper, photo by https://www.flickr.com/photos/southpaw2305/")](https://res.cloudinary.com/practicaldev/image/fetch/s--BDGgcona--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vincent.404.taipei/img/zipper/zipper.jpg)

拉链具有上述两个特征:

*   它们跟踪树结构和树中的当前位置。
*   它们提供了在编辑的任何时候从被跟踪的节点到整个树的访问。

下面是使用拉链时整个功能的样子:

```
(require  '[clojure.zip  :as  z])  (defn  build-tree  [data-sequence]  (let  [;; Build a custom zipper  empty-tree-zipper  (z/zipper  (comp  #{:root  :todo-list  :url-list}  :type)  ; branch?  :children  ; children  (fn  [node  children]  ; make-node  (assoc  node  :children  (vec  children)))  {:type  :root})  ; root  ;; The incremental update function  update-zipper  (fn  [zipper  element]  (case  (:type  element)  (:todo-list  :url-list)  (->  zipper  (z/append-child  element)  (z/down)  (z/rightmost))  (:todo-list-end  :url-list-end)  (z/up  zipper)  (z/append-child  zipper  element)))]  ;; Process the whole sequence  (->  (reduce  update-zipper  empty-tree-zipper  data-sequence)  ;; Return the tree data structure from the zipper  z/root)))  (build-tree  elements) 
```

值得注意的是:

*   `update-zipper`函数是最重要的部分，它只需要 8 行代码和大约 15 秒来阅读和理解。
*   修改代码以检查匹配的打开/关闭元素是微不足道的，留给读者作为练习。
*   操作树的函数只能做到这一点，定义 show 来更新节点的子节点的函数在别处定义。

# 接下来是什么

在下一篇文章中，我将讨论一个现实生活中的问题，在这个问题中，需要建造一棵树，并且通过使用一个定制的拉链很好地解决了这个问题。