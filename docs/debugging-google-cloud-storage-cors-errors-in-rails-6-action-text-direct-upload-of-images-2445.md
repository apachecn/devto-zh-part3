# 调试 Rails 6 动作文本中的谷歌云存储 CORS 错误(图片直接上传)

> 原文：<https://dev.to/morinoko/debugging-google-cloud-storage-cors-errors-in-rails-6-action-text-direct-upload-of-images-2445>

我最近在一个简单的博客页面上试用了 Rails 6 的新动作文本功能。我喜欢它的一点是，它可以让你拖放多个图像，然后自动将它们放置到一个格式良好的“图库”布局中。

当图像被放入动作文本编辑器时，它通过 JavaScript 将每个图像直接上传到您的存储位置。根据您使用的映像存储，您可能会遇到一些由 CORS(跨源资源共享)错误引起的问题。

就我而言，我在使用谷歌云存储时遇到了这个问题。每次 Rails 试图通过动作文本编辑上传照片，我都会得到一个`No 'Access-Control-Allow-Origin' header is present on the requested resource` CORS 错误。这是因为浏览器不允许共享来自两个不同来源的资源，例如`my-app.com`和`my-app.storage.googleapis.com`，包括在试图将照片上传到存储器时发送`POST`请求。(详见谷歌云的 [CORS 页面](https://cloud.google.com/storage/docs/cross-origin))。

我花了很长时间才想出如何解决这个问题，所以我想在这里分享一下我的做法！

为了解决这个问题，您需要修改 Google 云存储空间的 CORS 配置，这可以使用一个叫做“gsutil”的工具来完成。

gsutil 工具是一个 CLI，允许您从命令行管理 Google 云存储桶。如果你没有安装，按照谷歌的安装说明来安装。您还需要使用`gcloud init`通过命令行登录到 Google Cloud(安装页面上也有解释),否则您将无法配置您的 bucket。

现在你可以开始配置你的 CORS 设置了！

要检查当前的 CORS 配置，使用下面的命令，用您的 bucket 的实际名称替换`my-bucket-name`。

```
gsutil cors get gs://my-bucket-name 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一些 JSON，看起来像这样:

```
[  {  "origin":  ["http://example.appspot.com"],  "responseHeader":  ["Content-Type"],  "method":  ["GET",  "HEAD",  "DELETE"],  "maxAgeSeconds":  3600  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

要更改/更新 CORS 配置，首先需要创建一个包含配置设置的 JSON 文件。你可以随意命名这个文件，但是我选择了`cors.json`。从技术上来说，你也可以把它保存在电脑的任何地方，但我决定把我的放在我的 Rails 应用程序的`config`目录中，这样我就可以很容易地跟踪它，并在需要时更新它。

现在，为了解决当动作文本试图将图像直接上传到您的桶时发生的问题，您需要确保您的应用程序的 URL 在 JSON 的`origin`数组中列出，将`Content-MD5`内容类型添加到`responseHeader`数组，并将`PUT`、`POST`和`OPTIONS`方法添加到`method`数组。

完成的`cors.json`文件应该是这样的:

```
//  cors.json  [  {  "origin":  ["https://my-app.com"],  "responseHeader":  ["Content-Type",  "Content-Md5"],  "method":  ["PUT",  "GET",  "HEAD",  "DELETE",  "OPTIONS"],  "maxAgeSeconds":  3600  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

要使用这些设置配置铲斗，请使用以下命令:

```
gsutil cors set cors.json gs://my-bucket-name 
```

Enter fullscreen mode Exit fullscreen mode

注意，上面命令中的`cors.json`指的是文件的位置/路径。要么将`cd`放入同一个目录，要么写出路径。(例如，我在我的`config/`目录中有我的，所以我使用应用程序根目录中的`gsutil cors set config/cors.json gs://my-bucket-name`。

您可以再次使用`gsutil cors get`命令:
检查 CORS 配置是否已更新

```
gsutil cors get gs://my-bucket-name 
```

Enter fullscreen mode Exit fullscreen mode

请注意，更新后您可能仍然会收到 CORS 错误，因为浏览器可能已经缓存了以前的设置。如果是这样，请尝试完全清除缓存并刷新页面。你可以尝试的另一件事是将`cors.json`中的`"maxAgeSeconds"`临时设置为一个超级短的值，比如`1`，这样浏览器就可以立即刷新响应设置(确保以后再改回来)。

如果您仍然有问题，您可能需要通过使用 Chrome 开发工具实际检查响应头来进行一些故障排除。谷歌的[故障诊断文档](https://cloud.google.com/storage/docs/configuring-cors#troubleshooting)给了你一个很好的概述。

您可以通过打开开发工具并导航到`Network`选项卡来检查响应头。尝试上传另一个图像，然后在网络活动列表中找到上传图像的请求。单击请求`Name`列的名称，并检查`Headers`部分(在找到正确的请求之前，您可能需要检查几个不同的请求)。如果`Request URL`以`https://storage.googleapis.com/.....`开头，你就知道是对的。

在这里，您希望确保请求头中的`Origin`与您的`cors.json`中的一个源完全匹配。还要确保`Access-Control-Request-Method`(例如`content-md5`)和`Access-Control-Request-Method`(例如`PUT`或`OPTIONS`)与 JSON 文件中的内容相匹配。如果没有，相应地更新 JSON 文件。

例如，我在我的`cors.json`文件中的源 URL 中有一个打印错误，必须更新它。我后来还注意到，我必须将`OPTIONS`方法添加到`method`数组中(我只列出了`PUT`)。

上传愉快！