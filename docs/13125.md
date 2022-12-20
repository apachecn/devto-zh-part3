# 自动化测试、构建和发布类型脚本库

> 原文：<https://dev.to/webhookrelay/automating-testing-building-and-publishing-of-typescript-libraries-5652>

不管你是在做副业项目、小型开源库还是全职工作项目，自动化构建、测试和发布都可以极大地改善你的生活。然后，您可以专注于代码质量、特性，或者在完成一项任务后休息一会儿，而不是试图记住发布所需的所有步骤。

在我之前的文章中，我演示了如何用 Drone 建立一个[自托管 CI/CD 解决方案。您不需要强大的 CI 服务器或昂贵的虚拟机来运行它，您可以轻松地在您的笔记本电脑上运行一台 CI 服务器来在后台执行这些任务，速度比免费的替代品快得多，同时也获得了更大的灵活性。](https://dev.to/krusenas/setting-up-simple-self-hosted--fast-cicd-solution-with-drone-for-homelab-or-office-1fnd)

现在，我想分享一些我最近实现的实用管道。

**简短的免责声明:**我不认为自己是一个有经验的 TypeScript/JavaScript 开发人员，我总是倾向于去，但在这种情况下，我需要写一些 JavaScript，所以这是一个最终尝试 TypeScript 的好机会:)包本身可以在这里找到，这是一个简单的库，它允许你在你的应用程序中接收 webhooks，而不将其暴露给互联网。

## 测试库

我的库测试可能不是你在标准库中找到的。因为它们依赖于 SaaS 服务(来接收这些公共的 web 钩子)，所以它必须从环境中获取一些凭证并执行异步操作。我就是在那里了解到`done`回拨:

```
it('should be able to forward the webhook', (done) => {
    var payload = "payload-" + Math.floor((Math.random() * 100000) + 1);
    // creating a handler
    var handler = function (data: string) {
      var msg = JSON.parse(data);
      if (msg.type === 'status' && msg.status == 'subscribed') { // <---- once received, send a webhook
        var dispatchWebhook = function() {
          axios.post('https://my.webhookrelay.com/v1/webhooks/9c1f0997-1a34-4357-8a88-87f604daeca9', payload)
          .then(function (response) {          
            expect(response.status).to.equal(200)
          })
        }
        setTimeout(dispatchWebhook, 1000)

      }
      if (msg.type === 'webhook' && msg.body === payload) {
        expect(msg.method).to.equal('POST');
        done(); // <---- once webhook received, end the test case
        client.disconnect();
      }
    }

    var client = new WebhookRelayClient(key, secret, [testBucket], handler)
    client.connect(); // <---- connecting so our handler will be called
  }); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这并不真正与自动化相关，但它可能对某些人有用:)

## 建筑图书馆

使用无人机时，一切都在 Docker 容器中运行。这样做的主要好处是，获得可重现的构建变得很简单。在我们的案例中，第一步包括:

*   安装依赖项
*   用`tsc`构建(TypeScript 需要转换回 JavaScript)
*   运行测试

我们的无人机文件看起来像:

```
kind: pipeline
name: default

steps:

- name: build
  image: node:latest
  environment: # supplying environment variables for testing
    RELAY_KEY:
      from_secret: relay_key
    RELAY_SECRET:
      from_secret: relay_secret
    RELAY_BUCKET: ws-client-tests
  commands:
    - npm install
    - npm run build
    - make test 
```

Enter fullscreen mode Exit fullscreen mode

在这里，`npm run build`其实只是:

```
"scripts": {
    "build": "tsc"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

并且在 Makefile 中`make test` :

```
test:
    ./node_modules/mocha/bin/mocha --reporter spec --compilers ts:ts-node/register src/*.test.ts 
```

Enter fullscreen mode Exit fullscreen mode

## 发布到 npm 注册表

自动化发布包也总是好的。这样你将几乎不费吹灰之力获得一个好的发布过程。当您对软件包功能满意时，您只需标记一个 Github 版本，Drone 就会构建、测试并发布您的软件包到 npm registry:

```
- name: publish
  image: node:latest  
  environment:
    NPM_TOKEN:
      from_secret: npm_token
  commands:
    - make drone-publish
  when:
    event: [ tag ] 
```

Enter fullscreen mode Exit fullscreen mode

环境变量`NPM_TOKEN`是您可以为您的帐户生成的令牌。

`make drone-publish`命令看起来像:

```
drone-publish:
    echo "//registry.npmjs.org/:_authToken=${NPM_TOKEN}" > ~/.npmrc 
    npm publish 
```

Enter fullscreen mode Exit fullscreen mode

设置`.npmrc`文件很重要，因为没有它发布将无法工作。奇怪？是的。

## 奖励:通知

这最后一步在我所有的无人机管道上重复，这是对一个空闲通道的通知:

```
- name: slack
  image: plugins/slack
  when:
    status: [ success, failure ]
  settings:
    webhook:
      from_secret: slack_url
    channel: general
    username: drone 
```

Enter fullscreen mode Exit fullscreen mode

为此，获取你的 Slack 的 webhook URL 并创建一个`slack_url`秘密。

## 包装完毕

初始设置一切需要 30-90 分钟，一旦 CI 系统运行，随后的存储库可以在几秒钟内添加。即使你认为每次发布时运行`npm run build`和`npm publish`只需要 1 分钟的时间，自动化这一过程将极大地改善你的开发人员体验和总体生活:)将自动化构建和发布与测试相结合将确保只有一条路可以让你的包发布。我见过很多这样的情况，错过了一个 TypeScript 包构建步骤，发布了以前的版本。或者，在“快速修复”并提交到注册表后，由于有人忘记运行测试，包被破坏了。或者，仅仅考虑一下，在下一年你可能会做 200 个发布，这些发布会通过自动化节省很多时间！