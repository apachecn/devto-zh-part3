# 盖茨比和 WordPress:设置

> 原文：<https://dev.to/booyaa/gatsy-and-wordpress-setup-1298>

*Billy Huynh 在[Unsplash](https://unsplash.com/photos/saS98jKhVjA)T3 拍摄的照片*

这是一篇关于创建 Gatsby 网站的博文系列的开始，内容来自 WordPress 网站。

## 设置

*   创建一个名为“words by”`gatsby new wordsby`的新 Gatsby 站点，这将使用 starter 站点创建一个新站点。
*   输入您的新站点:`cd wordsby`
*   设置一个新的 GitHub repo，并将您的更改推送到您的 repo

## 检查点:在本地启动我们的站点

*   让我们启动我们的新网站`gatsby develop`
*   在 [http://localhost:8000](http://localhost:8000) 上打开您的新网站
*   在你的编辑器中打开`gatsby-config.js`,将`siteMetadata.title`更改为不同的内容，注意到在你保存更改后站点发生了变化吗？

如果您遇到了问题，可以查看下面的 Git hash: `168483273e38b94dd35dd5063a44c4696c69ea11`

安装 WordPress 和 Gatsby 插件

*   让我们来设置 WordPress，然后注册一个免费的 WordPress 站点。使用免费的 non-WordPress.com 网站是没问题的。
*   设置一个新的开发者应用程序，让我们可以连接到博客:[Developer.WordPress.com](https://developer.wordpress.com/apps/)
    *   记下你的
    *   WordPress URL
    *   凭证(您用来创建博客的电子邮件地址和密码)
    *   客户端 Id
    *   客户机密
*   安装 WordPress 插件`npm install --save gatsby-source-wordpress`
*   让我们编辑我们的`gatsby-config.js`

将以下条目添加到您的`plugins`部分

```
 {
      resolve: `gatsby-source-wordpress`,
      options: {
        baseUrl: process.env.WORDPRESS_BASE_URL,
        protocol: process.env.WORDPRESS_PROTOCOL,
        hostingWPCOM: (process.env.WORDPRESS_HOSTING_WPCOM === 'true'),
        useACF: (process.env.WORDPRESS_USE_ACF === 'true'),
        verboseOutput: (process.env.WORDPRESS_VERBOSE_OUTPUT === 'true'),
        auth: {
          wpcom_app_clientSecret: process.env.WORDPRESS_CLIENT_SECRET,
          wpcom_app_clientId: process.env.WORDPRESS_CLIENT_ID,
          wpcom_user: process.env.WORDPRESS_USER,
          wpcom_pass: process.env.WORDPRESS_PASSWORD,
        },
        includedRoutes: [
          "**/posts",
          "**/pages",
          "**/tags",
        ],
      },
    }, 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到我们利用环境变量来存储 WordPress 信息。这是一个很好的实践，可以防止机密的意外泄漏(将它们存储在一个配置文件中并提交给源代码控制)。

让我们通过将凭证添加到一个`.env`文件
来测试我们的凭证

```
WORDPRESS_BASE_URL=xxx.wordpress.com
WORDPRESS_PROTOCOL=https
WORDPRESS_HOSTING_WPCOM=true WORDPRESS_USE_ACF=false WORDPRESS_VERBOSE_OUTPUT=true WORDPRESS_CLIENT_SECRET=xxx
WORDPRESS_CLIENT_ID=xxx
WORDPRESS_USER=xxx@example.com
WORDPRESS_PASSWORD=xxx 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记在你的`.gitignore`中包含`.env`(starter Gatsby 站点默认添加了它，但是检查它是否被 Git 跟踪是一个好习惯)。

虽然`dotenv`模块是作为 Gatsby 的一部分安装的，但默认情况下它是不启用的，所以找到您的`module.exports`行，在它上面添加以下代码:

```
require('dotenv').config(); 
```

Enter fullscreen mode Exit fullscreen mode

## 检查点:验证 WordPress 凭证

让我们使用`gatsby develop`重启开发环境

这次让我们去图形浏览器`http://localhost:8000/___graphql`。将下面的`graphql`查询粘贴到左侧面板，并点击播放按钮:

```
{  allWordpressPost  {  edges  {  node  {  title  excerpt  tags  {  name  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果插件已经成功加载，并且能够连接到你的 WordPress 博客，你应该能够在右边的面板上看到数据。

### 故障排除

如果你复制了默认的 WordPress 进行详细输出，那么插件应该处于调试模式。查找以下警告:

`warning The gatsby-source-wordpress plugin has generated no Gatsby nodes. Do you need it?`

这表明在解析您在`.env`中给出的某个值时出错。

如果您看到以下内容:

```
Path: /oauth2/token
The server response was "400 Bad Request" 
```

Enter fullscreen mode Exit fullscreen mode

那么用户名或密码可能是错误的。通过登录 WordPress.com 来测试它们。

如果您看到以下内容:

```
=== [ Fetching wordpress__ ] ===
=== [ Fetching wordpress__ ] ===
=== [ Fetching wordpress__ ] === 
```

Enter fullscreen mode Exit fullscreen mode

我目前正在调查此事，我相信这可能与您的电子邮件地址有关，其中包含`+`例如`joe+wordpress@example.com`

如果你卡住了，你可以看看下面的 Git hash: [`3ee8aae5e0c8d6f45127963f776fd1c9358dd647`](https://github.com/booyaa/wordsby/commit/3ee8aae5e0c8d6f45127963f776fd1c9358dd647)

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*