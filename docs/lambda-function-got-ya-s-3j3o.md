# Lambda 函数得到了 Ya 的

> 原文：<https://dev.to/kameronkales/lambda-function-got-ya-s-3j3o>

我正在为一家注重隐私的初创公司开发新产品。我们保护客户免受日志记录、跟踪和所有我们不喜欢的坏事情的影响。

我们以 chrome 扩展作为 MVP 开始。

***(如果你知道一个拥有> 30，000 用户的 chrome 扩展在> 18 个月内没有更新，给我发邮件。我会支付介绍费。)***

在我们的 chrome 扩展中，我们将做一些不同的事情。主要是保护你的历史。为此，我们需要:

*   帐户创建
*   帐户登录
*   要检查的 API

我不想管理这些天来随着向客户提供服务而来的所有典型的 ECS/Docker/ELB/Route53 废话。所以我决定以无服务器的方式编写这些服务。

这篇文章详细介绍了我犯的一些常见错误，以及我是如何改正的。

需要注意的一点是，我通常不喜欢添加引入另一种“方言”的包来完成工作。当我这样做的时候，它经常抽象出实际发生的事情，使调试变得很困难。

我想尽可能地呆在纯 javascript，python 或者 go 里。bug 更容易预测，文档也更清晰。这并不是说我的方法是最好的，只是要知道这个博客将只关注我个人的方法。

最后一个管理问题:我讨厌不展示完整代码的博客。我们会的。如果你需要繁殖的东西少了一半，那也没用。

首先要意识到的是，当你创建一个 lambda 函数时，你上传了一个具有平面文件结构的压缩文件到 Lambda。

如果您使用 Node，这意味着您必须压缩 node_modules。如果你正在使用 Python，你将对你的 requirements.txt 包做同样的事情。

有一些简单的方法可以做到这一点:

在 node = >中，我创建了一个名为 build_upload.sh 的文件

```
zip -r ./dev/lambda_function.zip ./node_modules/* ./main.js -x "./dev" && aws s3 cp ./dev/lambda_function.zip s3://$1-$2/$2/v$3/lambda_function.zip 
```

这个 bash 文件包含 3 个变量:

*   $1 =阶段(例如，开发)
*   $2 =集成名称(例如，条带集成)
*   $3 =版本号(例如，1.0.0)

我选择这样设置这些变量。s3://$1-2 部分组合起来与我的存储桶名称相对应。如果您对存储桶的命名不同，请更改示例！

填充了变量的工作示例如下:

bash build_upload.sh dev 条带集成 1.0.0

这将从上面填充代码并产生:

```
zip -r ./dev/lambda_function.zip ./node_modules/* ./main.js -x "./dev" && aws s3 cp ./dev/lambda_function.zip s3://dev-stripe-integration/stripe-integration/v1.0.0/lambda_function.zip 
```

这将显示 s3 存储桶中的版本控制。这使您可以在任何需要的时候回滚。您可以使用无数的其他设置选项，但这是让构建和部署序列正常运行的最简单的方法。

在 python 中，

```
zip -r9 ../lambda.zip * -x "bin/*" requirements.txt setup.cfg 
```

我建议使用 Terraform 来管理这一切。最好的方法是将代码上传到 s3 bucket = >通过 Terraform 获取最新版本= >部署到 Lambda。

我在上面提供的节点示例中展示了如何做到这一点。python 只在本地构建更新后的 zip 文件。

这里是 Terraform 资源的块:

```
resource "aws_lambda_function" "check_file_lambda" {
    filename = "${var.function_path}"
    function_name = "${var.function_name}"
    role = "${aws_iam_role.check_file_lambda.arn}"
    handler = "${var.handler}"
    runtime = "${var.runtime}"
    timeout = "${var.timeout}"
    source_code_hash = "${base64sha256(file("${var.function_path}"))}"
} 
```

在 Terraform 中，您有一个名为 variables.tfvars 的文件。所有提到${var.function_path}或类似的内容都是存储在该文件中的变量。这些变量的语法如下:

