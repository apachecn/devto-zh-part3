# 如何用 AWS Lambda 创建无服务器 API 函数

> 原文：<https://dev.to/3sanket3/how-to-create-serverless-api-functions-with-aws-lambda-4k8m>

在本教程中，我们将学习如何创建无服务器 API 函数。我们将使用[无服务器框架](https://serverless.com)并托管 AWS Lambda 的功能。

### 疑惑为什么无服务器？

请查一下 [AWS Lambda](https://aws.amazon.com/lambda/) 或者 Cloudflare 的[为什么要用无服务器](https://www.cloudflare.com/learning/serverless/why-use-serverless/)。

## 开始使用

全局安装`serverless`

```
npm install -g serverless 
```

通过在终端
上运行`serverless -v`命令检查是否安装

```
serverless -v
1.42.3 
```

## 获取样板文件

我们将使用 *aws-nodejs* 模板来创建我们的服务，比方说使用名称`testing-one`。

```
sls create --template aws-nodejs --path testing-one 
```

其中`sls`是`serverless`的快捷方式。

默认模板会有`handler.js`。它包含一个名为`hello`的虚拟函数。业务逻辑应该放在里面。下一步，我们将在 AWS Lambda 中部署相同的功能。

我们简化一下`hello`函数，返回`message`如下:

```
module.exports.hello = async event => {
  return {
    statusCode: 200,
    body: JSON.stringify(
      {
        message: `Hi, there!`
      },
      null,
      2
    )
  };
}; 
```

## 部署到 AWS

为了将这些功能部署到 AWS，我们需要在我们的机器中设置凭证。

如果它已经在你的机器中设置好了，下面的命令应该显示*访问密钥 id* 和*秘密访问密钥*

```
cat < ~/.aws/credentials

[default]
aws_access_key_id = your-access-key-id
aws_secret_access_key = your-secret-access-key 
```

否则，请按照此[视频](https://www.youtube.com/watch?v=KngM5bfpttA)设置 AWS 凭证。

要部署该功能，运行`deploy`命令

```
sls deploy 
```

如果您导航到 AWS Lambda 控制台([https://console.aws.amazon.com](https://console.aws.amazon.com)T3】服务> Lambda >功能)，您会发现`hello`功能部署在那里。(确保您在屏幕右上角选择了正确的区域)

[![lambda console](img/60bb59c43988de020147ad4c52c237a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NachS-tR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/53xuqhvqz8rald35do32.PNG)

显示的函数名`testing-one-dev-hello`格式如下。

< service name > - < stage > - < function name >

## 调用部署的功能

```
sls invoke -f hello

{
 "statusCode": 200,
 "body": "{\n  \"message\": \"Hi, there!\"\n}"
} 
```

其中`-f`是`-function`的简写。

所以函数`hello`被部署并运行。让我们把它变成一个 REST API 函数。

## 使用函数作为 REST API

*事件*是触发你的功能运行的事情。其中一种事件是 HTTP 事件。HTTP 事件可以由 HTTP 端点之一生成。

### 创建获取端点

假设我们想在对路径`/hello`发出一个`GET` HTTP 请求时触发`hello`函数。

也就是`GET : https://someurl.com/hello`

文件`serverless.yml`正是用于无服务器项目中的这种配置。

> YAML(。yml) 是一种数据序列化语言，用于配置文件。它就像 JSON，但是更容易阅读。

在`serverless.yml`中，改变如下所示的`functions`部分。

> 请确保您有符合 YAML 标准的适当[压痕。也可以在 http://nodeca.github.io/js-yaml/](https://www.tutorialspoint.com/yaml/yaml_basics.htm)
> 边玩边学

```
functions:
  hello:
    handler: handler.hello
    events:
      - http:
          path: hello
          method: get 
```

### 部署变更

```
sls deploy 
```

deploy 命令输出应该返回 URL 端点，我们应该使用这些端点来触发函数。

[![lambda GET endpoints](img/a363b33343712106cd77d6ed8317678a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ccIdUvHy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gybrnub4sgwu6micu9up.png)

您可以在浏览器中浏览端点。它将调用 lambda 函数并返回下面的结果。

```
{  "message":  "Hi, there!"  } 
```

### 接受查询字符串参数

您还可以传递查询字符串参数，并在您的业务逻辑中处理它。

让我们更新`hello`函数来处理作为查询字符串
传入的`name`参数

```
module.exports.hello = async event => {
  if (event.queryStringParameters && event.queryStringParameters.name) {
    return {
      statusCode: 200,
      body: JSON.stringify(
        {
          message: `Hi, ${event.queryStringParameters.name}!`
        },
        null,
        2
      )
    };
  }
  return {
    statusCode: 200,
    body: JSON.stringify(
      {
        message: `Hi, there!`
      },
      null,
      2
    )
  };
}; 
```

现在，如果您点击下面的 URL，您应该会收到如下输出

`https://some-random-text.execute-api.us-east-1.amazonaws.com/dev/hello?name=Sanket`

```
{  "message":  "Hi, Sanket!"  } 
```

### 创建发布端点

您可以在`serverless.yml`中将功能(比如说`submitForm`)配置为 POST，如下图

```
functions:
  hello:
    handler: handler.hello
    events:
      - http:
          path: hello
          method: get
  # POST endpoint
  submitForm:
    handler: handler.submitForm
    events:
      - http:
          path: submitForm
          method: post 
```

`submitForm`功能将为

```
module.exports.submitForm = async event => {
  if (event.body) {
    return {
      statusCode: 200,
      body: JSON.stringify(
        {
          data: JSON.parse(event.body)
        },
        null,
        2
      )
    };
  }

  return {
    statusCode: 200,
    body: JSON.stringify(
      {
        message: "Received nothing"
      },
      null,
      2
    )
  };
}; 
```

使用`sls deploy`部署服务

[![lambda POST endpoints](img/42ba59af33814bb0c6f5b1334acaf7c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6pCfdzDO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s1z6toi0nxey2vxmbhkj.png)

您可以使用类似于 [Postman](https://www.getpostman.com/) 的工具将数据发送到端点。它应该用正文中发送的数据来响应。

[![postman](img/d21c61640b6be70b50ff6531d05974f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OVrJHY6a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fiajnayofdp4ev0j84qu.png)

暂时就这样了。我希望本教程对开始使用无服务器功能有所帮助。

你可以在[https://github.com/3sanket3/serverless-aws-api-gateway](https://github.com/3sanket3/serverless-aws-api-gateway)找到这里解释的代码