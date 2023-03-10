# 从图像中提取文本:形状检测实验

> 原文：<https://dev.to/chromiumdev/extracting-text-from-an-image-experiments-with-shape-detection-2kce>

在谷歌 IO 之后，我有一点时间休息，我想挠一挠我长期以来的痒。我只是希望能够在浏览器中复制包含在图像中的文本。仅此而已。我认为这对每个人来说都是一个不错的功能。

将功能直接添加到 Chrome 并不容易，但我知道我可以利用 Android 上的 intent 系统，我现在可以在 Web 上这样做(或至少在 Android 上使用 Chrome)。

web 平台的两个新功能——共享目标级别 2(或者我喜欢称之为文件共享)和形状检测 API 中的`TextDetector`—[允许我构建一个实用程序，我可以共享图像并获取图像中的文本](https://copy-image-text.glitch.me/)。

基本实现相对简单，您在服务工作器中创建一个共享目标和一个处理程序，然后一旦您有了用户共享的映像，您就可以在其上运行`TextDetector`。

`Share Target API`允许您的 web 应用程序成为本地共享子系统的一部分，在这种情况下，您现在可以通过在您的`Web App Manifest`中声明它来注册处理所有的`image/*`类型，如下所示。

```
"share_target": {
  "action": "/index.html",
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
} 
```

当您的 PWA 安装后，您将在所有共享图像的地方看到它，如下所示:

[![](img/a510951e5d3904e6df00cd7a5231d26e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OchLLvSd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paul.kinlan.img/2019-05-13-extracting-text-from-an-imageexperiments-with-shape-detection-0.jpeg)

API 将共享文件视为表单发布。当文件共享到 Web 应用程序时，服务人员被激活，使用文件数据调用`fetch`处理程序。数据现在在服务工作器中，但我需要它在当前窗口中，以便我可以处理它，服务知道哪个窗口调用了请求，所以您可以轻松地将数据发送给客户端。

```
self.addEventListener('fetch', event => {
  if (event.request.method === 'POST') {
    event.respondWith(Response.redirect('/index.html'));
    event.waitUntil(async function () {
      const data = await event.request.formData();
      const client = await self.clients.get(event.resultingClientId || event.clientId);
      const file = data.get('file');
      client.postMessage({ file, action: 'load-image' });
    }());

    return;
  }
  ...
  ...
} 
```

一旦图像出现在用户界面中，我就用文本检测 API 处理它。

```
navigator.serviceWorker.onmessage = (event) => {  
  const file = event.data.file;
  const imgEl = document.getElementById('img');
  const outputEl = document.getElementById('output');
  const objUrl = URL.createObjectURL(file);
  imgEl.src = objUrl;
  imgEl.onload = () => {
    const texts = await textDetector.detect(imgEl);
    texts.forEach(text => {
      const textEl = document.createElement('p');
      textEl.textContent = text.rawValue;
      outputEl.appendChild(textEl);
    });
  };
  ...
}; 
```

最大的问题是浏览器不会自然地旋转图像(如下所示)，形状检测 API 需要文本处于正确的阅读方向。

[![](img/603530643a78b234a3ecbc995132d484.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r0VpxMgd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paul.kinlan.img/2019-05-13-extracting-text-from-an-imageexperiments-with-shape-detection-1.jpeg)

我使用了相当容易使用的 [EXIF-Js 库](a%20href=%22https://github.com/exif-js/exif-js%22>https://github.com/exif-js/exif-js</a)来检测旋转，然后做一些基本的画布操作来重新定位图像。

```
EXIF.getData(imgEl, async function() {
  // http://sylvana.net/jpegcrop/exif_orientation.html
  const orientation = EXIF.getTag(this, 'Orientation');
  const [width, height] = (orientation > 4) 
                  ? [imgEl.naturalWidth, imgEl.naturalHeight]
                  : [imgEl.naturalHeight, imgEl.naturalWidth];

  canvas.width = width;
  canvas.height = height;
  const context = canvas.getContext('2d');
  // We have to get the correct orientation for the image
  // See also https://stackoverflow.com/questions/20600800/js-client-side-exif-orientation-rotate-and-mirror-jpeg-images
  switch(orientation) {
    case 2: context.transform(-1, 0, 0, 1, width, 0); break;
    case 3: context.transform(-1, 0, 0, -1, width, height); break;
    case 4: context.transform(1, 0, 0, -1, 0, height); break;
    case 5: context.transform(0, 1, 1, 0, 0, 0); break;
    case 6: context.transform(0, 1, -1, 0, height, 0); break;
    case 7: context.transform(0, -1, -1, 0, height, width); break;
    case 8: context.transform(0, -1, 1, 0, 0, width); break;
  }
  context.drawImage(imgEl, 0, 0);
} 
```

瞧，如果你分享一张图片到应用程序，它会旋转图片，然后分析它，返回它找到的文本输出。

[![](img/7845f8a4f083004b726b595549e32375.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wKJj9zTr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paul.kinlan.img/2019-05-13-extracting-text-from-an-imageexperiments-with-shape-detection-2.jpeg)

创建这个小实验非常有趣，而且它立即对我有用。然而，它强调了网络平台的[不一致性。这些 API 并非在所有浏览器中都可用，它们甚至不是在所有版本的 Chrome 中都可用——这意味着当我写这篇文章时，我不能使用 Chrome OS，但与此同时，当我可以使用它时……天哪，太酷了。](https://paul.kinlan.me/the-lumpy-web/)