```
variable "function_path" {
    description: "The path to the lambda function package you're deploying"
} 
```

您可以另外添加一个默认字段:

```
variable "function_path" {
    description: "The path to the lambda function package you're deploying"
    default: "../../lambda_function.zip"
} 
```

如果没有默认字段，当您运行 Terraform 计划或 Terraform 应用时，命令行将提示您输入默认值的定义。

接下来，我们要特别注意的一个字段是 source_code_hash。当我们运行 Terraform apply 时，这会将我们的源代码散列并上传到 lambda 新版本。

你也可以用其他方式，但是因为我使用 Terraform 做所有的事情(s3、函数、cronjobs、数据库、路由等等),所以我通过它部署变更。# TeamNeverUseAWSUI

这个神奇的小字段将使我能够压缩更改并从任何地方重新部署。

您可能会遇到无法在在线编辑器中编辑代码的情况。我的 python 函数不允许，但我的节点包允许。对于这一点，我目前没有很好的解决方案。但是如果有人知道，请告诉我！

我喜欢写基于函数的代码。这意味着我的代码经常是这样的:

```
def initial_function():
        blah blah blah 
        blah blah blah 
        format_data(blah)

def format_data(blah):
        blah2 blah2 blah2
        blah2 blah2 blah2
        insert_data(blah2)

def insert_data(blah2):
        blah3 blah3 blah3 
        blah3 blah3 blah3 
```

我早期犯的一个错误是不太理解如何将它集成到 lambda 函数语法中:

```
def handler(event, context): 
```

为此，我修改了上面的代码，使处理函数调用 initial_function。

```
def handler(event, context):
    initial_function()

def initial_function():
        blah blah blah 
        blah blah blah 
        format_data(blah)

def format_data(blah):
        blah2 blah2 blah2
        blah2 blah2 blah2
        insert_data(blah2)

def insert_data(blah2):
        blah3 blah3 blah3 
        blah3 blah3 blah3
        return 
```

如果需要，可以从事件中传递参数。这是一个基本的例子，展示了如何让你的本地代码在 Lambda 中工作。

我对下面显示的节点做了完全相同的操作:

一个关键的提醒:一旦你的函数完成，记得调用回调。回调(null，响应如下所示)

```
exports.handler = function (event, context, callback){
    .......... bunch of code here 
    .......... bunch of code here
    .......... bunch of code here
    .......... bunch of code here
    callback(null, response) 
```

一旦你做到这一步，你应该有一个可以通过控制台测试的 lambda 函数。你只需点击 test 按钮，进行一个假测试(如果你的函数依赖于 params，那么就让它更真实)。

你也可以创建一个名为 invoke_lambda.sh
的文件

```
aws lambda invoke --region=us-east-1 --function-name=$1 $1.txt 
```

这需要 1 个变量(如$1 所示)。函数名。它将函数的结果输出到名为 function_name.txt 的文件中。

然后，您可以打开它，或者从您的终端通过 cat 阅读。

这使得在不打开 Amazons impossible UI 的情况下再次弄清楚发生了什么变得非常容易。

我在抓取基础设施时遇到的一个问题是 lambda 超时。它最初设置为 3 秒超时。我变成了 30 岁，又一次变成了金色。

你也可能会耗尽内存。这是人们经常遇到的问题。我没有同样的问题，但如果你有，在 UI 中有一个滑块或 Terraform 中的一个字段来更新。

下一个逻辑步骤是从 lambda 函数创建一个自定义域。所以你可以这样做:

```
https://api.kameronkales.com/stripe 
```

对条带集成 lambda 函数执行 get 请求。

如果有人想知道的话，我可以稍后更新。这很简单:

*   从 AWS 证书管理器请求证书
*   进行验证(DNS 或电子邮件)
*   设置一个 CName，以便在您的 cloudfront 分发 url 上可以找到您想要的路线
*   在 api 网关的自定义域门户中将/route 映射到该域。