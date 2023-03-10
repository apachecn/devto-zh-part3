# 使用 AWS Lambda 和 Rust 调整 s3 中的图像大小

> 原文：<https://dev.to/matsimitsu/resize-images-from-s3-with-aws-lambda-and-rust-36b7>

我的网站的第一次迭代没有调整图像大小，总是显示完整的 2200 像素宽的图像，这在我的本地(桌面)机器上非常好，但当我试图在柬埔寨的一家酒店访问该网站时，该网站需要很长时间才能加载。

在本地调整图像大小很好，但是很费时间，而且在慢速 Wi-Fi 上上传一张五种不同大小的图像要花很长时间，如果它能工作的话。

然后我切换到使用一个图像调整代理，它从磁盘上获取图像，并动态地调整它们的大小，将结果缓存在 Nginx 中。这很好，但是在服务器规格和每月费用之间有一个折衷。低规格意味着在一个未缓存的页面上，需要几分钟的时间来调整所有图像的大小，而高规格意味着对于一个 99%时间都处于空闲状态的服务器来说，每月的成本很高。

不运行服务器的解决方案是切换到 [imgix](https://imgix.com) ，这是一个很好的服务，可以为你调整图像大小，质量和速度都很好，但不管你是否使用这项服务，每月的最低费用是 10，00 美元，随着你添加越来越多的照片，费用会很快增加。这是 imgix 用作其代理来源的 S3 存储成本之外的成本。

这让我想到了最新的解决方案，使用 s3 存储图像(imgix 也需要 S3 作为源，所以图像已经在那里了)和 AWS Lambda 在上传时调整图像的大小。

这意味着我只需要上传一次图片，Lambda 就会处理所有调整过大小的变体。我找到了一些(Javascript)解决方案，并且 [aws-lambda-image](https://github.com/ysugimoto/aws-lambda-image) 看起来最容易使用。这种情况持续了几个月，因为一些原因，我决定推出自己的解决方案。

aws-lambda-image 做了很多神奇的事情，并使用 [Claudia](https://claudiajs.com/) 来管理 lambda 设置。虽然它工作得很好，但我真的不喜欢需要对 AWS API 进行高级访问并自动为您配置大量内容的工具。我不知道运行命令后会发生什么。

对我来说，另一个风险是它运行在 Node 上，最终在某个时候需要升级，这有很高的破坏功能的风险，而且这些事情总是在最不方便的时候发生。

我想要的是一个单独的二进制程序，它可以一直工作，不需要任何维护，由我自己配置，这样我就知道发生了什么，并且理想情况下比 NodeJS 解决方案更高效。这也是一个很好的借口来玩 Rust 和刚刚发布的 [Rust AWS Lambda Runtime](https://github.com/awslabs/aws-lambda-rust-runtime) 。

## 目标

我想要类似于 Javascript 的解决方案。它应该监听文件上传到 S3 时发出的事件，并在几个宽度(`360px`、`720px`、`1200px`和`2200px`)调整图像的大小。

在我们开始之前，你可以跟随 [GitHub](https://github.com/matsimitsu/lambda-image-resize-rust/) 上的完整源代码

### 一个二元项目

让我们从制作一个新的 Rust 项目开始，它应该是一个二进制项目，我们需要对 Cargo TOML 做一些调整，以确保 Lambda 可以运行二进制。

```
[package]
name = "lambda-image-resize-rust"
version = "0.1.0"
authors = ["Robert Beekman <robert@matsimitsu.nl>"]

[dependencies]
lambda_runtime = "0.1"

[[bin]]
name = "bootstrap"
path = "src/main.rs" 
```

AWS Lambda 的工作方式是，它为您启动应用程序/二进制文件，然后您必须从应用程序中调用某个端点来接收要处理的新作业。`lambda_runtime` crate 抽象了这个过程，你所要做的就是实现一个事件处理器，这个事件处理器将被`lambda!`调用。

从示例应用程序中，我们启动了一个记录器，并为 AWS 运行时运行 lambda。

```
fn main() -> Result<(), Box<Error>> {
    simple_logger::init_with_level(log::Level::Info)?;

    lambda!(handle_event);

    Ok(())
} 
```

将使用运行时为我们调用的端点的 JSON 结果来调用`handle_event`函数。让我们通过使用 [AWS-lambda-events crate](https://github.com/LegNeato/aws-lambda-events/blob/master/aws_lambda_events/src/generated/s3.rs) ，用 Serde 将它转换成一个漂亮的结构。

### 处理 S3 事件

该事件包含一个或多个“记录”,代表它收到的 S3 上传。

```
fn handle_event(event: Value, ctx: lambda::Context) -> Result<(), HandlerError> {
    let config = Config::new();

    let s3_event: S3Event =
        serde_json::from_value(event).map_err(|e| ctx.new_error(e.to_string().as_str()))?;

    for record in s3_event.records {
        handle_record(&config, record);
    }
    Ok(())
} 
```

对于每次上传，我们必须从 S3 获得文件，将文件转换成一个或多个图像变化，并再次上传回 S3。有几个板条箱实现了 S3 API，我选择了 [rust-s3](https://github.com/durch/rust-s3) ，因为它看起来简单小巧。

AWS Lambda 设置了几个[默认环境变量](https://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html)，其中设置了`AWS_ACCESS_KEY_ID`和`AWS_SECRET_ACCESS_KEY`，`rust-s3`机箱可以检测到具有`Credentials::default()`功能的环境变量。

在我的例子中，我希望将图像存储在与源相同的桶中，只是存储在不同的位置，这样我就可以使用来自 S3 事件的信息来确定区域和桶。

```
fn handle_record(config: &Config, record: S3EventRecord) {
    let credentials = Credentials::default();
    let region: Region = record
        .aws_region
        .expect("Could not get region from record")
        .parse()
        .expect("Could not parse region from record");
    let bucket = Bucket::new(
        &record
            .s3
            .bucket
            .name
            .expect("Could not get bucket name from record"),
        region,
        credentials,
    );
    let source = record
        .s3
        .object
        .key
        .expect("Could not get key from object record");
} 
```

### 递归

既然我们已经获得了在 S3 上获取和存储映像所需的所有配置，我们必须首先进行健全性检查。我们监听上传图像的 S3 事件，但是这个函数也上传图像到 S3，这意味着如果你在配置中犯了一个错误，它可以为你放回桶中的每个文件发送一个 S3 事件。

这意味着您将再次处理您自己的(调整大小的)图像，因为我们为每个上传的图像生成了多个变体。结合 Lambda 的强大功能及其并发性，这意味着您将快速生成数以千计的新 Lambda 任务，迫使您在积累巨额 AWS 账单之前点击 Lambda UI 中可怕的“紧急按钮”。(这个*可能*也可能不是来自我自己的经历；)).

调整图像大小后，我们将把`-<size>`添加到文件名中(例如`foo.jpg`变成了`foo-360.jpg`)。为了防止这种 Lambda 递归，我们检查上传的文件名，看看它是否已经调整了大小。

```
 /* Make sure we don't process files twice */
    for size in &config.sizes {
        let to_match = format!("-{}.jpg", size);
        if source.ends_with(&to_match) {
            warn!(
                "Source: '{}' ends with: '{}'. Skipping.",
                &source,
                &to_match
            );
            return;
        }
    } 
```

### 从-获取图片并上传到 S3

现在我们已经确定我们有正确的图像，让我们从 S3 获取它，并从内存加载到`image`箱中。

```
 let (data, _) = bucket
        .get(&source)
        .expect(&format!("Could not get object: {}", &source));

    let img = image::load_from_memory(&data)
        .ok()
        .expect("Opening image failed"); 
```

### 调整图片大小

随着图像在内存中，我们可以调整它的大小。根据您为 Lambda 函数分配的内存，您可以获得一个或多个 CPU 内核供您使用。为了最大限度地利用我们预定的执行时间，我选择使用巨大的 [rayon-rs](https://github.com/rayon-rs/rayon) 机箱来并行执行 resizes。并行处理图像你要做的就是把`iter()`换成`.par_iter()`，牛逼！

```
 let _: Vec<_> = config
        .sizes
        .par_iter()
        .map(|size| {
            let buffer = resize_image(&img, &size).expect("Could not resize image");

            let mut target = source.clone();
            for (rep_key, rep_val) in &config.replacements {
                target = target.replace(rep_key, rep_val);
            }
            target = target.replace(".jpg", &format!("-{}.jpg", size));
            let (_, code) = bucket
                .put(&target, &buffer, "image/jpeg")
                .expect(&format!("Could not upload object to :{}", &target));
            info!("Uploaded: {} with: {}", &target, &code);
        })
        .collect(); 
```

我们做的另一件事是循环通过`&config.replacemens`，这是另一个对抗递归问题的特性，通过允许我们替换文件(输入)路径的某些部分。

我们可以用键/值字符串设置一个`REPLACEMENTS` env 变量，比如`"original:resized"`。

输入路径为`/original/trips/asia2018/img_01.jpg`时，这将被转换为`/resized/trips/asia2018/img_01.jpg`。结合 AWS Lambda 配置页面上的输入过滤器，您可以确保转换后的图像不会被处理两次。

最后，我们需要实现上面代码中调用的实际 resize 函数。

它获取图像和新的宽度，计算比率并生成新的所需高度。然后我们调用 image crate 函数并使用`ImageOutputFormat::JPEG(90)`枚举将 JPEG 质量设置为`90`(从默认的`75`)。

```
fn resize_image(img: &image::DynamicImage, new_w: &f32) -> Result<Vec<u8>, ImageError> {
    let mut result: Vec<u8> = Vec::new();

    let old_w = img.width() as f32;
    let old_h = img.height() as f32;
    let ratio = new_w / old_w;
    let new_h = (old_h * ratio).floor();

    let scaled = img.resize(*new_w as u32, new_h as u32, image::FilterType::Lanczos3);
    scaled.write_to(&mut result, ImageOutputFormat::JPEG(90))?;

    Ok(result)
} 
```

你可以在 GitHub 上找到完整的项目。

### 为 AWS Lambda 编译

有了可用的二进制文件，我们现在需要针对正确的环境/发行版(交叉)编译它。幸运的是，一个名叫 [softprops](https://hub.docker.com/u/softprops) 的人创建了一个 [docker 容器](https://hub.docker.com/r/softprops/lambda-rust/)，它拥有我们需要的所有工具来编译这个用于 Lambda 映像的二进制文件。

```
 docker run --rm \
        -v ${PWD}:/code \
        -v ${HOME}/.cargo/registry:/root/.cargo/registry \
        -v ${HOME}/.cargo/git:/root/.cargo/git \
        softprops/lambda-rust 
```

这将在`target/labmda/release`中生成一个`boostrap.zip`文件。你也可以从的[发布页面获得`boostrap.zip`。](https://github.com/matsimitsu/lambda-image-resize-rust/releases)

### 配置λ

有了新编译的二进制文件，我们就快成功了。我们需要做两件事，配置一个 IAM 角色，它允许 Lambda 函数写日志和访问 S3 存储桶，并配置 Lambda 函数本身。

让我们从 IAM 角色开始，我们必须添加两个策略，一个允许该功能登录，另一个允许访问 S3，看起来应该是这样的:

[![image](img/f7f1678916d0114efa8412698dac3b84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V9Zgwvzm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d3khpbv2gxh34v.cloudfront.net/r/blog/image-resize-rust/iam-role-720.jpg)

加分，如果你锁定 S3 的角色，不允许它移除物品。

配置好角色后，我们可以配置 lambda 函数，我们必须设置`SIZES`和`REPLACEMENTS` ENV 变量，我发现该函数在至少分配了`1024MB`内存的情况下工作得最好。

[![image](img/8d8e9d0ff7348524c4e6520bbefb69a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XVfjfw9i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d3khpbv2gxh34v.cloudfront.net/r/blog/image-resize-rust/lambda-config-options-720.jpg)

附上生成的`bootstrap.zip`文件，保存功能。

最后，我们需要配置 S3 事件，从页面上的“添加触发器”部分选择“S3”事件，并选择“所有上传事件”选项。我还设置了前缀/后缀选项来防止我们的递归问题。

[![image](img/436b7008ce32c60648c6f4f65ac1b58e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---YNYLV-F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d3khpbv2gxh34v.cloudfront.net/r/blog/image-resize-rust/lambda-s3-config-720.jpg)

再次保存该功能并上传一张图片进行测试，如果一切顺利，它应该在上传完成后生成调整大小的图片。您可以在 AWS Cloudwatch 上验证它是否工作(或捕捉任何错误)，它应该是这样的:

```
START RequestId: 7e7886d6-f983-4ef7-9916-83ab53874c6c Version: $LATEST
2019-03-09 15:07:35 INFO [lambda_runtime::runtime] Received new event with AWS request id: 7e7886d6-f983-4ef7-9916-83ab53874c6c
2019-03-09 15:07:35 INFO [bootstrap] Fetching: original-rust/blog/image-resize-rust/lambda-config.jpg, config: Config { sizes: [360.0, 720.0, 1200.0, 2200.0], replacements: [("original-rust", "r"), ("original", "r")] }
2019-03-09 15:07:36 INFO [bootstrap] Uploaded: r/blog/image-resize-rust/lambda-config-360.jpg with: 200
2019-03-09 15:07:36 INFO [bootstrap] Uploaded: r/blog/image-resize-rust/lambda-config-1200.jpg with: 200
2019-03-09 15:07:36 INFO [bootstrap] Uploaded: r/blog/image-resize-rust/lambda-config-720.jpg with: 200
2019-03-09 15:07:38 INFO [bootstrap] Uploaded: r/blog/image-resize-rust/lambda-config-2200.jpg with: 200
2019-03-09 15:07:38 INFO [lambda_runtime::runtime] Response for 7e7886d6-f983-4ef7-9916-83ab53874c6c accepted by Runtime API
END RequestId: 7e7886d6-f983-4ef7-9916-83ab53874c6c
REPORT RequestId: 7e7886d6-f983-4ef7-9916-83ab53874c6c  Init Duration: 86.53 ms Duration: 2944.40 ms    Billed Duration: 3100 ms Memory Size: 1024 MB   Max Memory Used: 125 MB 
```

## 未来目标

你可以在 [GitHub](https://github.com/matsimitsu/lambda-image-resize-rust) 上找到源代码，在[release 页面](https://github.com/matsimitsu/lambda-image-resize-rust/releases)上找到现成的`bootstrap.zip`。

这个二进制文件非常好用，已经调整了许多图像的大小。有了亚马逊慷慨的免费 Lambda 层，调整我博客上所有图片的大小总共花了我 0.61 美元(T2)。然而，仍有改进的余地。错误处理可以比任何地方的`.expect()`都好得多，尽管只要它在 CloudWatch 中记录错误，它现在就对我有效。

如果它可以处理更多的图像格式就好了，而`image` crate 可以很好地处理 GIF、JPEG、PNG 和 WEBP 等输入格式，现在我只生成 JPEG 图像。我喜欢它生成网页图片以及 JPEGs 文件，但我找不到任何可以生成网页图片的板条箱。如果你碰巧知道其中一个或者对这篇文章有其他反馈，请通过电子邮件让我知道[，或者](//mailto:hello@matsimitsu.nl)[发微博给我](https://twitter.com/matsimitsu)。

### 参考文献/资源

*   [https://AWS . Amazon . com/blogs/open source/rust-runtime-for-AWS-lambda/](https://aws.amazon.com/blogs/opensource/rust-runtime-for-aws-lambda/)
*   [https://github.com/srijs/rust-aws-lambda](https://github.com/srijs/rust-aws-lambda)
*   [https://dev . to/adnanrahic/a-crash-course-on-servers-with-AWS-image-resize-on-the-fly-with-lambda-and-S3-4 foo](https://dev.to/adnanrahic/a-crash-course-on-serverless-with-aws---image-resize-on-the-fly-with-lambda-and-s3-4foo)
*   [https://github . com/aw slabs/AWS-lambda-rust-runtime/issues/29](https://github.com/awslabs/aws-lambda-rust-runtime/issues/29)
*   [https://docs . AWS . Amazon . com/Amazon S3/latest/dev/notification how to . html](https://docs.aws.amazon.com/AmazonS3/latest/dev/NotificationHowTo.html)
*   [https://hub . docker . com/r/soft props/lambda-rust/](https://hub.docker.com/r/softprops/lambda-rust/)