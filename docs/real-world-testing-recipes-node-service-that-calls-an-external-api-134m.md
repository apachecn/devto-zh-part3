# 真实世界测试方法:调用外部 API 的节点服务

> 原文：<https://dev.to/ccleary00/real-world-testing-recipes-node-service-that-calls-an-external-api-134m>

***原载于 [coreycleary.me](https://www.coreycleary.me/real-world-testing-recipes-node-service-that-calls-an-external-api/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我还定期发送备忘单、其他优秀教程的链接(由其他人提供)和其他免费赠品。*

这是我的系列文章中的下一篇，通过将**方法**应用到**真实场景**来了解在节点应用程序中测试什么。

在第一篇文章中，我们回顾了从节点服务调用数据库时，您应该在测试中涵盖的[场景。](https://www.coreycleary.me/know-what-to-test-using-these-recipes-node-service-that-calls-a-database/)

在这篇文章中，我们将介绍另一个非常常见的真实应用程序:一个调用外部 REST API/端点的节点服务。“外部”意味着它是我们自己之外的应用程序——想想 Twitter REST API、Google Maps API，或者甚至是你公司内部的 API，但不是你的应用程序的一部分。

### 如果你是这个系列的新手...

如果你曾经浏览过 JavaScript 或节点单元测试教程，你可能很难找到任何东西来告诉你 ***什么*** 你应该测试- ***什么场景*** 要覆盖， ***什么*** 要编写测试，等等。

很容易找到指导您完成非常基本的示例的教程——比如如何编写加法函数的测试或检查对象属性的测试——但是很难找到超出基础并涵盖真实场景的教程。

作为一名开发人员，你知道如果你想被认为是一名“好的开发人员”，你“应该”写测试。但是如果你不知道你应该寻找的测试场景的种类，那么一开始就很难写出来。如果你完全不熟悉编写测试，那就更令人沮丧了。

当你有了一个需要在工作中实现的特性，截止日期就要到了，而你却陷入了测试，通常这些测试根本不会被写出来。

### 测试配方的应用

当我学习如何为我的代码编写测试时，我也面临这种挫折。但是我学会了用几种不同的方法来报道一些事情:

*   获取我的测试应该覆盖的场景的错误报告
*   通读现有代码库的大量测试，包括工作代码库和开源代码库

我最终注意到很多测试覆盖了相似的场景。根据您正在处理的应用程序的部分，有一些事情需要注意，以确保您的测试——通过扩展，代码——覆盖，这样您就可以确保以后引入到应用程序中的任何新变化都可以在它崩溃时捕捉到。

这些场景是从我的发现中提炼出来的。当您遇到类似的应用程序时，可以将这些作为起点。

最终，你不仅会知道要写什么测试，[这些测试还会帮助你设计/实现你的代码](https://www.coreycleary.me/tried-tdd-and-didnt-realize-the-benefits-try-it-the-next-time-you-get-writers-block/)。

### 我们现实世界的例子

我们将为一个图书馆应用程序编写测试，该应用程序允许您通过标题搜索图书馆的书籍。

我们将与之交互的 API 将是[开放库搜索 API](https://openlibrary.org/dev/docs/api/search) 。

完整的代码(带测试！)可以在这里下载[，但是我建议先跟随这里。毕竟，这篇文章的目的是帮助你识别要覆盖的*场景*，而不仅仅是理解代码。](https://github.com/coreyc/test-recipes-service-to-api)

就这样，让我们开始进入我们的食谱...

### 场景 1:我们的节点服务成功调用外部 API 了吗？

这是我们实际调用 API 的初始代码。在我们的节点服务- `book.service.js` :

```
const request = require('superagent')

const fetchBooks = async (query) => {
  return await request
      .get('http://openlibrary.org/search.json')
      .query({ q: query }) // query string
}

module.exports = {
  fetchBooks
} 
```

那么测试应该覆盖什么场景呢？

```
const chai = require('chai')
const chaiAsPromised = require('chai-as-promised')
const nock = require('nock')

const { booksFixture } = require('./books.fixture')

const { fetchBooks } = require('../src/book-service')

const expect = chai.expect
chai.use(chaiAsPromised)

describe('Book Service', () => {
  describe('fetchBooks', () => {
    it('should return list of books based on search string', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(200, booksFixture)

      const {body} = await fetchBooks('lord of the rings')
      expect(body).to.deep.equal({
        docs: [
          {title_suggest: 'The Lord of the Rings', cover_edition_key: 'OL9701406M'},
          {title_suggest: 'Lord of the Rings', cover_edition_key: 'OL1532643M'},
          {title_suggest: 'The Fellowship of the Ring', cover_edition_key: 'OL18299598M'}
        ]
      })
    })
  })
}) 
```

我们在这里测试的是，我们从 API ( **200 状态代码**)获得一个成功的响应，并获得我们的图书结果。很简单，对吧？

因为这是我们添加的第一个测试，所以让我们快速回顾一下**我们如何**测试它。

### 嘲弄的

在测试中使用模拟本身可以是一整本书，但是在这里我们以一种非常简单的方式使用它，使用[NPM 模块，`nock`](https://github.com/nock/nock) 。

将监听对我们指定的 url 的调用——在本例中是 Open Library REST API——并将“拦截”这些调用。因此，代替*实际上*调用**真实的**开放库 REST API，我们指定假的响应来返回。

我们这样做是因为:

*   调用真正的 HTTP API 会引入延迟
*   延迟降低了我们测试的速度
*   我们对返回的数据控制较少
*   返回的数据可能略有不同，这将破坏我们的测试

### 固定装置

我们回复的假回应呢？那是我们的**夹具**。就像模拟一样，固定物是一个太大的概念，无法在这里完全涵盖。*但是*最终它们还是很容易的。

这是来自开放库 REST API 的真实响应:
[![](img/ffaec1e1783ac6f716b1bec0d5b96818.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FCRO6Edt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.coreycleary.me/wp-content/uploads/2018/11/Screen-Shot-2018-11-21-at-12.31.59-PM.png)
`isbn`和`text`这些属性中的每一个都是可能包含数百个条目的数组。你能想象如果我们必须用手重现那个反应吗？我们可以复制并粘贴 JSON 响应，但即使这样，它也会占用整个测试文件，并且很难阅读。

我们不是复制整个响应，而是复制它的一个子集。这给了我们足够的数据来测试我们所需要的东西，而不必弄乱我们的测试。

这些数据放在我们的`books.fixture.js`文件中:

```
const booksFixture = {
  docs: [
    {title_suggest: 'The Lord of the Rings', cover_edition_key: 'OL9701406M'},
    {title_suggest: 'Lord of the Rings', cover_edition_key: 'OL1532643M'},
    {title_suggest: 'The Fellowship of the Ring', cover_edition_key: 'OL18299598M'}
  ]
}

module.exports = {
  booksFixture
} 
```

### 场景 2:如果外部 API 关闭了怎么办？

如果 API 能够正常运行，那就太棒了，我们当前的代码和测试已经涵盖了这一点。

但是如果 API 关闭了呢？我们的代码将如何处理这个问题？

让我们先在代码中解决这个问题。在`book.service.js`中，让我们修改我们的`fetchBooks`函数:

```
const fetchBooks = async (query) => {
  let response

  try {
    response = await request
      .get('http://openlibrary.org/search.json')
      .query({ q: query }) // query string
  } catch(e) {
    response = e.status
  }

  if (response.status === 500) throw new Error('Open Library service down')
  else return response
} 
```

酷，让我们添加测试:

```
 it('should throw an error if the service is down', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(500)

      await expect(fetchBooks('lord of the rings')).to.be.rejected
    }) 
```

我选择在我们的服务中抛出一个错误，调用这个服务的控制器必须捕捉并处理这个错误。但是我们也可以很容易地返回 null 或空数组。这个更多的只是看你的要求。

### 场景 3:如果外部 API 没有为我们的查询找到任何东西怎么办？

如果 API 启动了，但是我们的搜索没有返回任何东西，我们将从 API 得到一个`404`响应代码。所以我们也来处理一下:

```
const fetchBooks = async (query) => {
  let response

  try {
    response = await request
      .get('http://openlibrary.org/search.json')
      .query({ q: query }) // query string
  } catch(e) {
    response = e.status
  }

  if (response.status === 404) return null
  if (response.status === 500) throw new Error('Open Library service down')
  else return response
} 
```

还有测试:

```
 it('should return null if query returns a 404', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(404)

      const response = await fetchBooks('aksdfhkahsdfkhsadkfjhskadjhf')
      expect(response).to.be.null;
    }) 
```

轻松点。

### 场景 4:如果我们的请求有问题怎么办？

我们的请求可能有几个问题:

*   我们可能会不小心忘记添加查询字符串
*   我们可能在查询中有一个坏的字符
*   我们可能缺少适当的认证令牌/头

幸运的是，开放库 API 不需要任何认证令牌。它是...良好的...“开放”。

但是，如果您确实有一个需要 JWT 令牌的服务，例如，或基本身份验证，那么最好涵盖令牌丢失或格式不正确的情况。

我们再修改一下`fetchBooks`:

```
const fetchBooks = async (query) => {
  let response

  try {
    response = await request
      .get('http://openlibrary.org/search.json')
      .query({ q: query }) // query string
  } catch(e) {
    response = e.status
  }

  if (response.status === 404) return null
  if (response.status === 500) throw new Error('Open Library service down')
  if (response.status >= 400) throw new Error('Problem with request')
  else return response
} 
```

因为我们可以覆盖许多不同的 HTTP 响应代码，并且我们可以编写许多条件检查来处理每个代码，所以这里我们只指定`if (response.status >= 400)`来捕获所有的`Bad Request` 400 级代码。

还有测试:

```
 it('should throw an error if there is a problem with the request (i.e. - 401 Unauthorized)', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(401)

      await expect(fetchBooks('lord of the rings')).to.be.rejected
    })

    it('should throw an error if there is a problem with the request (i.e. - 400 Bad Request)', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(400)

      await expect(fetchBooks('lord of the rings')).to.be.rejected
    }) 
```

### 场景 5:如果我们的应用程序不需要完整的响应怎么办？

如果我们的应用程序不需要完整的响应呢？如果我们只需要书名呢？

我们需要一个过滤器/格式功能。在`book.service.js`中，让我们添加一个`getBookTitles`函数，并将其添加到图书服务导出:

```
const getBookTitles = (searchResults) => {
  return searchResults.map(({title_suggest}) => title_suggest)
}

module.exports = {
  fetchBooks,
  getBookTitles
} 
```

还有测试:

```
 describe('getBookTitles', () => {
    it('should filter down response object to just book titles', () => {
      const titles = getBookTitles(booksFixture.docs)
      expect(titles).to.deep.equal([
        'The Lord of the Rings',
        'Lord of the Rings',
        'The Fellowship of the Ring'
      ])
    })
  }) 
```

### 包扎

以下是完整的测试列表:

```
const chai = require('chai')
const chaiAsPromised = require('chai-as-promised')
const nock = require('nock')

const { booksFixture } = require('./books.fixture')

const { fetchBooks, getBookTitles } = require('../src/book.service')

const expect = chai.expect
chai.use(chaiAsPromised)

describe('Book Service', () => {
  describe('fetchBooks', () => {
    it('should return list of books based on search string', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(200, booksFixture)

      const {body} = await fetchBooks('lord of the rings')
      expect(body).to.deep.equal({
        docs: [
          {title_suggest: 'The Lord of the Rings', cover_edition_key: 'OL9701406M'},
          {title_suggest: 'Lord of the Rings', cover_edition_key: 'OL1532643M'},
          {title_suggest: 'The Fellowship of the Ring', cover_edition_key: 'OL18299598M'}
        ]
      })
    })

    it('should throw an error if the service is down', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(500)

      await expect(fetchBooks('lord of the rings')).to.be.rejectedWith('Open Library service down')
    })

    it('should return null if query returns a 404', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(404)

      const response = await fetchBooks('aksdfhkahsdfkhsadkfjhskadjhf')
      expect(response).to.be.null;
    })

    it('should throw an error if there is a problem with the request (i.e. - 401 Unauthorized)', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(401)

      expect(fetchBooks('lord of the rings')).to.be.rejectedWith('Problem with request')
    })

    it('should throw an error if there is a problem with the request (i.e. - 400 Bad Request)', async () => {
      nock('http://openlibrary.org')
        .get('/search.json')
        .query(true)
        .reply(400)

      await expect(fetchBooks('lord of the rings')).to.be.rejectedWith('Problem with request')
    })
  })

  describe('getBookTitles', () => {
    it('should filter down response object to just book titles', () => {
      const titles = getBookTitles(booksFixture.docs)
      expect(titles).to.deep.equal([
        'The Lord of the Rings',
        'Lord of the Rings',
        'The Fellowship of the Ring'
      ])
    })
  })
}) 
```

下次当您编写一个调用外部 REST API 的节点应用程序时，使用这个方法作为编写测试的起点。这会让你更容易知道**要覆盖什么**，并帮助你弄清楚如何编写你的代码。

### 最后一件事！

测试花了我一段时间才弄明白——没有很多好的教程解释测试的**如何**和**什么**。

我只是想让事情变得简单些。因为一旦我发现如何编写几个测试，就像滚雪球一样，编写测试变得容易多了。也许...甚至...好玩？

还有更多的测试帖子在路上——如果你不仅想了解**如何**测试，还想了解**测试什么**，[注册我的简讯](https://www.coreycleary.me/about/),下一个帖子一发布就会通知你！