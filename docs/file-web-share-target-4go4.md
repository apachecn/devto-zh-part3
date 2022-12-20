# 文件 Web 共享目标

> 原文：<https://dev.to/chromiumdev/file-web-share-target-4go4>

我经常说，要让网络应用在应用世界中有效竞争，它们需要整合到用户期望应用出现的所有地方。应用程序间的通信是网络平台主要缺失的部分之一，具体来说，最后一个主要缺失的功能是本地级共享:网络应用程序需要能够将[数据从它们的筒仓](https://dev.to/paul_kinlan/web-sites-as-unintended-silos-the-problem-with-getting-data-in-and-out-of-the-web-client-4o9i)中取出，并放入其他网站和应用程序中；他们还需要能够从其他本地应用程序和网站接收数据。

文件共享目标 API 是 ChromeCanary 中现有 API 的游戏规则改变者。该 API 扩展了 [Web Share TargetAPI](https://github.com/WICG/web-share-target/blob/master/docs/explainer.md) ，通过将它们集成到系统共享功能中，让应用程序和网站共享简单的链接和文本。

这个非常静态的文件博客利用了网络共享目标 API，所以我可以从任何 Android 应用程序中快速地[共享我感兴趣的链接](https://dev.to/paul_kinlan/web-share-target-api-2e3g)，并且从上周开始[我启用了文件共享目标 API，所以我可以直接从 Android 上的相机应用程序](https://dev.to/chromiumdev/testing-file-share-target-from-camera-198j-temp-slug-2092201)上传图像到我的博客。这篇文章讲的是我是如何做到的(从杰克·阿奇博尔德那里偷了一些代码——他解决了很多集成到 [squoosh.app](https://squoosh.app/) 中的错误。)

[文件共享 TargetAPI](https://wicg.github.io/web-share-target/level-2/#example-3-manifest-webmanifest) 是一个非常新颖的 API，因为它是完全渐进式的。如果你的应用程序可以处理表单`POST`请求，那么你可以很容易地与这个 API 集成。基本流程是:当用户从原生选取器中选择你的应用程序时，Chrome 会向你的服务器发送一个表单`POST`请求，由你决定如何处理它(在服务人员或服务器上处理)。

要在您的 web 应用程序中添加文件共享支持，您需要做两件事:

1.  声明支持通过清单文件共享文件，
2.  在您的服务人员中处理表单`POST`请求。

清单向主机系统声明共享应该如何从主机应用程序映射到 web 应用程序。在下面的清单中，它实际上是说“当用户共享一个‘image/*’类型的文件时，向'/share/image/'发出一个表单 POST 请求，并将数据命名为‘file’”。

*manifest.json*

```
{
  "name": "Blog: Share Image",
  "short_name": "Blog: Share Image",
  "start_url": "/share/image/",
  "theme_color": "#000000",
  "background_color": "#000000",
  "icons": [ {
      "sizes": "192x192",
      "src":img/me.png",
      "type": "image/png"
  }],
  "share_target": {
    "action": "/share/image/",
    "method": "POST",
    "enctype": "multipart/form-data",
    "params": {
      "files": [
        {
          "name": "file",
          "accept": ["image/*"]
        }
      ]
    }
  },
  "display": "standalone",
  "scope": "/share/"
} 
```

一旦用户分享到你的网络应用程序，Chrome 就会用文件数据作为有效载荷向你的站点发出请求。

建议您在服务人员内部处理 POST 请求，以便 1)快速，2)在网络不可用时具有弹性。您可以这样做:

*serviceworker.js* - [演示](https://paul.kinlan.me/share/image/sw.js)

```
onfetch = async (event) => {
  if (event.request.method !== 'POST') return;
  if (event.request.url.startsWith('https://paul.kinlan.me/share/image/') === false) return;

  /* This is to fix the issue Jake found */
  event.respondWith(Response.redirect('/share/image/'));

  event.waitUntil(async function () {
    const data = await event.request.formData();
    const client = await self.clients.get(event.resultingClientId || event.clientId);
    // Get the data from the named element 'file'
    const file = data.get('file');

    console.log('file', file);
    client.postMessage({ file, action: 'load-image' });
  }());
}; 
```

上面发生了一些有趣的事情，可以很快总结为:

*   通过执行重定向将 UI 呈现为`POST`请求的结果。
*   通过`event.request.formData()`读取通过表格提交的数据
*   将数据发送到打开的窗口(这将是我们在第一点中将用户重定向到的 UI)。

你完全可以决定如何处理发布给你的服务人员的数据，但是在我的应用程序中，我需要直接在用户界面中显示它，所以我必须找到用户正在使用的窗口并在那里显示数据。

*index.html*-[演示](https://paul.kinlan.me/share/image/index.html)

```
navigator.serviceWorker.onmessage = (event) => {
  console.log(event);
  imageBlob = event.data.file;
  // Update the UI with the data that has been shared to it.
  imageShare.src = URL.createObjectURL(imageBlob);
}; 
```

仅此而已。如果您的 web 表单已经有了一个 API 端点，那么这是一个简单而强大的附加功能，您可以添加到您的站点中。

Web Share Target API 是一个非常强大的平台原语，它打破了 Web 应用程序在其主机平台上的另一个障碍。