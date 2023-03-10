# 逻辑在哪里？

> 原文：<https://dev.to/jessachandler/where-to-logic--310p>

## 动机

你知道你可以从 mongoose 模式中的方法返回 HTML 吗？
**还我**:我没有。
**me** :当然。我刚刚试了一下，现在，你可以使用像 item.getSummaryContainer()这样的方法来获得你的摘要容器 HTML。
**还我**:爽。*运行创建一堆方法来返回 HTML，删除视图中的 if/then 语句并替换为方法调用*

分钟过去了

**也是我**:等等，但是为什么？
T3】我 : *眨眼*

## 讨论

作为一名开发人员，我有很多次想出了一些新的(对我来说)想法，然后双脚跳了进去，只是后来想知道这个想法是否不太好。实际上，做决定很难，需要的经验并不是我们所有人都有。

我目前对一个新的、有趣的、但可能不太好(或者可能是)的想法的演绎，与任何推动关于开发人员应该如何处理这些“等等，但是为什么？”的对话一样好决定。我个人没有答案，但我希望评论中的讨论将有助于找出答案。

### 设置

我在动机中透露了一点——如果我们从 mongoose 模式方法返回 HTML 会怎么样。对于这个例子，我们有一个 Mongo Express 节点堆栈。我将展示一个非常简单的显示 if/then 的两种编码方法。假设在这两种情况下，user.things 都作为响应的一部分从服务器返回。

#### EJS 逻辑

当逻辑在 EJS 中时，模式只是变量(或其他方法，但为了简单起见，这里没有显示)，HTML 被 if/then 语句中断。

**thing.js**

```
const mongoose = require('mongoose');

const thingSchema = mongoose.Schema({
     name: { type: String },
     description: { type: String },
     cost: { type: Number }
 });

module.exports = mongoose.model('Thing', thingSchema); 
```

**userthings.ejs**

```
<% include ./partials/header %>
<div class="container-fluid">
  <div class="col-12 col-md-8 mx-auto">
    <div id="upcoming">
      <h2>Things</h2>
      <div class="row">
        <% user.things.forEach(function(thing){ %> 
          <div><h1> <%= thing.name %> </h1>
            <% if (thing.sold) { %>
              <h5> was sold for <%= this.cost %> </h5>
            <% } else { %> 
              <h5><%= this.description %> for just <%= this.cost %></h5>
            <% } %>
            </div>
        <% }) %>
      </div>
  </div>
</div>
<% include ./partials/header %> 
```

#### 猫鼬图式中的逻辑

当逻辑在模式中时，从 EJS 页面看不到 HTML，但是页面是干净的。

**thing.js**

```
const mongoose = require('mongoose');

const thingSchema = mongoose.Schema({
     name: { type: String },
     description: { type: String },
     cost: { type: Number },
     sold: { type: Boolean )}
 });

thingSchema.method({
    printinfo: function () {
      let theinfo = '<div><h1>'+this.name+'</h1><h5>'+this.description+' for just '+this.cost+'</h5></div>'
      if (this.sold) {
        theinfo = '<div><h1>'+this.name+'</h1><h5> was sold for '+this.cost+'</h5></div>'
      }
      return the_info
    }
});

module.exports = mongoose.model('Thing', thingSchema); 
```

**userthings.ejs**

```
<% include ./partials/header %>
<div class="container-fluid">
  <div class="col-12 col-md-8 mx-auto">
    <div id="upcoming">
      <h2>Things</h2>
      <div class="row">
        <% user.things.forEach(function(thing){ thing.printinfo(); }) %>
      </div>
  </div>
</div>
<% include ./partials/header %> 
```

最终，两者返回相同的 HTML 并呈现相同的结果。哪个更好？多一个是正确的吗？为什么？我认为答案可能是“做你觉得对的事情”，但我实际上并不知道。我想知道人们是怎么解决这种事情的。

## 游戏计划

在这一点上，我的计划是继续尝试，失败，然后再尝试。我将继续阅读以了解他人的想法，并在新事物出现时分享给我兴奋或惊喜。我喜欢 dev 社区在互联网这片狂野自由的土地上如此支持和有创造力。

我希望你们，开发者伙伴们，能在下面提出你们对开发者，尤其是缺乏经验的开发者如何做出这样的选择的想法。