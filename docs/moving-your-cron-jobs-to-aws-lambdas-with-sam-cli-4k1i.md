# 使用 SAM cli 将 cron 作业迁移到 AWS lambda

> 原文：<https://dev.to/tathagata/moving-your-cron-jobs-to-aws-lambdas-with-sam-cli-4k1i>

时不时地，你需要一个脚本定期运行，做一些有趣的事情。在无服务器计算出现之前，cron 作业实际上是这样做的。但是现在我们有几个更划算的选择。ECS 调度任务和[是 AWS 中任务调度的两种无服务器替代方案。根据您的工作量，您可以决定哪个选项最适合您。Lambdas 具有 15 分钟运行时间限制，非常适合小型工作负载。让我们探索一下如何让它工作起来。](https://aws.amazon.com/lambda/)

## 安装 SAM cli

AWS Sam cli 是一款以可爱的花栗鼠为吉祥物的工具。它简化了 lambdas 的构建和部署。

```
pip install --user aws-sam-cli 
```

## 创建项目

```
sam init --name lambda-cron --runtime python3.7
[+] Initializing project structure...

Project generated: ./lambda-cron

Steps you can take next within the project folder
===================================================
[*] Invoke Function: sam local invoke HelloWorldFunction --event event.json
[*] Start API Gateway locally: sam local start-api

Read lambda-cron/README.md for further instructions

[*] Project initialization is now complete 
```

让我们看看生成的代码

```
tree lambda-cron/
lambda-cron/
|-- README.md
|-- event.json
|-- hello_world
|   |-- __init__.py
|   |-- __pycache__
|   |   |-- __init__.cpython-37.pyc
|   |   `-- app.cpython-37.pyc
|   |-- app.py
|   `-- requirements.txt
|-- template.yaml
`-- tests
    `-- unit
        |-- __init__.py
        |-- __pycache__
        |   |-- __init__.cpython-37.pyc
        |   `-- test_handler.cpython-37.pyc
        `-- test_handler.py

5 directories, 12 files 
```

与 cron 标签中的一行相比，这似乎是对工件的过度破坏。但是，如果你认为你正在摆脱整个服务器，这看起来并不那么不合理。我们来看看那些不明显的文件。

### event.json

sam 的脚手架瞄准了最流行的 lambda 用例——一个服务于 API 网关端点的函数。我们可以忽略这个。

### app.py

这个文件非常重要，因为这是您的代码所在的地方。默认情况下，`lambda_handler`是您填充的函数的名称。现在，我们只限于打印一个字符串。

```
def lambda_handler(event, context):
    print("Do something interesting!") 
```

### 需求. txt

标准的东西——你的包依赖在这里。

### template.yaml

该文件负责协调无服务器性。它以声明的方式指定了组成您的`stack`的组件，其中堆栈是您作为一个单元部署在 AWS CloudFromation 中的整个软件架构。如果这不是魔法，我不知道什么是。我是云形成的忠实粉丝。

注意，生成该文件是为了构建一个 lambda 函数，该函数由对 API 网关端点的 HTTP 请求触发。但是我们的目标是按预定的时间间隔运行它。看一下描述这些事件的部分。

```
 Events:
        HelloWorld:
          Type: Api # More info about API Event Source: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#api
          Properties:
            Path: /hello
            Method: get 
```

我们需要用时间表信息替换它。Sam 模板[支持`cron`语法和更简单的`rate`表达式中的](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#schedule)调度表达式。

每分钟使用率

```
 Events:
        HelloWorld:
          Type: Schedule
          Properties:
            Schedule: rate(1 minute) 
```

每一分钟都在使用 cron

```
 Events:
        HelloWorld:
          Type: Schedule
          Properties:
            Schedule: cron(1 * * * ? *) 
```

### [T1】test _ handler . py](#testhandlerpy)

这是为 pytest 预先生成的 unittest 存根。可以像往常一样安装 pytest ( `pip install pytest`)并运行`pytest`。

## 打造

随着测试的通过，现在是时候进行构建了

```
cd lambda-cron/
t lambda-cron $ sam build --use-container
2019-03-30 19:42:24 Starting Build inside a container
2019-03-30 19:42:24 Building resource 'HelloWorldFunction'

Fetching lambci/lambda:build-python3.7 Docker container image......
2019-03-30 19:42:25 Mounting /Users/t/projects/python/playground/lambda-deep-dive/lambda-cron/hello_world as /tmp/samcli/source:ro inside runtime container

Build Succeeded

Built Artifacts  : .aws-sam/build
Built Template   : .aws-sam/build/template.yaml

Commands you can use next
=========================
[*] Invoke Function: sam local invoke
[*] Package: sam package --s3-bucket <yourbucket>

Running PythonPipBuilder:ResolveDependencies
Running PythonPipBuilder:CopySource 
```

注意，第一次运行时，它会下载运行所需的 docker 映像。

## 本地测试

sam 提供的最好的功能之一是能够在 docker 容器中本地测试您的代码。这使得快速迭代成为可能，而不必实际部署在 AWS lambda 环境中。

```
sam local invoke --no-event -t template.yaml
2019-03-30 21:01:43 Found credentials in shared credentials file: ~/.aws/credentials
2019-03-30 21:01:43 Invoking app.lambda_handler (python3.7)

Fetching lambci/lambda:python3.7 Docker container image....................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................
2019-03-30 21:02:53 Mounting /Users/t/projects/python/playground/lambda-deep-dive/lambda-cron/hello_world as /var/task:ro inside runtime container
START RequestId: 52fdfc07-2182-154f-163f-5f0f9a621d72 Version: $LATEST
Do something intersting!
END RequestId: 52fdfc07-2182-154f-163f-5f0f9a621d72
REPORT RequestId: 52fdfc07-2182-154f-163f-5f0f9a621d72  Duration: 16.52 ms  Billed Duration: 100 ms Memory Size: 128 MB Max Memory Used: 22 MB
null 
```

## 打包你的代码

让我们创建一个桶，并将其传递给打包命令。注意，我们正在传递一个名为 packaged.yaml 的文件，它是生成的云形成模板，我们将在下一个命令中使用它来部署它。

```
aws s3 mb s3://lambda-cron-bucket
sam package --s3-bucket lambda-cron-bucket --output-template-file packaged.yaml 
```

## 部署

最后部署 cron 作业。您需要为您的栈提供一个名称，并通过传递`CAPABILITY_IAM`标志明确地告诉 cloudformation 您可以创建 IAM 相关的资源。

```
aws cloudformation deploy --template-file /absolute/path/packaged.yaml --stack-name lambda-cron --capabilities CAPABILITY_IAM 
```

## 验证

让我们看看我们的 cron 是否每分钟都在执行。

```
sam logs -n HelloWorldFunction --stack-name lambda-cron --tail 
```

您应该会看到如下内容:

```
2019-03-30 20:35:19 Found credentials in shared credentials file: ~/.aws/credentials
2019/03/31/[$LATEST]7cc1e71b6eb14c89881cb91d6c29b920 2019-03-31T01:29:12.613000 START RequestId: c2c8a898-dfab-450f-944b-d43d77741c35 Version: $LATEST
2019/03/31/[$LATEST]7cc1e71b6eb14c89881cb91d6c29b920 2019-03-31T01:29:12.618000 Do something intersting!
2019/03/31/[$LATEST]7cc1e71b6eb14c89881cb91d6c29b920 2019-03-31T01:29:12.638000 END RequestId: c2c8a898-dfab-450f-944b-d43d77741c35
2019/03/31/[$LATEST]7cc1e71b6eb14c89881cb91d6c29b920 2019-03-31T01:29:12.638000 REPORT RequestId: c2c8a898-dfab-450f-944b-d43d77741c35  Duration: 24.83 ms  Billed Duration: 100 ms     Memory Size: 128 MB Max Memory Used: 63 MB
2019/03/31/[$LATEST]7cc1e71b6eb14c89881cb91d6c29b920 2019-03-31T01:30:12.272000 START RequestId: 93645e34-850b-47b0-a4a3-00d10e502057 Version: $LATEST
2019/03/31/[$LATEST]7cc1e71b6eb14c89881cb91d6c29b920 2019-03-31T01:30:12.278000 Do something intersting!
2019/03/31/[$LATEST]7cc1e71b6eb14c89881cb91d6c29b920 2019-03-31T01:30:12.298000 END 
... 
```

祝贺你的第一个无服务器 cron 工作！

## 封面图片是怎么回事？

与我的其他 dev.to 帖子的主题保持一致，它与帖子无关。从我的阳台上可以看到我们美丽的城市芝加哥。