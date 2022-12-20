# 以 VueJs 为例说明数据结构化的重要性

> 原文：<https://dev.to/costicaaa/importance-of-data-structuring-with-vuejs-example--161b>

我们大多数人都专注于解决一个又一个小问题，忘记了应用程序的架构，忽视了根据我们的需求正确构建数据的重要性——无论是在数据库中还是在视图/页面中。

不要误解我，把你的大的、不可能的任务分解成小的任务**是**要走的路。我想说的是:不要忽略更大的画面:你交付的代码最终为某人解决了问题(让我们称那个人为我们的“客户”)。久而久之，客户**会**有不同的需求——没有“可能”，相信我！-所以你必须尽可能多地预见到它。

至于你无法预料的部分，你必须有扩展/修改应用程序的可能性，因为需求的变化不会破坏它，并且在合理的时间框架内。“测试”在这种情况下听起来很酷？抛开独角兽和彩虹不谈，最有可能的是你的应用没有必要的测试。如果你是快乐的一部分，无论如何继续阅读，这也适用于测试覆盖的应用程序。

理论说够了，是时候举个真实的例子了。

假设我们必须根据一些标志/条件，以不同的方式显示或多或少相同的数据——无论您想怎么称呼它们。

这些旗帜从何而来并不重要。它们可能是用户在页面中应用的过滤器，也可能是管理员想要在每次他喜欢的时候改变页面的外观。最后，最重要的是旗帜——真或假，竖或横，黑或白。

VueJs 又漂亮又牛逼。有人可能会说是“魔法”。但它是这样的，因为它负责渲染和更新。您有责任以正确的格式向 It 部门提供数据。

假设我们必须按类别显示产品列表。当然，这意味着我们需要一个看起来或多或少像这样的物体:

```
 const displayProducts = {
        "Category1": [ PRODUCTS ],
        "Category2": [ PRODUCTS ],
    } 
```

这很好，但是如果我们想按颜色、排名或随机顺序显示产品，会发生什么呢？当然，我们可以解析`displayProducts`并获得包含所有产品的产品数组。

但这正是问题所在。在实际使用数据之前，您已经必须解析自己的数据。当这种情况发生时，我认为你应该后退一步，重新考虑你在做什么。

花点时间想想实际的问题:您必须显示产品列表。产品，而不是类别。所以我们的主要数据来源和事实应该是...嗯，是的，产品。

现在，在 Vue 中，一个以数据为中心的框架，我们准备好了。我们有一系列的产品包含他们的信息:标签，类别，排名，他们是否有特色，等等。将所有数据放在一个地方，以不同的方式显示给最终用户实际上是非常容易的。

在所有情况下，显示产品标记几乎都是一样的，但是可以根据您的需要进行定制——不要讨论细节，因为文章会太长。每个不同的列表可以是一个独立的组件，它获取一个产品列表作为道具，这个组件可以呈现一个`product`组件列表。基本清单如下所示:

```
 <template v-if="groupType === 2">
        <template v-for="tag of orderedTags">
            <br>
            <b>{{tag.label}}</b>
            <br>
            <template v-for="(item, index) in productsTagged(tag.id)">
                {{ item.name }}
                <br>
            </template>
        </template>
    </template> 
```

假设我们想通过标签展示产品。我们返回产品的计算就像这样简单:

```
 productsTagged(tagId) {
        let itemsWithTag = [];
        for (let item of this.products) {
            if (item.tags.indexOf(tagId) !== -1) {
                itemsWithTag.push(item);
            }
        }
        return itemsWithTag;
    }, 
```

还不服气？当你的客户在半夜决定首先显示绿色产品，然后第三个显示红色产品时，改变整个页面的顺序是多么容易

```
 orderedTags() {
        let collectedTags = coll(this.tags);
        return collectedTags.sortBy("position").all();
    }, 
```

好吧，现在是凌晨 2 点，他一点也不关心红色和绿色产品。现在他有一些最喜欢的西红柿和一些美味的熏肉，他想先展示出来。我们该怎么办？更改前端页面以显示 2 种“不同”类型的产品，这可能来自 2 个请求，如`getFeaturedProduts`和`getOtherProducts`？
现在有人——不管是你还是其他人——需要进入你的应用程序的后端代码，并以两种不同的方式公开相同的数据。

或者....我们可以用正确的、可扩展的方式构建数据和应用。

```
<template v-if="groupType === 3">
    <b>Our most bought products</b>
    <br>
    <template v-for="item in byFeaturedOption(true)">
        {{ item.name }}
        <br>
    </template>
    <hr>
    <template v-for="item in byFeaturedOption(false)">
        {{ item.name }}
        <br>
    </template> </template> 
```

还有我们的过滤功能:

```
byFeaturedOption(option) {
    let collectedProducts = coll(this.products);
    let itemsByCat = collectedProducts.where("featured", option);
    return itemsByCat.all();
} 
```

你可能想知道那是什么？它就是`collect.js`，一个非常好的无依赖性 js 库，适合像我这样的懒人。它提供了方便的数据集合查询功能——与 laravel 的雄辩集合 99%相似，因为它们的灵感来自于这些集合。

现在你可以编写自己的过滤和排序函数，或者使用像`collect.js`这样的助手。这完全取决于你。如果你的数据结构正确，并且是“原始的”形式，那么你就可以随心所欲地操纵它，而不必每次改变都要修改一半的应用程序结构。

这个片段是面向初学者的，而这篇文章的目的是让每个人意识到正确构建你的应用程序是多么重要。这不仅仅适用于简单的 js 呈现的产品列表，但是我认为 VueJs 的例子是最容易理解和遵循的。

完整片段可在此处找到:

[https://codepen.io/costicaaa/pen/bZWGoB](https://codepen.io/costicaaa/pen/bZWGoB)

一如既往，我们非常欢迎反馈和问题。

干杯: )