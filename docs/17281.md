# 使用 CodeBuild 和 Lambda@Edge 为您的静态站点进行 PR 预览

> 原文：<https://dev.to/ascorbic/pr-previews-for-your-static-site-with-codebuild-and-lambdaedge-2n60>

静态站点生成器非常棒(尤其是 [Gatsby](https://www.gatsbyjs.org) )，它们在持续部署中工作得非常好。推送至 git repo，运行 CI 测试，构建网站，然后上线。不喜欢什么？更好的是，您可以自动构建所有拉取请求的预览。Netlify 这样做，真的很酷。[我的博客](https:///mk.gg)由 Netlify 主持，我是它的忠实粉丝。然而，目前我在一个大型的 Gatsby 网站上工作，有很多图像需要处理，不幸的是，我碰到了 Netlify 的 15 分钟构建限制。这促使我再次关注 [CodeBuild](https://aws.amazon.com/codebuild/) ，这是 AWS 构建服务。它可以很好地完成从 GitHub 到 S3 的静态站点的部署，开箱即用。它不做的是构建部署预览。我觉得这是对λ@ Edge 的完美使用，它允许你在 CloudFront 中定制请求和响应。

我要采取的方法是:

*   设置 CodeBuild 以构建 GitHub pull 请求
*   将这些推送到 S3 对应于拉取请求名称的目录中
*   使用通配符子域将它们部署到 CloudFront
*   使用 Lambda@Edge 模拟虚拟主机，子域与拉请求名称匹配

## 设置代码构建

第一部分很简单。转到 [CodeBuild](https://aws.amazon.com/codebuild/) 并创建一个新项目，以 GitHub 作为源代码。将其设置为在推送时自动构建。一旦你用 GitHub 链接了它，它就会安装一个 webhook 来触发构建。默认情况下，每次推送时都会调用这个函数，这不是我们想要的。如果你进入 GitHub 的存储库设置，在 webhook 部分，除了 pull requests 之外，不要选择任何东西。

您需要为构建环境选择一些设置。我在 Node 8 上使用了 Ubuntu 映像，这是我们构建 Gatsby 所需要的。您可以选择其他图像，或者您甚至可以指定自己的 Docker 容器。确保启用缓存，我们将在下面进行设置。

CodeBuild 使用文件 [`buildspec.yml`](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) 来定义如何构建项目。在这个文件中，您可以定义在构建过程的每个阶段使用的命令。我们的设置非常简单，但是非常灵活。

我在这个项目中使用了 yarn，它没有包含在 CodeBuild 映像中，但是它很容易安装。然后我运行`yarn build`来建立网站。我们这样做的原因是，在我的网站上，这需要很长时间——默认情况下大约 18 分钟。不过，我们已经启用了缓存，所以后续的构建很快。

默认情况下，CodeBuild 可以部署到 S3。然而，这并不像我们需要的那样灵活。首先，无论构建是否失败，它都会运行，这不是我们想要的。它还会在过程结束时运行，在构建缓存上传之后，这需要花费很长时间，所以您可能会停留 10 分钟等待
1 分钟的构建部署。通过在构建阶段自己运行它，您可以在较慢的缓存上传之前快速同步。你还可以完全控制整个过程。

对于部署预览，我们希望将构建上传到与拉请求的名称相匹配的目录中。幸运的是，我们可以在
`$CODEBUILD_WEBHOOK_TRIGGER`环境变量中访问它。如果你查看旧的文档(或者这篇文章的以前版本)，你可能会看到这个是
`$CODEBUILD_SOURCE_VERSION`，但是现在它总是包含提交散列。对于拉取请求，变量将采用“pr/22”的形式。我们不能直接使用那个
作为桶键，因为我们需要它是一个有效的子域。幸运的是，我们可以在 yaml 中使用 shell 命令，因此我们可以使用`tr`将`/`替换为`-` :

```
echo $CODEBUILD_WEBHOOK_TRIGGER | tr / - 
```

Enter fullscreen mode Exit fullscreen mode

我们用来将构建推送到 S3 的命令是`aws sync`。`--delete`标志意味着任何被删除的本地文件也将从桶中删除。

```
# buildspec.yml
version: 0.2

phases:
    install:
        commands:
            - npm i -g yarn
            - yarn
    build:
        commands:
            - yarn build && aws s3 sync --delete public "s3://your-example-bucket/previews/$(echo $CODEBUILD_WEBHOOK_TRIGGER | tr / -)"

cache:
    paths:
        - node_modules/**/*
        - public/**/*
        - /usr/local/lib/node_modules/**/* 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在部署它，它将会构建，但无法推进到桶中。你需要进入 IAM 并设置权限。

## 云锋

您现在可以运行构建并将其部署到 S3。你可以使用网站托管功能直接从 S3 为网站提供服务，但这并不好。如果你想使用网站功能(你这样做，因为你需要指定索引文件)，那么你不能使用 HTTPS。这是 2018 年的一个转折点。你也不能用压缩来回应。基本上，无论何时你从 S3 为一个站点服务，你都应该使用 CloudFront。幸运的是，用 S3 后端设置 CloudFront 非常简单。

要设置 HTTPS，您需要验证您控制该域。如果该区域托管在 Route 53 中，这非常简单:选择 DNS 验证，然后单击验证，会有一个按钮为您将记录添加到 Route 53。

## λ@ Edge

这是一个很棒的新服务，可以让你在 CloudFront edge 服务器上运行 Lambda 函数。当我第一次听说这个的时候，我真的想不出它的用处，但是这是一个完美的例子。我们将使用子域来重写请求，将它们映射到保存我们的构建预览的目录。代码非常简单。我们从请求的主机头中提取子域，然后重写请求 URI。我们还将在目录请求后添加一个斜杠，以避免重定向。

```
"use strict";

/*
 * This extracts the subdomain from the request host, and rewrites the request uri
 * to use it as a folder. e.g https://pr-4.example.com/under_construction.gif is rewritten
 * to https://pr-4.example.com/preview/pr-4/under_construction.gif
 */
exports.handler = (event, context, callback) => {
  const { request } = event.Records[0].cf;
  const { host, accept } = request.headers;

  if (
    accept &&
    accept.length &&
    accept[0].value.includes("/html") &&
    !(request.uri.endsWith("/") || request.uri.endsWith(".html"))
  ) {
    request.uri += "/";
  }

  if (host && host.length) {
    // Destructured assignment extracts the first item in an array
    const [subdomain] = host[0].value.split(".");
    if (subdomain) {
      request.uri = `/public/${subdomain}${request.uri}`;
      return callback(null, request);
    }
  }

  callback("Missing Host header");
}; 
```

Enter fullscreen mode Exit fullscreen mode

用节点创建一个新的 Lambda。JS 8 并粘贴上面的代码。保存它，然后选择操作和发布。CloudFront 要求您使用 Lambda 的发布版本。您所做的任何更改都需要发布，并在 CloudFront 中更新 Lambda。完成后，转到左边的触发器列表，选择 CloudFront。在 configuration 部分，选择您之前创建的 CloudFront 发行版，然后选择“viewer request”。

有四个阶段可以运行 Lambda@Edge 函数。查看器请求，这是请求到达 CloudFront 的时间；Origin Request，就在 CloudFront 向源服务器发出请求之前；源响应，即来自源服务器的响应到达的时间；以及查看者响应，它就在响应被发送给查看者之前。我们需要在查看器请求阶段运行，因为我们需要访问请求主机头。

## 固定缓存头

这些指令是特定于 Gatsby 的，但是您需要为您的静态站点生成器做一些类似的事情。默认情况下，S3 不会向其响应添加任何缓存控制头。这是一件坏事，因为这意味着浏览器不会缓存任何东西，失去了静态站点的一大优势。你可以在上传到 S3 时添加缓存控制属性，但由于我们已经在使用 Lambda@Edge，我们可以更灵活地在这里添加它们。这一次，您需要在源响应阶段添加它们，此时来自 S3 的响应到达没有缓存头的 CloudFront。

对于 Gatsby，建议您不要缓存 HTML 文件，但是因为所有其他资源都使用文件名哈希，所以应该永远缓存它们。我们可以动态添加适当的标题。

我们还重写了所有重定向，删除了之前添加的目录。

用以下内容创建一个新的 Lambda:

```
"use strict";

exports.handler = (event, context, callback) => {
  const { response } = event.Records[0].cf;
  if (response.status === 302) {
    const { location } = response.headers;
    if (location && location.length) {
      // Strip the leading 2 directories from redirects
      response.headers.location[0].value = location.replace(
        /\/([^\/]+)\/([^\/]+)(\/.*)/,
        "$3"
      );
    }
  }
  const type = response.headers["content-type"];
  let cache;
  if (type && type.length && type[0].value === "text/html") {
    cache = "public,max-age=0,must-revalidate";
  } else {
    cache = "public,max-age=31536000,immutable";
  }
  response.headers["cache-control"] = [{ key: "Cache-Control", value: cache }];

  callback(null, response);
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 固定 mime 类型

默认情况下，AWS cli 会尝试猜测上传文件的 mimetype。这很好，因为 S3 在提供内容时不使用文件名规则。不幸的是，它使用的 Python 库读取操作系统的 mime.types 文件，CodeBuild 使用的图像版本严重过时，缺少. json 等模糊类型。幸运的是，这很容易修复:只需用 Apache 的最新版本替换默认的 mime.types 文件。我们可以将它作为命令添加到我们的 buildspec:

```
# buildspec.yml
version: 0.2

phases:
    install:
        commands:
            - curl -s -o mime.types "https://svn.apache.org/viewvc/httpd/httpd/trunk/docs/conf/mime.types?view=co"
            - sudo mv mime.types /etc/
            - npm i -g yarn
            - yarn
    build:
        commands:
            - yarn build && aws s3 sync --delete public "s3://your-example-bucket/previews/$(echo $CODEBUILD_WEBHOOK_TRIGGER | tr / -)"

cache:
    paths:
        - node_modules/**/*
        - public/**/*
        - /usr/local/lib/node_modules/**/* 
```

Enter fullscreen mode Exit fullscreen mode

现在当你同步你的文件，甚至你的. woff2，.webp 和。json 文件将使用正确的 mimetypes，这意味着它们将被正确压缩。

现在，您应该能够打开一个拉请求，或者推送到一个现有的请求，让 CodeBuild 构建并将预览部署到 S3 和 CloudFront。

最初发布到 [mk.gg](https:///mk.gg) 。下一部分将讨论当您合并到 master 时如何部署 live 站点。