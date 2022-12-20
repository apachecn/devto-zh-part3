# 用 Deno 写 AWS Lambda 函数

> 原文：<https://dev.to/kt3k/write-aws-lambda-function-in-deno-4b20>

**更新(2019-12-01)**[@ hayd](https://dev.to/hayd)Andy Hayden 创造了 [deno-lambda](https://github.com/hayd/deno-lambda) 这是一个非常好的用 deno 编写 lambda 函数的起点。知识库维护得很好。我强烈建议您也试试这个工具。

* * *

本指南描述了在编写时如何在 [deno](https://deno.land/) 中编写 AWS Lambda 函数(deno v0.4.0)。许多事情可以在以后改进，将来你可以跳过这些步骤。

AWS Lambda 支持[自定义运行时](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-custom.html)。你可以用任何语言编写自己的运行时，并在 AWS Lambda 中使用。在本指南中，我将向您展示如何在 deno 中编写自定义运行时并将其部署到 AWS。

# 先决条件

本指南描述了在 deno 中创建 lambda 函数的两种方法(第 1 部分和第 2 部分)。在这两种情况下，您都需要以下内容:

*   可以创建 lambda 函数的 AWS IAM 用户
*   lambda 函数的 AWS 角色(您需要一个具有“AWSLambdaBasicExecutionRole”策略的角色)
    *   在本文中，我猜它的名字是`arn:aws:iam::123456789012:role/lambda-role`。请把它换成你这边自己的。
*   AWS CLI 已安装

更多细节参见 AWS Lambda 定制运行时教程的先决条件部分。

# 第一部分。凡事都要靠自己

## 构建自定义 Deno

由于 [glibc 兼容性问题](https://github.com/denoland/deno/issues/1658)，目前官方的 deno 二进制文件不能在 Lambda 的操作系统上运行。你需要为它建立你自己的 deno。你需要做的是在[这个图像](https://console.aws.amazon.com/ec2/v2/home#Images:visibility=public-images;search=amzn-ami-hvm-2017.03.1.20170812-x86_64-gp2)中构建 deno，这正是 Lambda 使用的图像。(另外，需要在`.gn`文件上设置`use_sysroot = false`标志。我不懂这面旗，但不管怎样，它是有用的。如果你对细节感兴趣的话，请看上一期的评论。)

如果你想避免自己构建 deno，请从[这里](https://github.com/kt3k/lambda-deno-runtime-wip/blob/master/deno)下载二进制文件，这是我根据 deno 的最新版本用上述设置构建的。我确认这在 Lambda 环境中是可行的。

## 编写自定义运行时

你需要用 deno 写一个自定义的运行时。自定义运行时是负责设置 Lambda 处理程序、从 Lambda 运行时 API 获取事件、调用处理程序、将响应发送回 Lambda 运行时 API 等的程序。自定义运行时的入口点必须命名为`bootstrap`。这样的程序的例子如下(这是由 Bash 脚本包装的 Deno 程序。)

```
#!/bin/sh
set -euo pipefail

SCRIPT_DIR=$(cd $(dirname $0); pwd)
HANDLER_NAME=$(echo "$_HANDLER" | cut -d. -f2)
HANDLER_FILE=$(echo "$_HANDLER" | cut -d. -f1)

echo "
import { $HANDLER_NAME } from '$LAMBDA_TASK_ROOT/$HANDLER_FILE.ts';
const API_ROOT =
  'http://${AWS_LAMBDA_RUNTIME_API}/2018-06-01/runtime/invocation/';
(async () => {
  while (true) {
    const next = await fetch(API_ROOT + 'next');
    const reqId = next.headers.get('Lambda-Runtime-Aws-Request-Id');
    const res = await $HANDLER_NAME(await next.json());
    await (await fetch(
      API_ROOT + reqId + '/response',
      {
        method: 'POST',
        body: JSON.stringify(res)
      }
    )).blob();
  }
})();
" > /tmp/runtime.ts
DENO_DIR=/tmp/deno_dir $SCRIPT_DIR/deno run --allow-net --allow-read /tmp/runtime.ts 
```

```
import { $HANDLER_NAME } from '$LAMBDA_TASK_ROOT/$HANDLER_FILE.ts'; 
```

在这一行中，您从任务目录中导入 lambda 函数。`$LAMBDA_TASK_ROOT`由λ环境给出。`$HANDLER_NAME`和`$HANDLER_FILE`是您的 lambda 的`handler`属性的第一部分和第二部分，您将通过 AWS CLI 对其进行设置。例如，如果您设置了处理程序属性`function.handler`，那么上面的行就变成了`import { handler } from '$LAMBDA_TASK_ROOT/function.ts'`。所以你的 lambda 函数需要被命名为`function.ts`，在这种情况下，它需要导出`handler`作为处理程序。

```
(async () => {
  while (true) {
    ...
  }
})(); 
```

这个块创建了 Lambda 事件处理的循环。在循环的每次迭代中只处理一个事件。

```
 const next = await fetch(API_ROOT + 'next');
    const reqId = next.headers.get('Lambda-Runtime-Aws-Request-Id'); 
```

这两行代码从 Lambda 运行时 API 获取事件并存储请求 id。

```
 const res = await $HANDLER_NAME(await next.json()); 
```

这一行用给定的事件负载调用 lambda 处理程序，并存储结果。

```
 await (await fetch(
      API_ROOT + reqId + '/response',
      {
        method: 'POST',
        body: JSON.stringify(res)
      }
    )).blob(); 
```

这一行将结果发送回 Lambda 运行时 API。

```
DENO_DIR=/tmp/deno_dir $SCRIPT_DIR/deno run --allow-net --allow-read /tmp/runtime.ts 
```

这一行用`net`和`read`权限启动运行时脚本。如果你想要更多的权限，你可以在这里添加你想要的选项。`DENO_DIR=/tmp/deno_dir`很重要的部分。因为 Lambda 环境除了`/tmp`不允许写文件系统，所以需要在`/tmp`下的某个地方设置`DENO_DIR`。

## 编写 Lambda 函数

现在你需要用 deno 写你的 lambda 函数。示例如下:

```
export async function handler(event) {
  return {
    statusCode: 200,
    body: JSON.stringify({
      version: Deno.version,
      build: Deno.build
    })
  };
} 
```

这个 lambda 函数返回一个简单的对象，它包含状态代码 200 和 deno 的版本信息作为主体。

## 部署

现在你有 3 个文件`deno`、`bootstrap` (bash 脚本)和`function.ts` (deno 脚本)。这些都是运行 Lambda 函数所需的文件。你需要压缩它们:

```
$  zip function.zip deno bootstrap function.ts 
```

然后你可以像下面这样部署它:

```
$  aws lambda create-function --function-name deno-func --zip-file fileb://function.zip --handler function.handler --runtime provided --role arn:aws:iam::123456789012:role/lambda-role 
```

(注:将`arn:aws:iam::123456789012:role/lambda-role`替换为自己角色的 arn。)

选项意味着这个 lambda 使用一个定制的运行时。

## 测试

您可以像下面这样调用上面的 lambda:

```
$  aws lambda invoke --function-name deno-func response.json
{
    "StatusCode": 200,
 "ExecutedVersion": "$LATEST" } $  cat response.json
{"statusCode":200,"body":"{\"version\":{\"deno\":\"0.4.0\",...}}} 
```

# 第二部分。使用共享层

AWS 支持 Lambda 层。lambda 层是包含库、自定义运行时或其他依赖项的 ZIP 存档。我发布了上面的`deno`二进制和`bootstrap`脚本作为公共层。您可以将其作为自定义 deno 运行时重用。

在这种情况下，你需要做的只是在 deno 中编写一个 lambda 函数，并将其部署到 AWS 中。

## 使用公共 Deno 运行时创建 Deno Lambda 函数

一个例子`function.ts`如下图所示(同上):

```
export async function handler(event) {
  return {
    statusCode: 200,
    body: JSON.stringify({
      version: Deno.version,
      build: Deno.build
    })
  };
} 
```

然后压缩并部署它:

```
$  zip function-only.zip function.ts
$  aws lambda create-function --function-name deno-func-only --layers arn:aws:lambda:ap-northeast-1:439362156346:layer:deno-runtime:13 --zip-file fileb://function-only.zip --handler function.handler --runtime provided --role arn:aws:iam::123456789012:role/lambda-role 
```

(注:将`arn:aws:iam::123456789012:role/lambda-role`替换为自己角色的 arn。)

其中 arn `arn:aws:lambda:ap-northeast-1:439362156346:layer:deno-runtime:13`是实现 deno 运行时的公共 lambda 层。`--layers arn:aws:lambda:ap-northeast-1:439362156346:layer:deno-runtime:13`选项指定这个 lambda 函数使用它作为共享层。

## 测试一下

您应该能够像下面这样调用上面的 lambda 函数:

```
$  aws lambda invoke --function-name deno-func-only response.json
{
    "StatusCode": 200,
 "ExecutedVersion": "$LATEST" } $  cat response.json
{"statusCode":200,"body":"{\"version\":{\"deno\":\"0.4.0\",...}}} 
```

就是这样。感谢您的阅读。

# 参考文献

所有的例子都可以在[这个库](https://github.com/kt3k/lambda-deno-runtime-wip)中找到。