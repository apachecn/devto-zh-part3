# 如何在 Redux 中存储关系数据

> 原文：<https://dev.to/pigozzifr/how-to-store-relational-data-inside-redux-el6>

[![](img/edfc777a88785f9b77b2fd31d58d9804.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ux-xT9Nj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wete0e9ivdsi5wjj6zm9.jpg)

*照片由[托比亚斯·菲舍尔](https://unsplash.com/photos/PkbZahEG2Ng?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/)T5 拍摄*

迟早，所有前端开发人员都需要将关系数据保存在 Redux 存储中。

## TL；速度三角形定位法(dead reckoning)

遵循 Firebase 的建议，它们非常有用。

## 数据

不管它们是来自 API 还是静态负载，不管是否基于关系数据库，数据之间都有关系。

我们将定义**资源**所有那些可能与其他**资源**有关系的数据类型。

一个示例资源可以是来自我们个人博客的帖子。

这个**帖子**会和**作者**产生 1:1 的关系(从**帖子**的角度来看，我们会看到一个**作者**会和**帖子**产生 1:N 的关系，和**评论**产生 1:N 的关系)。

## 低效的结构

假设我们的个人博客应该只呈现一个帖子列表和每个帖子的详细页面。

对于这个具体的例子，在 Redux 存储中，我们可以有一个第一级键，包含我们所有的文章，按 id 进行索引。在每一个里面，我们可以嵌套作者的数据和评论。

```
const store = {
  posts: {
    'p123': {
      id: 'p123',
      title: 'Best React pattern in the world!',
      author: {
        id: 'a123',
        name: 'Francesco',
        email: 'name.surname@gmail.com',
      },
      comments: {
        'c123': {
          id: 'c123',
          body: 'Best article ever!',
        },
        'c124': {
          id: 'c124',
          body: 'Best article ever ever!',
        },
      }
    },
  }
}

// And so on, you get the point 
```

如果我想为作者的个人资料创建一个包含他的文章列表的页面，就会出现问题。

举一个使用 Redux 选择器的例子，我们可以这样检索我们需要的数据:

```
// This returns an array of posts
const getPostsByAuthor = authorId => state => (
  Object.values(state.posts).filter(post => post.author.id === authorId)
)

// And you'd call this selector like this:
const state = store.getState()
const postsByAuthor = getPostsByAuthor('a123')(state) // [...] 
```

但是，如果能够得到我们需要的东西，效率会特别低:每次我们都应该浏览所有的邮件。

## 一种加权结构

加权结构可以是关系数据库中假设表的 1:1 表示。

```
const store = {
  posts: {
    'p123': {
      id: 'p123',
      title: 'Best React pattern in the world!',
      author: 'a123',
    },
  },
  author_posts: {
    'a123': ['p123'],
  },
  authors: {
    'a123': {
      id: 'a123',
      name: 'Francesco',
      email: 'name.surname@gmail.com',
    }
  },
  post_comments: {
    'p123': ['c123', 'c124'],
  },
  comments: {
    'c123': {
      id: 'c123',
      body: 'Best article ever!',
      post: 'p123',
    },
    'c124': {
      id: 'c124',
      body: 'Best article ever ever!',
      post: 'p123',
    },
  },
} 
```

在这种情况下，我们消除了嵌套问题。然而，我们在 Redux 存储中添加了两个新的一级键。

这种方法并不是完全错误的，但是随着应用程序的增长，可能很难有效地管理所有的关系。

如果资源有限，这可能是一个有用的方法。但是，如果资源数量有限，我们可能并不真正需要 Redux，这也是事实。

## 一个高效的结构

按照 Firebase 的建议，我们可以为自己保存一些一级钥匙:

```
const store = {
  posts: {
    data: {
      'p123': {
        id: 'p123',
        title: 'Best React pattern in the world!',
        author: 'a123',
      },
    },
    comments: {
      'p123': ['c123', 'c124'],
    },
  },
  authors: {
    data: {
      'a123': {
        id: 'a123',
        name: 'Francesco',
        email: 'name.surname@gmail.com',
      },
    },
    posts: {
      'a123': ['p123'],
    },
  },
  comments: {
    data: {
      'c123': {
        id: 'c123',
        body: 'Best article ever!',
        post: 'p123',
      },
      'c124': {
        id: 'c124',
        body: 'Best article ever ever!',
        post: 'p123',
      },
    },
  },
} 
```

与 Firebase 不同，我们不会用“占位符”来嵌套关系。

相反，我们将一级键组织成小型的二级存储容器。

关于`reducers`和`combineReducers`函数，你会想到类似的东西吗？同样的逻辑:我们将全局对象缩小到最小的可表示部分。

## 加成:如何构造选择器

在构建了我们的 Redux 存储之后，您想到的第一个问题可能是:我如何获得这些数据？

这里有一些简单的选择器。

```
// Base data

const selectAuthors = state => Object.values(state.authors.data)
const selectAuthor = id => state => state.authors.data[id]

const selectPosts = state => Object.values(state.posts.data)
const selectPost = id => state => state.posts.data[id]

// Totally useless
const selectComments = state => Object.values(state.comments.data)
// Maybe useless
const selectComment = id => state => state.comments.data[id]

// Relations

const selectAuthorPosts = authorId => state => {
  const authorPosts = state.authors.posts[authorId] || []
  return authorPosts.map(postId => selectPost(postId)(state))
}

const selectPostComments = postId => state => {
  const postComments = state.posts.comments[postId] || []
  return postComments.map(commentId => selectComment(commentId)(state))
} 
```

## 结论

现在，您可以构建一个 Redux 存储来保存关系数据。在某些情况下，这可能有些过头了，但在处理更复杂的应用程序时，这将会很方便。

留下一个🦄如果你喜欢这个帖子！