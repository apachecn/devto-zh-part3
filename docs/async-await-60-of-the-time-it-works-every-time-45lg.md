# 异步等待:60%的时间，它每次都工作

> 原文：<https://dev.to/rhg/async-await-60-of-the-time-it-works-every-time-45lg>

## 全部登上炒作列车。

Hot take: **async await** 并不是万能的。

当我看到人们这样写的时候:

```
const printF = async () => {
  try {
    const a = await getA()
    const b = await getB(a)
    const c = await getC(b)
    const d = await getD(c)
    const e = await getE(d)
    const f = await getF(e)
    console.log(f)
  } catch (err) {
    console.error(err)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

作为这个的替代:

```
const printF = () =>
  getA()
   .then(getB)
   .then(getC)
   .then(getD)
   .then(getE)
   .then(getF)
   .then(console.log)
   .catch(console.error) 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是朝着错误的方向迈出的一大步。它添加了一堆样板文件，但没有什么好处。我试捕了很多年，我不想回去！

我有一些严肃的问题要问我工作中只使用异步 await 的朋友:

[![Brick are you just looking at things in the office and saying that you love them?](img/a48f3f6117023ad04fb7a19098deb205.png)](https://i.giphy.com/media/VnK6iD9Xj9Sww/giphy.gif)

> 我们真的喜欢 async await 吗，或者我们只是因为看到它才这么说？

[![I love lamp.](img/360d8ad29fc75d0ebffadb29eda6ab66.png)](https://i.giphy.com/media/2oNyZo0GZXfgI/giphy.gif)

## 何时爱灯。

上面的例子实际上很不典型。我只是想指出，我们不需要对所有的事情都进行异步/等待。

它并不总是“更可读”，它只是看起来更像同步代码。

现实中，人们并没有付钱给我`printF`。而是以建网站为生，暗地里只是把字符串变成其他字符串而已。

所以当我得到这个字符串:

```
'https://www.<some-website>.com/people/ryan' 
```

Enter fullscreen mode Exit fullscreen mode

我把它变成这个字符串:

```
<div>
  <h1>Ryan Haskell-Glatz</h1>
  <section>
    <h3>Posts</h3>
    <ul>
      <li>Elm is neat.</li>
      <li>Promises are neat.</li>
      <li>Saying neat is neat.</li>
    </ul>
  </section>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

但是有时候我的字符串在一个数据库里:

```
// MongoDB
{
  people: [
    { _id: 1, slug: 'ryan', name: 'Ryan Haskell-Glatz' },
    // ... more people
  ],
  posts: [
    { _id: 12, slug: 'elm-is-neat', title: 'Elm is neat.', author: 1 },
    { _id: 13, slug: 'promises-are-neat', title: 'Promises are neat.', author: 1 },
    { _id: 14, slug: 'saying-neat-is-neat', title: 'Saying neat is neat.', author: 1 },
    // ... more posts
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我的 Javascript 函数看起来更像这样:

```
const mongoose = require('mongoose')

const getPosts = (person) =>
  mongoose.model('posts')
    .find({ author: person })
    .select('title')
    .lean()
    .exec()

const getPerson = (slug) =>
  mongoose.model('people')
    .findOne({ slug })
    .select('name')
    .lean()
    .exec()
    .then(person => person || Promise.reject(`Couldn't find a person with slug: ${slug}`))

const getPeopleDetailPage = (req) =>
  getPerson(req.params.slug)
    .then(person =>
      getPosts(person)
        .then(posts => ({ person, posts }))
    ) 
```

Enter fullscreen mode Exit fullscreen mode

### 让事情变得更美好

`getPosts`和`getPerson`都很好，异步 await 不会改善任何东西。

注意我是如何在`getPeopleDetailPage`中嵌套我的`.then`函数的？有点像那个尖尖的三角形回调地狱的东西。

我嵌套东西的原因是因为我需要访问**`person`和`posts`来将它们作为对象返回。**

 **让我们重写最后一个函数:

```
const getPeopleDetailPage = async (req) => {
  const person = await getPerson(req.params.slug)
  const posts = await getPosts(person)

  return { person, posts }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，person 和 posts 都在范围内，所以我不需要嵌套东西。

Async await 非常适合将其他承诺结合在一起的函数。这有助于我们将事情控制在一定范围内，这样我们就不必忘记`)`并缩进 47 次！

也许这比承诺更好...

[![60% of the time, it works every time](img/c97bada2d76aeb34c0607363ebc071f8.png)](https://i.giphy.com/media/k5lj4s1qxaSyI/giphy.gif)

## 升级以后的事情

假设出现了一个名为“tags”的新集合，我们希望将 Ryan 的标签包含在他的详细信息页面中。

这是新的数据库:

```
// MongoDB
{
  people: [
    { _id: 1, slug: 'ryan', name: 'Ryan Haskell-Glatz' },
    // ... more people
  ],
  posts: [
    { _id: 12, slug: 'elm-is-neat', title: 'Elm is neat.', author: 1 },
    { _id: 13, slug: 'promises-are-neat', title: 'Promises are neat.', author: 1 },
    { _id: 14, slug: 'saying-neat-is-neat', title: 'Saying neat is neat.', author: 1 },
    // ... more posts
  ],
  tags: [
    { _id: 25, name: 'js', people: [ 1 ] },
    { _id: 26, name: 'elm', people: [ 1, 2 ] },
    { _id: 27, name: 'web', people: [ 1, 5 ] },
    // ... more tags
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

还有我们新的`getTags`功能:

```
const getTags = (person) =>
  mongoose.model('tags')
    .find({ people: person })
    .select('name')
    .lean()
    .exec() 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用`Promise.all`更新我们的函数来做一些很棒的事情:

```
const getPeopleDetailPage = async (req) => {
  const person = await getPerson(req.params.slug)
  const [ posts, tags ] = await Promise.all([
    getPosts(person),
    getTags(person)
  ])

  return { person, posts, tags }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`Promise.all`将并行处理事情，因此我们获得了惊人的性能和错误处理的好处。

## 处理错误

在这个函数之外，我们的用户可以决定他们想要如何处理错误。

如果这是一个带有 ExpressJS 的 API 端点，可能会是这样的:

```
const express = require('express')
const app = express()

app.get('/api/people/:slug', (req, res, next) =>
  getPeopleDetailPage(req)
    .then(data => res.json(data))
    .catch(err => next(err))
) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我使用了 async/await 而没有使用 try-catch，万岁！

[![I'm not even mad. That's amazing.](img/50dcb08b90ac9ed5f46dbca5dac832f0.png)](https://i.giphy.com/media/mmw01I9sFtfi/giphy.gif)

## 就是这样！

希望你喜欢阅读，我很高兴我终于开始使用 async await，我想分享使用它的好处。

这不是万能的灵丹妙药，但它对承诺非常有效。

> **Async await** : 60%的时间，每次都有效。

[![That doesn't make sense.](img/75e07ba6df5118b1f8b75f4c7a517556.png)](https://i.giphy.com/media/8xggx6JIPHdWE/giphy.gif)**