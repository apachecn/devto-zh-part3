# Lambda 上的铁锈

> 原文：<https://dev.to/jeikabu/rust-on-lambda-4ae8>

[我们对](//./aws-lambda-rust-runtime-announced-39lc)[亚马逊宣布 AWS Lambda](https://aws.amazon.com/blogs/opensource/rust-runtime-for-aws-lambda/)Rust Runtime 感到兴奋。我们终于开始着手调查此事了。

## 铁锈 2018

自 11 月底宣布以来，Rust 1.31 和 [Rust 2018](https://doc.rust-lang.org/nightly/edition-guide/rust-2018/index.html) 以及`lambda_runtime = 0.2`已经发布。

对模块系统的更改意味着可以简化前面的示例。一、原代码:

```
#[macro_use]
extern crate lambda_runtime as lambda;
use lambda::error::HandlerError;
#[macro_use]
extern crate serde_derive; 
```

Enter fullscreen mode Exit fullscreen mode

现在，在`Cargo.toml` :
中重命名`lambda_runtime`板条箱

```
lambda = {version = "0.2", package = "lambda_runtime"} 
```

Enter fullscreen mode Exit fullscreen mode

然后在`rs` :

```
use lambda::error::HandlerError;
use serde::{Serialize, Deserialize}; 
```

Enter fullscreen mode Exit fullscreen mode

查看 github 获取[更新示例](https://github.com/awslabs/aws-lambda-rust-runtime/blob/master/lambda-runtime/examples/basic.rs)的完整版本。

## 设置

在 Linux 上，你可以:

```
rustup target add x86_64-unknown-linux-musl # First time only
cargo build --release --target x86_64-unknown-linux-musl 
```

Enter fullscreen mode Exit fullscreen mode

在 OSX，它将失败:

```
Internal error occurred: Failed to find tool. Is `musl-gcc` installed? 
```

Enter fullscreen mode Exit fullscreen mode

在 macOS 上使用 musl 涉及更多(一如既往)。在最初的 AWS 博客以及[这篇文章](https://chr4.org/blog/2017/03/15/cross-compile-and-link-a-static-binary-on-macos-for-linux-with-cargo-and-rust/)中都有涉及。要点是用[这个 brew 脚本](https://github.com/FiloSottile/homebrew-musl-cross)安装 [musl-cross-make](https://github.com/richfelker/musl-cross-make) :

```
brew install FiloSottile/musl-cross/musl-cross 
```

Enter fullscreen mode Exit fullscreen mode

这并没有创建`musl-gcc`，他在 AWS 博客中创建了一个软链接:

```
ln -s /usr/local/bin/x86_64-linux-musl-gcc /usr/local/bin/musl-gcc 
```

Enter fullscreen mode Exit fullscreen mode

musl-gcc 是由 [musl 安装目标](http://git.musl-libc.org/cgit/musl/tree/Makefile?h=v1.1.21&id=1691b23955590d1eb66a11158fdd91c86337e886#n70)创建的脚本。他联系的的[问题暗示了解决方案，在](https://github.com/alexcrichton/cc-rs/issues/82)[这一期](https://github.com/zonyitoo/context-rs/issues/31) :
中更为明确

```
CROSS_COMPILE=x86_64-linux-musl- cargo build --release --target x86_64-unknown-linux-musl 
```

Enter fullscreen mode Exit fullscreen mode

你还需要创建一个包含
的`.cargo/config`文件(在 Rust 项目或`~/`的根目录下)

```
[target.x86_64-unknown-linux-musl]
linker = "x86_64-linux-musl-gcc" 
```

Enter fullscreen mode Exit fullscreen mode

否则货物将失败:

```
error: linking with `cc` failed: exit code: 1
  |
  = note: "cc" "-Wl,--as-needed" "-Wl,-z,noexecstack" "-Wl,--eh-frame-hdr" "-m64" "-nostdlib"
<SNIP>
  = note: clang: warning: argument unused during compilation: '-nopie' [-Wunused-command-line-argument]
          ld: unknown option: --as-needed
          clang: error: linker command failed with exit code 1 (use -v to see invocation) 
```

Enter fullscreen mode Exit fullscreen mode

## AWS

### λ

你基本上可以按照最初的例子，我们将在这里澄清一些事情。

1.  对于**运行时**，确保选择“在功能代码或层中使用自定义运行时”。

2.  在**功能代码**面板中，从“代码输入类型”下拉菜单中选择“上传一个. zip 文件”。点击页面右上角橙色的“保存”按钮:

    [![](img/731fb4b673449f516044f9c5df20813a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dLTNWUMY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/aws_lambda_upload.png)

3.  点击旁边的**测试**按钮，为“事件名称”添加内容并添加:

    ```
    {
      "firstName": "Rustacean"
    } 
    ```

4.  点击**创建**按钮。回到主屏幕，再次按下**测试**。

5.  `CustomOutput{ message: ... }`在顶部不在“日志输出”中:

    [![](img/583455037809de7eeb01c0d768bb3787.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j2fYf8W2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/aws_lambda_result.png)

还有一些其他有趣的面板:

*   **环境变量**:对`RUST_BACKTRACE`来说可能是完美的，或者可能是 env_logger
*   **基础设置**:配置内存余量和超时

从[AWSλ限值](https://docs.aws.amazon.com/lambda/latest/dg/limits.html):

| 种类 | 限制 |
| --- | --- |
| 记忆 | 128 MB 至 3008 MB |
| 功能超时 | 90 秒(15 分钟) |
| 包装尺寸 | 50MB 压缩，250MB 解压缩 |

从我们的日志输出中我们看到:

```
Memory Size: 128 MB Max Memory Used: 40 MB 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看看我们的包装:

```
-rw-r--r-- 1 jake staff 1922463 Feb 15 12:50 rust.zip
-rwxr-xr-x 2 jake staff 5908520 Feb 15 12:47 target/x86_64-unknown-linux-musl/release/bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

2 MB 压缩，6 MB 未压缩；我们使用了不到一半的最小内存和不到 5%的包大小。Rust 就是为这玩意而生的！

### 区域选择

如果您碰巧在 AWS 区域之间操作*，您可能会比其他情况下更多地考虑区域选择。我们曾经做过非正式的性能测试，但是最近遇到了[这个“速度测试”](https://cloudharmony.com/speedtest-for-aws):*

[![](img/9b2ef9d621dcac4ac906c3e4e689bc65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aVIXpZw0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/speedtest_aws_shanghai_china_telecom.png)

您可以对列进行排序，并对盒状图和生成的统计数据进行分析。

如果你在东亚，你可能已经猜到欧洲和北美东海岸不值得考虑。但是在`ap-northeast-1`(东京)`ap-northeast-2`(首尔)，和`ap-southeast-1`(新加坡)之间呢？不太明显。

所有这些都回避了一个问题，“为什么是 AWS，为什么不是一个区域提供商？”碰巧的是，我们有一个[阿里云/阿里云](https://www.alibabacloud.com/)账户，它的产品“奇怪地”类似于 AWS，包括一个类似 Lambda 的等价物。问题是，它缺少运行时选项:

[![](img/5e5e0a3f69196cb3e4563d6f8b2e27be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C-HkdfG1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/aliyun_lambda_runtimes.png)

### λ@ Edge

“Lambda@Edge”是 Lambda 和 CloudFront(无聊的老 CDN)的组合的时髦的、市场营销的名字。

它有比普通 Lambda 更严格的限制。限制有效负载的大小和请求的速率。

1.  确保你在“N. Virginia”地区:![](img/81b106bb485071e2d63d632ce5a074cc.png)
2.  **创建功能**
3.  在**权限**下，为“执行角色”选择“从 AWS 策略模板创建新角色”，并从“策略模板”中选择**基本 Lambda@Edge 权限**。再次**创建功能**
4.  从**配置**选项卡，在“设计器”中选择**云锋** : ![](img/1e5ed356b1b7ce3ea648dee29d3a13d2.png)
5.  在“配置触发器”面板中，单击**部署到 Lambda@Edge** 按钮
6.  对于“云锋事件”,您从触发您的功能的四个事件中选择一个[(注意，根据事件的不同，限制也不同)](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-cloudfront-trigger-events.html)
7.  填好剩余的东西，然后**展开** …

失败:

```
Correct the errors below and try again.
Runtime must be one of: Node.js 6.10, Node.js 8.10.
Your function's execution role must be assumable by the edgelambda.amazonaws.com service principal. 
```

Enter fullscreen mode Exit fullscreen mode

果然，在[需求和限制](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-requirements-limits.html#lambda-requirements-lambda-function-configuration)中你得用“nodejs”。

言语无法表达我的悲伤。

似乎有人对使用 nodejs 以外的运行时感兴趣。这里有一个关于 aws-lambda-rust-runtime 的[前体问题和一个关于](https://github.com/srijs/rust-aws-lambda/issues/28) [golang](https://github.com/aws/aws-lambda-go/issues/52) 的问题。T4 继续推进 aws-lambda-rust-runtime 项目，但很快遭到拒绝。好吧，好吧……"信口开河，沉船"之类的。

我们会回来的…