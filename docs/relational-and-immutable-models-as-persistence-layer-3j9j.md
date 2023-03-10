# 作为持久层的关系(和不可变)模型

> 原文：<https://dev.to/tomekbuszewski/relational-and-immutable-models-as-persistence-layer-3j9j>

将数据保持在应用程序状态是一件非常常见的事情。但保持其规模和复杂性可能是一个挑战。除非我们把它弄平。

## 我们要解决的问题

大多数情况下，当在存储中保存数据时，数据多少是被扔在一起的，是一个对象数组。例如:

```
[
  { id: 1, title: "Title", },
  { id: 2, title: "Title 2", },
] 
```

这对于没有嵌套数据的小型集合来说是很好的。但是想象一个更复杂的例子，一个博客:

```
[
    {
      id: 1,
      title: "Hello",
      tags: [
        { tag: "Tag 1", slug: "tag-1", },
        { tag: "Tag 2", slug: "tag-2", },
      ],
      categories: [
        { category: "Category 1", slug: "category-1", },
        { category: "Category 2", slug: "category-2", },
      ],
    },
] 
```

虽然可读性很强，但这也产生了一个问题。无论我想显示什么，我都必须获取所有数据。我真的不需要列表上的标签或类别。或者，如果我只想列出所有类别，我必须:

1.  循环浏览所有帖子；
2.  提取`categories`对象；
3.  将数据合并到一个新表中(在一个州或临时的某个地方)。

似乎有很多事要做。

这就是平板模型派上用场的地方。

## 基于关系的平面模型

当拥有不同类型的数据时，关系是一件大事。您可能从 SQL 表中了解到这一点。

**帖子:**

| 身份证明（identification） | 种类 | 标题 |
| --- | --- | --- |
| one | one | “你好” |

**类别:**

| 身份证明（identification） | 名字 | 鼻涕虫 |
| --- | --- | --- |
| one | “欢迎帖” | “欢迎柱” |

非常简单明了。可以很容易地镜像为 JavaScript 对象:

```
{
  posts: [
    { id: 1, category: 1, title: "Hello" },
  ],
  categories: [
    { id: 1, name: "Welcoming posts", slug: "welcoming-posts" },
  ],
} 
```

使用这个模型，我们只保留我们实际需要的数据。如果我们想单独显示文章列表，我们使用`posts`集合。如果我们需要查询什么，我们只需在另一个集合中查找，例如:

```
const data = {
  posts: [
    { id: 1, category: 1, title: "Hello" },
  ],
  categories: [
    { id: 1, name: "Welcoming posts", slug: "welcoming-posts" },
  ],
};

class Post {
  constructor(id) {
    this.id = id;
  }

  getPost() {
    return data.posts.find(post => post.id === this.id);
  }

  getDetailedPost() {
    const post = this.getPost();
    const category = data.categories.find(category => category.id === post.category);

    return {
      ...post,
      category,
    }
  }
} 
```

虽然这是一个完美的解决方案，但是每次都需要编写过滤器并不是很好。

## 输入不可变. js

> 本节假设您至少了解不可变映射和列表。如果没有，事情可能看起来不清楚。

当涉及到数据时，不变性的问题是相当大的。为了避免混淆文本，我不会过多地谈论它，但是我将向您展示，为什么我在创建与我们的模型相似的模型时经常使用它。

首先，让我们稍微重新定义一下我们的模型:

```
const immutableData = Map({
  posts: Map({
    items: Map(),
    collection: List(),
  }),
  categories: Map({
    items: Map(),
    collection: List(),
  }),
}); 
```

很容易看出我们在重复一个模式。这纯粹是故意的。在任何地方使用相同的符号，我们可以确保每个对象都可以在相同的条件下访问。

让我花点时间来解释一下，为什么我们将实体模型分为两个部分。`items`将是一个地图`{ id: { content } }`，`collection`将是一个简单的`[id, id]`列表。这样，我们将能够轻松地获取一个知道其 id 的项目，而无需编写过滤器。

因此，让我们用现有数据建立这样的模型。我将引用文本中前面定义的`data`对象。

首先，我们需要从给定的对象中提取 id。

为了方便起见，我先把数据转换成地图:

```
const immutablePosts = fromJS(data.posts);
const immutableCategories = fromJS(data.categories); 
```

现在我们需要函数来提取所需的值。首先，我们需要一个`[id, id]`列表。我们来写:

```
const extractIds = input => input.reduce((acc, item) => acc.push(item.get("id")), List()); 
```

接下来，我们需要有一个`{ id: { content } }`关系。那是一张地图:

```
const extractElements = input => input.reduce((acc, item) => acc.set(item.get("id"), item), Map()); 
```

仅此而已。现在我们可以创建整个数据库:

```
const immutableData = Map({
  posts: Map({
    items: extractElements(immutablePosts),
    collection: extractIds(immutablePosts),
  }),
  categories: Map({
    items: extractElements(immutableCategories),
    collection: extractIds(immutableCategories),
  }),
}); 
```

现在，当我们有一个完整的数据库时，让我们试着得到一个条目:

```
const item = immutableData.getIn(["posts", "items", 1]); 
```

我知道乍一看这似乎很长，但看看它的纯粹性。很容易判断到底发生了什么。列出所有项目也是如此:

```
const items = immutableData.getIn(["posts", "collection"])
  .map(
    item => immutableData.getIn(
      ["posts", "items", item]
    ),
  ); 
```

很简单，是吧？当然，在现实生活的应用程序中，你会为此构建助手，以避免一直写这个。这样的助手可能是一个类，它将像前面创建的`Post`:
一样工作

```
class ImmutablePost {
  constructor(id) {
    this.id = id;
  }

  getPost() {
    return immutableData.getIn(["posts", "items", this.id]);
  }

  getDetailedPost() {
    const post = this.getPost();
    const category = immutableData.getIn(["categories", "items", post.get("category")]);

    return post.set("category", category);
  }
} 
```

现在拿到一单帖子就是简单的`new ImmutablePost(1);`。

> 你有没有注意到，表面上，那些类是一样的？那是因为[他们都是一个门面](https://dev.to/tomekbuszewski/facade-pattern-in-javascript-3on4)！

## 结论

我认为这很好地展示了如何使用平面数据模型而不是嵌套数据模型。将不变量添加到组合中很有帮助，但是引入了另一个层次的复杂性。因此，如果这不是你熟悉的东西，考虑暂时不要考虑，以免头疼。

* * *

*   [code pen](https://codepen.io/tomekbuszewski/pen/VgZZvM?editors=0010)上的完整代码；
*   [ImmutableJS Home Page](https://facebook.github.io/immutable-js/) ;