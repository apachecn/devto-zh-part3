# 用 Mocha 进行 Javascript 测试

> 原文：<https://dev.to/edezekiel/javascript-testing-with-mocha-25e8>

[![](img/dcc71cbaab1ea9d06c8db6a689e21d60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iDU8X6PX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AEgEulBNpsN6Nw_bwy33NKA.jpeg)

### Javascript 测试

去编码训练营我最喜欢的部分之一是所有的自动化测试。查看我的代码是否包含错误，并得到可能导致这些错误的提示，这非常有帮助。

现在我已经毕业了，该由我来为自己的项目编写这些测试了！

我已经用 Capybara 编写了一些 Ruby 测试，所以现在我想尝试编写 Javascript 测试。

### 摩卡

为了纪念“五月四日与你同在”，我这篇文章的另一个标题是“愿摩卡咖啡与你同在”我发现摩卡很容易上手，使用起来也很愉快。

Mocha 是一个 Javascript 测试框架。它用于在浏览器中测试 JS，以及在节点 JS 环境中执行的 JS。

### 我的第一次摩卡测试

我决定编写一个将在 node JS 环境中执行的 Mocha 测试。

代码如下:

```
// Require the built in 'assertion' library
 var assert = require('assert');

describe('Compare', function() {
 describe('Star Wars Quotes', function() {
 it('should test whether two data types are equivalent ', function() {
 assert.equal(true, typeof "May the Fourth Be With You" === typeof "Help me Obi Wan Kenobi, you're my only hope")
 })
 })
 }) 
```

这个测试通过了，因为两个操作数都是字符串，所以函数将返回 true 的预期输出。

### 吸取教训

mochajs.org 上的入门指南对如何进行你的第一次摩卡测试有非常基本的指导。

然而，每当我试图运行“npm test”时，我总是收到关于我的 package.json 文件的错误消息。错误消息声称“没有这样的文件或目录”存在。最后，我从项目目录中运行了命令“npm init”。它在我的项目目录中创建了一个 package.json 文件并修复了这个问题。

### 资源

如果你感兴趣的话，有很多学习摩卡的好资源。当然，在[mochajs.org](https://mochajs.org/#getting-started)有官方文件。

还有一个由 [codeburst](https://codeburst.io/how-to-test-javascript-with-mocha-the-basics-80132324752e) 编写的关于 medium 的更深入的教程