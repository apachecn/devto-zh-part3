# 如何在 NodeJS 中处理海量数据

> 原文：<https://dev.to/itmayziii/how-to-process-epic-amounts-of-data-in-nodejs-16hl>

## 前言

如果你像我一样，那么你喜欢使用 NodeJS 做很多不同的事情，比如处理 HTTP 请求、制作 CLI 工具、物联网等等。您可能遇到过需要同时发送多个 HTTP 请求的情况，如果您没有遇到过这种情况，请不要担心，因为有一天您会遇到的。利用 javascript 的异步特性，大多数有经验的开发人员将能够同时发送几个 HTTP 请求。**但是**当你需要发送几百万个 HTTP 请求时会发生什么？这个问题甚至可能会绊倒经验丰富的 javascript 开发人员，因为它触及了一个大多数人不必经常处理的问题，即处理大数据。

您可能已经猜到，如果您尝试异步发送一百万个 HTTP 请求，那么您的程序将会崩溃，您的猜测将是正确的。事实上，在一百万个 HTTP 请求之前，你的程序可能会崩溃。仅仅因为某个东西是异步的，并不意味着它可以处理无限量的数据。在本文的剩余部分，我希望向您展示如何高效地处理任何大小的数据，而不会导致系统资源耗尽。我们将使用 [NodeJS Streams](https://nodejs.org/api/stream.html) 这是我们的秘方，所以如果你需要 Streams 指南，那么[这是我最喜欢的文章](https://medium.freecodecamp.org/node-js-streams-everything-you-need-to-know-c9141306be93)。与那篇文章不同，我不打算深入研究流是如何工作的，除非是在较高的层次上，相反，我的目标是给你一个使用流处理大数据的实际例子。

## 直奔成品代码

如果你赶时间或者不喜欢阅读，那么这里是我们将要建立的 Github 库。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png)[itmayziii](https://github.com/itmayziii)/[dev-to-nodejs-epic-data](https://github.com/itmayziii/dev-to-nodejs-epic-data)

<article class="markdown-body entry-content p-5" itemprop="text">

# 如何在 NodeJS 中处理海量数据

</article>

[View on GitHub](https://github.com/itmayziii/dev-to-nodejs-epic-data)

## 我们将建造什么

1.  我们将从一个文件中读取 Github 用户名列表
2.  对于每个 Github 用户名，我们希望调用 github API 并获得它们的库列表。我们将只使用 12 个用户名的较小列表，因为我不希望一群读者滥发 Github 的 API，因为这个概念对于任何数量的数据都是一样的。
3.  将这些数据写入我们的数据库，但是为了避免这一步设置的复杂性，我们将只是将数据写入一个文件。
4.  最后，我们将重构，使事情更有性能。

我们将使用 NodeJS 流来完成所有这些工作，如果操作正确的话，会有[背压](https://nodejs.org/en/docs/guides/backpressuring-in-streams/)的概念，这有助于我们以不会耗尽内存的方式使用 NodeJS 资源。

### 1。读取 Github 用户名文件

您可以在[存储库](https://github.com/itmayziii/dev-to-nodejs-epic-data/blob/master/github-usernames.csv)中的示例文件中找到该文件

*src/main.js*

```
const fs = require('fs')
const path = require('path')
const CsvParser = require('csv-parse')

const readGithubUsernamesStream = fs.createReadStream(path.resolve(__dirname, '../github-usernames.csv'))
const csvParser = new CsvParser({})

let githubUsernames = ''
readGithubUsernamesStream
  .pipe(csvParser)
  .on('data', (data) => githubUsernames += data)
  .on('end', () => console.log(githubUsernames))

// Outputs - itmayziii,dhershman1,HetaRZinzuvadia,joeswislocki,justinvoelkel,mandarm2593,mfrost503,wmontgomery,kentcdodds,gaearon,btholt,paulirish,ryanflorence 
```

### 2。从 Github 获取存储库列表

NodeJS 为我们提供了 [createReadStream](https://nodejs.org/api/fs.html#fs_fs_createreadstream_path_options) 来作为流读取我们的文件，这很好，但现在我们需要自己的流来获取用户名列表，读取它，并将其转换为 github 结果。

*对于这一步，我们将使用 [axios](https://www.npmjs.com/package/axios) 来发出 HTTP 请求*

*src/transform-用户名到 github-repos.js*

```
const axios = require('axios')
const stream = require('stream')

module.exports = class TransformUsernameToGithubRepos extends stream.Transform {
  constructor (options = {}) {
    super({ ...options, objectMode: true })
  }

  _transform (chunk, encoding, callback) {
    const username = chunk
    this.getGithubRepositoriesForUser(username)
      .then((response) => {
        let repositories = []
        if (response.data) {
          repositories = response.data.map((repository) => repository.name)
        }

        this.push(JSON.stringify({
          username,
          repositories
        }))
        callback()
      })
      .catch(callback)
  }

  getGithubRepositoriesForUser (username) {
    return axios.get(`https://api.github.com/users/${username}/repos`, {
      headers: {
        Authorization: `Token ${process.env.GITHUB_ACCESS_TOKEN}`
      }
    })
  }
} 
```

并更改我们的 *src/main.js*

```
const fs = require('fs')
const path = require('path')
const CsvParser = require('csv-parse')
const TransformUsernameToGithubRepos = require('./transform-username-to-github-repos')

const readGithubUsernamesStream = fs.createReadStream(path.resolve(__dirname, '../github-usernames.csv'))
const csvParser = new CsvParser({ columns: false })
const transformUsernameToGithubRepos = new TransformUsernameToGithubRepos()

let githubUserRepositories = []
readGithubUsernamesStream
  .pipe(csvParser)
  .pipe(transformUsernameToGithubRepos)
  .on('data', (data) => githubUserRepositories.push(data))
  .on('end', () => console.log(githubUserRepositories)) 
```

我们改变了很多东西，所以让我们打开包装。我们创建了一个转换流，上面有一个`_transform`方法。当我们将 CSV 文件传输到这个转换流时，这个`_transform`方法将被调用。一旦使用传递给它的用户名调用了`_tranform`方法，我们就获取用户名并向 github 请求该用户的所有存储库。然后，我们用`this.push(...)`将结果发送给流中的下一个对象。我们还没有蒸汽管道的下一步，所以我们开始监听`data`事件，在那里我们收集数据并登录 main.js

### 3。将我们的用户库写到一个文件中

*src/main.js*

```
const fs = require('fs')
const path = require('path')
const CsvParser = require('csv-parse')
const TransformUsernameToGithubRepos = require('./transform-username-to-github-repos')

const readGithubUsernamesStream = fs.createReadStream(path.resolve(__dirname, '../github-usernames.csv'))
const csvParser = new CsvParser({ columns: false })
const transformUsernameToGithubRepos = new TransformUsernameToGithubRepos()
const writeStream = fs.createWriteStream(path.resolve(__dirname, '../github-user-repositories.txt'))

let githubUserRepositories = []
readGithubUsernamesStream
  .pipe(csvParser)
  .pipe(transformUsernameToGithubRepos)
  .pipe(writeStream)
  .on('end', () => process.exit()) 
```

这是一个简单的步骤，我们只需创建一个写流，将内容写入一个 txt 文件。

### 4。重构

我们所拥有的是可行的，但远非理想。如果你看看代码，它的效率非常低。

*   它一次只做一个 HTTP 请求，不能同时做一百万个 HTTP 请求并不意味着我们不能做也许一百个。在本例中，出于演示目的，我们将每个管道演练限制为 5 个。
*   代码的错误处理能力也很差

让我们继续解决这些问题，从每个管道的多个 HTTP 请求演练开始

*src/transform-用户名到 github-repos.js*

```
const axios = require('axios')
const stream = require('stream')

module.exports = class TransformUsernameToGithubRepos extends stream.Transform {
  constructor (options = {}) {
    super({ ...options, objectMode: true })
    this.requests = []
  }

  _transform (chunk, encoding, callback) {
    const username = chunk[0]
    const githubRequest = this.getGithubRepositoriesForUser(username)
    this.requests.push(this.prepareGithubRequest(username, githubRequest))
    if (this.requests.length < 5) {
      return callback()
    }

    this.processRequests(callback)
  }

  _flush (callback) {
    this.processRequests(callback)
  }

  getGithubRepositoriesForUser (username) {
    return axios.get(`https://api.github.com/users/${username}/repos`, {
      headers: {
        Authorization: `Token ${process.env.GITHUB_ACCESS_TOKEN}`
      }
    })
  }

  prepareGithubRequest (username, githubRequest) {
    return githubRequest
      .then((response) => {
        let repositories = []
        if (response.data) {
          repositories = response.data.map((repository) => repository.name)
        }

        return {
          username,
          repositories
        }
      })
  }

  processRequests (callback) {
    return Promise.all(this.requests)
      .then((responses) => {
        this.requests = []

        this.push(responses.reduce((accumulator, currentValue) => {
          return accumulator + JSON.stringify(currentValue)
        }, ''))
        callback()
      })
      .catch(callback)
  }
} 
```

我们又做了很多，让我们回顾一下发生了什么。我们修改了我们的`_tranform`方法来调用 Github API，然后将承诺放入一个数组中，如果总的累积承诺小于 5，我们就继续。基本上，在我们告诉 transform 通过流推送数据之前，我们调用 Github 5 次，这可以在方法`processRequests`中找到。我们已经成功地改变了管道，每次处理 5 个请求，而不是 1 个，这是一个巨大的性能提升。

我们可以想象一下，如果我们处理 100 万条记录，而不是 5 条，我们使用 100 条，所以我们会在几乎相同的时间发送 100 个 HTTP 请求，并等待它们全部解决，然后再发送 100 个。这是处理大量数据的一种非常有效/节省资源的方式。

但是我们还没有完成，我们仍然需要更好的错误处理，为此我们将利用本机 NodeJS [管道](https://nodejs.org/api/stream.html#stream_stream_pipeline_streams_callback)函数。

> pipeline——一种模块方法，用于在转发错误的流之间建立管道，并在管道完成时进行适当的清理和提供回调。

*src/main.js*

```
const fs = require('fs')
const path = require('path')
const CsvParser = require('csv-parse')
const TransformUsernameToGithubRepos = require('./transform-username-to-github-repos')
const stream = require('stream')

const readGithubUsernamesStream = fs.createReadStream(path.resolve(__dirname, '../github-usernames.csv'))
const csvParser = new CsvParser({ columns: false })
const transformUsernameToGithubRepos = new TransformUsernameToGithubRepos()
const writeStream = fs.createWriteStream(path.resolve(__dirname, '../github-user-repositories.txt'))

stream.pipeline(
  readGithubUsernamesStream,
  csvParser,
  transformUsernameToGithubRepos,
  writeStream,
  (error) => {
    if (error) {
      console.error('error ', error)
      return process.exit(1)
    }

    process.exit()
  }
) 
```

## 结论

NodeJS 流允许我们有效地拥有一个管道，数据从一个点开始，一直流到最后。使用背压，它来自于简单地实现 NodeJS 已经建立的流，我们有效地使用计算机的资源，同时处理非常大的数据集。我知道这种方法的工作原理，因为我已经用它处理了来自 CSV 的超过 1000 万条记录，调用 API 获得额外的数据，然后将结果存储在数据库中，就像我们在本文中所做的那样。流本身是有效的，但是如果你真的想加快速度，我会考虑将[子进程](https://nodejs.org/api/child_process.html)与流结合起来以获得最大效率。

封面照片致谢——乔纳森·肯珀在 [unsplash](https://unsplash.com/photos/naiJh3BP6bY)