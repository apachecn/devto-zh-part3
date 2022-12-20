# 通过触发器实现𝚒𝚗𝚝𝚎𝚐𝚛𝚒𝚏𝚢 -凡斯托参照完整性

> 原文：<https://dev.to/anishkny/---firestore-referential-integrity-via-triggers-kpb>

# Firestore 很惊艳，但是...

Google Cloud Firestore 是一个水平扩展的无服务器 NoSQL 文档数据库——这意味着它可以根据需求自动添加/删除节点来服务您的数据库。它还做了一些有趣的索引，允许查询时间与结果大小成比例，而不是与总数据大小成比例。因此，基本上，如果您的查询返回 10 条记录，那么如果总数据大小为 10、100、1000 或数万亿条记录，那么它将花费相同的时间来运行。

它提供了一种富有表现力的查询语言，但是确实有一些保证 O(ResultSet)性能的限制。此外，在设计 NoSQL 数据库模式时，我们不得不经常“忘记”我们在构建关系数据库时学到的数据规范化原则。

例如，假设您有一个数据库，记录了拥有用户名和个人资料照片的用户所做的评论。传统上，您会在 comments 表中存储一个名为 userId 的外键，并执行一个“join”来将评论与用户名和个人资料照片放在一起。

但是在 NoSQL 模式中，数据通常是非规范化的——例如，在这种情况下，用户名和照片在每个评论记录中重复出现，以便于检索。

