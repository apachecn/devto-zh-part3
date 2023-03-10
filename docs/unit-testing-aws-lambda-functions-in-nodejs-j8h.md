# Node.js 中 AWS Lambda 函数的单元测试

> 原文：<https://dev.to/jarroo/unit-testing-aws-lambda-functions-in-nodejs-j8h>

使用 AWS Lambda 函数编写后端代码——像 web 服务或其他任何东西——非常容易，特别是当您选择 Node.js 作为武器时。开始运行所需的代码量非常少，几乎是不可思议的。然而，当你构建你的 Lambda 时，复杂性会很快出现，你很快就会觉得需要添加一些测试。

单元测试是任何优秀开发人员工作流程的一部分，但是我觉得它在处理动态类型语言时尤其重要，比如普通的 Javascript。其松散类型的本质使开发变得快速，但是在进行更改或重构时，也会产生一定程度的不确定性。良好的测试覆盖率可以弥补这一点，它可以让你工作得更快。如果你能够模仿 Lambda 的依赖关系，你会非常自信地认为你成功的单元测试代表了最终的产品代码。

## 依赖注入

“依赖注入”是软件工程中使用的一个有点模糊的术语，用来描述一些非常简单的事情:

> 依赖注入是一种使类独立于其依赖项的编程技术。它通过将对象的使用与创建分离来实现这一点。

它在单元测试环境中应用时最有用，因为它使您能够模拟测试期间不应该活动的依赖关系。

在 Node.js Lambda 函数中，使用 require()函数导入依赖关系。它在函数范围内创建一个常量，指向一些外部代码。默认情况下，您将在 Node.js 文件的顶层这样做，从而有效地使该文件可以全局访问依赖项。考虑这个片段，我们在这里导入 AWS SDK，并创建 DynamoDB DocumentClient 的一个新实例:

```
const AWS = require('aws-sdk')
const documentClient = new AWS.DynamoDB.DocumentClient() 
```

当你对导入上述依赖项的代码进行单元测试时，会发生什么？在这种情况下，您的测试将建立一个到 DynamoDB 的活动连接，并可能开始向它读写数据！虽然你可能认为这本身就是一次测试，但这种情况远非理想。每个单元测试调用将

*   潜在成本
*   将数据写入活动数据库，可能会破坏其一致性
*   慢点

Richard Hyatt 在 2016 年发表的 [Medium 帖子今天仍然适用，因为它描述了我们如何通过使用 exports 对象来存储和引用依赖项，从而使依赖项加载异步且可注入。](https://medium.com/vandium-software/unit-testing-aws-lambda-functions-in-node-js-7ad6c8f5000) 

```
exports.deps = () => {
  const AWS = require('aws-sdk')
  const documentClient = new AWS.DynamoDB.DocumentClient()

  return Promise.resolve({
    dynamoBatchWrite: params => documentClient.batchWrite(params).promise()
  })
} 
```

实际的依赖项导入包含在 deps 函数范围内，通过将结果字典包装在一个承诺中来实现异步导入。这种异步性允许我们在测试期间覆盖 deps 函数，同时在生产中保持不变。

生产代码将等待顶部的依赖项，之后您将能够访问完全构建的依赖项:

```
exports.handler = async event => {
  const deps = await exports.deps()
  ...
} 
```

现在，为了测试:

```
require('chai').should()
const lambda = require('../index')
const sinon = require('sinon')

describe('importOPML', () => {
  beforeEach('mock dependencies', () => {
    const mockWriter = sinon.mock()
    mockWriter.resolves({ UnprocessedItems: [] })

    lambda.deps = () => Promise.resolve({
      dynamoBatchWrite: mockWriter
    })
  })

  it('should succeed with empty opml', async () => {
    // Using lambda here, will call the version that uses the mocked DynamoDB writer.
  }
}) 
```

这恰好是一个用西农来嘲讽的柴测，但前提是一样的。在运行每个测试块之前，执行 Before each 块，这为 lambda 准备了模拟依赖项。

就是这样。你要去比赛了！