# 在 15 分钟内建立你自己的网址缩写

> 原文：<https://dev.to/commonshost/build-your-own-url-shortener-in-15-minutes-279n>

[Commons Host](https://commons.host) CDN 平台最近引入了对定制 HTTP 响应头和重定向规则的支持。让我们使用这些特性来构建我们自己的私有 URL shortener，支持自定义域名和谷歌分析跟踪。我有没有提到这是完全免费和 100%开源的？

让我们开始吧。

您可以使用现有的 Commons 主机站点，但是本教程展示了如何从头开始。这些说明适用于 Mac OS 或 Linux。

## 项目目录

首先创建以下项目目录结构，然后用 NPM 在本地安装 Commons Host CLI 和`short`工具。

```
short/                 // Project directory
| CNAME                // File
| commonshost.json     // File
| package.json         // File
\ public/              // Directory
  \ redirect/          // Directory
    \ index.html       // File 
```

Enter fullscreen mode Exit fullscreen mode

项目目录是独立的，不会对您的系统进行任何全局更改。在您喜欢的任何地方创建它，例如在您的主目录(`~`)中。

从终端运行这些命令来创建项目目录。

```
$ mkdir -p short/public/redirect
$ cd short
$ touch CNAME commonshost.json public/redirect/index.html
$ npm init -y
$ npm install -D @commonshost/cli @commonshost/short 
```

Enter fullscreen mode Exit fullscreen mode

## 域名和 DNS

设置您的域名。

使用免费的公共主机子域，或您自己注册的自定义域名。

### 选项 A)自由`*.commons.host`子域

用你的自由共享主机子域编辑`CNAME`文件。

```
$ echo "your-sub-domain.commons.host" >| CNAME 
```

Enter fullscreen mode Exit fullscreen mode

用你喜欢的任何子域替换`your-sub-domain.commons.host`。本教程使用`short.commons.host`，所以为你自己的 URL 缩短服务选择一个唯一的名称。

公共主机子域不需要额外的 DNS 配置。所有`*.commons.host`子域名的 DNS 设置已经有一个通配符`CNAME`记录指向`commons.host`。

### 选项 B)自定义域名

用您注册的自定义域名编辑`CNAME`文件。

```
$ echo "your-name.example" >| CNAME 
```

Enter fullscreen mode Exit fullscreen mode

用你实际注册的自定义域名替换`your-name.example`。

您还必须在您的 DNS 提供商的仪表板上创建一个从`your-name.example`指向`commons.host`的`CNAME`记录。那个`CNAME`记录将把用户引导到他们最近的公共主机边缘服务器。

注意:对于 Cloudflare DNS，当使用*灵活 SSL* (默认设置)时，您可能会遇到重定向循环。Commons Host 强制执行完整的 TLS，从不提供未加密的内容。要解决这个问题，您可以:

*   为您的 CNAME 记录禁用 Cloudflare CDN，方法是设置: **DNS > DNS 记录>状态>仅 DNS**
*   或者，保持启用 Cloudflare CDN，但配置设置:**加密> SSL >完全 SSL**

## 配置文件

将这个 JSON 样板文件另存为:`commonshost.json`

这包含必要的自定义标题规则和 URL 重定向的占位符。

```
{  "hosts":  [  {  "headers":  [  {  "uri":  "/redirect/{?url,}",  "fields":  {  "Refresh":  "2; {url}"  }  }  ],  "redirects":  []  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## 重定向页面

将此 HTML 样板文件另存为:`public/redirect/index.html`

要设置 Google Analytics，请将下面代码中的`GA_TRACKING_ID`替换为您的 Google Analytics 跟踪 ID(例如`UA-12345678-1`)。详见[谷歌分析文档](https://support.google.com/analytics/answer/1008080?hl=en)。

随意定制或删除任何公共主机品牌。你可以完全控制你的网站。

```
<!DOCTYPE html>
<html>
  <head>
    <script async src="https://www.googletagmanager.com/gtag/js?id=GA_TRACKING_ID"></script>
    <script>
      window.dataLayer = window.dataLayer || []
      function gtag(){dataLayer.push(arguments)}
      gtag('js', new Date())
      gtag('config', 'GA_TRACKING_ID')
    </script>
    Redirecting
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
      body { text-align: center; font-family: sans-serif; }
      a { color: black; }
    </style>
  </head>
  <body>
    <main>
      <h1>Redirecting</h1>
      <p id="location"></p>
    </main>
    <footer>
      Powered by 🐑 <a href="https://commons.host" rel="noopener">Commons Host</a>.
    </footer>
    <script>
      if ('URLSearchParams' in window) {
        const params = new URLSearchParams(location.search)
        if (params.has('url')) {
          const to = document.createElement('a')
          const url = params.get('url')
          to.href = url
          to.textContent = url
          document.querySelector('#location').appendChild(to)
        }
      }
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

## 注册共享主机

通过 CLI 工具创建一个 Commons 主机帐户。这将在`~/.commonshost`中保存一个令牌，让您在这台机器上保持身份验证。

```
$ npx commonshost signup 
```

Enter fullscreen mode Exit fullscreen mode

输入电子邮件地址、用户名和密码来创建您的帐户。

```
? Email address: sebdeckers83@gmail.com
? Username: seb
? Password: [hidden]
  ✔ Registering account
  ✔ Creating new authentication token
  ✔ Saving credentials 
```

Enter fullscreen mode Exit fullscreen mode

## 缩短网址

`short`命令创建一个新的重定向规则，并打印出结果的短 URL。

```
$ npx short https://en.wikipedia.org/wiki/Longest_words
🔗 https://short.commons.host/1302 
```

Enter fullscreen mode Exit fullscreen mode

您的`commonshost.json`配置文件的`redirects`部分现在应该包含如下内容:

```
"redirects":  [  {  "from":  "/1302",  "to":  "/redirect/?url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FLongest_words"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

提示:运行`npx short --help`查看更多高级选项。例如，`--emoji`选项将为缩短的网址生成随机表情符号。🤨

## 部署到公共主机

```
$ npx commonshost deploy
To cancel, press Ctrl+C.

Detected options file: commonshost.json
To override, use: --options "path"

Deploying:

  Directory:    ~/short
  File count:   1
  Total size:   1.84 kB
  URL:      https://short.commons.host
  Options:  ~/short/commonshost.json

  ✔ Uploading 
```

Enter fullscreen mode Exit fullscreen mode

完了！享受由 Commons Host 提供的个人 URL shortener。

## 总结

要缩短另一个 URL，只需在项目目录中重复最后两个命令:

1.  `npx short https://some.really.long.url.example/foo/bar.html`
2.  `npx commonshost deploy --confirm`

查看[服务器文档](https://help.commons.host/server/configuration/host/),了解如何进一步定制你的 Commons 主机站点，比如设置一个`404`回退 HTML 页面。

感谢 [@donavon](https://twitter.com/donavon) 对本教程的反馈。