那么，关键问题当然是如何在用户的所有评论中反映用户名/照片的更新？在 Firestore 的例子中，我们可以编写一个云函数[,通过对任何用户记录的更新来触发](https://firebase.google.com/docs/functions/firestore-events),将更新复制到所有评论记录。

# 𝚒𝚗𝚝𝚎𝚐𝚛𝚒𝚏𝚢可以帮忙！

𝚒𝚗𝚝𝚎𝚐𝚛𝚒𝚏𝚢是一个 npm 库，提供预装的 Firestore 触发器，有助于在一些常见的情况下保持引用和数据的完整性。

## 属性复制

**场景**——继续上面的用户/评论示例，您可以有一个这样的模式:

```
 /users/
    userId/
      username
      photoURL

  /comments/
    commentId/
      body
      userId       <-- foreign key
      username     <-- replicated field
      photoURL     <-- replicated field 
```

Enter fullscreen mode Exit fullscreen mode

**解决方案**——要在用户名/图片 URL 更新时实施参照完整性，只需使用:

```
exports.replUserAttrs = integrify({
  rule: 'REPLICATE_ATTRIBUTES',
  source: {
    collection: 'users',
  },
  targets: [
    {
      collection: 'comments',
      foreignKey: 'userId',
      attributeMapping: { 
        'username': 'username', 
        'photoURL': 'photoURL', 
      },
    },
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 删除陈旧引用

**场景**——假设您有一个文章集合，其中每篇文章可以有零个或多个评论，每个评论都有一个 articleId 外键。如果相应的文章被删除，您希望自动删除所有评论。

```
 /articles/
    articleId/
      body
      updatedAt
      isPublished
      ...

  /comments/
    commentId/
      articleId   <-- foreign key
      body
      ... 
```

Enter fullscreen mode Exit fullscreen mode

**解决方案** -要删除与已删除文章对应的所有评论，请使用:

```
exports.delArticleRefs = integrify({
  rule: 'DELETE_REFERENCES',
  source: {
    collection: 'articles',
  },
  targets: [
    {
      collection: 'comments',
      foreignKey: 'articleId',
    },
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 计数维护

**场景**——假设你想记录哪些用户喜欢了某篇文章，并且能够快速确定一篇文章总共获得了多少个赞。

```
 /likes/
    likeId/
      userId
      articleId    <-- foreign key

  /articles/
    articleId/
      likesCount   <-- aggregate field 
```

Enter fullscreen mode Exit fullscreen mode

**解决方案**——要维护存储在相应文章文档中的点赞数的实时计数，请使用:

```
[
  module.exports.incrementLikesCount,
  module.exports.decrementLikesCount,
] = integrify({
  rule: 'MAINTAIN_COUNT',
  source: {
    collection: 'likes',
    foreignKey: 'articleId',
  },
  target: {
    collection: 'articles',
    attribute: 'likesCount',
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，对于 likes 集合中的每次添加或删除，有两个触发器，一个用于递增 likesCount 属性，另一个用于递减 likes count 属性。

# 正在部署

𝚒𝚗𝚝𝚎𝚐𝚛𝚒𝚏𝚢应该与`firebase-functions`和`firebase-admin`一起使用。事实上，他们是𝚒𝚗𝚝𝚎𝚐𝚛𝚒𝚏𝚢.的贵族通常，您的设置看起来像:

```
const functions = require('firebase-functions');
const admin = require('firebase-admin');
admin.initializeApp();
const db = admin.firestore();
const { integrify } = require('integrify');

integrify({ config: { functions, db } });

// Use integrify here... 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以像其他 [Firebase 函数](https://firebase.google.com/docs/functions/get-started) :
一样部署𝚒𝚗𝚝𝚎𝚐𝚛𝚒𝚏𝚢返回的函数

```
firebase deploy --only functions 
```

Enter fullscreen mode Exit fullscreen mode

# 源代码

检查源代码，并随时打开任何问题，发出 PRs 或一般意见！

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[整合](https://github.com/anishkny/integrify)

### 🤝使用触发器在云 Firestore 中实施引用和数据完整性

<article class="markdown-body entry-content container-lg" itemprop="text">

# 𝚒𝚗𝚝𝚎𝚐𝚛𝚒𝚏𝚢

[![Build & Test](img/3ee4fa382aa9174ba03c43f9efc96e10.png)](https://github.com/anishkny/integrify/actions/workflows/build-and-test.yml)[![Code Coverage Status](img/41dc7de7b0bcbb9f8253916b8b8cf79f.png)](https://codecov.io/gh/anishkny/integrify)[![Dependency Status](img/df59a77c591266cef994654d56e44ad9.png)](https://dependabot.com)[![Security](img/0d2f6abed9afa55add7b9be7b800f052.png)](https://github.com/anishkny/integrify/network/alerts)[![npm package](img/277fbb34c3c12ae887eba9e3360b473e.png)](https://www.npmjs.com/package/integrify)[![Mentioned in Awesome Firebase](img/1ccd181a01eb46221abe3b2cdab927b2.png)](https://github.com/jthegedus/awesome-firebase)[![Firebase Open Source](img/fa842e7e9f453de931c236e0dc45327d.png)](https://firebaseopensource.com/projects/anishkny/integrify/)

<g-emoji class="g-emoji" alias="handshake" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f91d.png">🤝</g-emoji>使用[触发器](https://firebase.google.com/docs/functions/firestore-events)在[云 Firestore](https://firebase.google.com/docs/firestore/) 中实施参照和数据完整性

[介绍性博客文章](https://dev.to/anishkny/---firestore-referential-integrity-via-triggers-kpb)

## 使用

```
// index.js
const { integrify } = require('integrify');

const functions = require('firebase-functions');
const admin = require('firebase-admin');
admin.initializeApp();
const db = admin.firestore();

integrify({ config: { functions, db } });

// Automatically replicate attributes from source to target
module.exports.replicateMasterToDetail = integrify({
  rule: 'REPLICATE_ATTRIBUTES',
  source: {
    collection: 'master',
  },
  targets: [
    {
      collection: 'detail1',
      foreignKey: 'masterId',
      attributeMapping: {
        masterField1: 'detail1Field1',
        masterField2: 'detail1Field2',
      },
    },
    {
      collection: 'detail2',
      foreignKey: 'masterId',
      attributeMapping: {
        masterField1: 'detail2Field1',
        masterField3: 'detail2Field3',
      }
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/anishkny/integrify)

感谢你阅读✌️✌️✌️