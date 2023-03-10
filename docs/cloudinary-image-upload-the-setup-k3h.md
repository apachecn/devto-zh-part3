# 云二进制图像上传-设置

> 原文：<https://dev.to/ogwurujohnson/cloudinary-image-upload-the-setup-k3h>

在访问这个页面之前，你可能偶然发现了关于如何使用 cloudinary 插件在你的应用程序上设置图像上传功能的教程，但很少有人真正谈到如何在没有插件的情况下做到这一点，用简单的方式，我们中的一些人实际上更喜欢这种方式，因为它让我们可以控制如何调整我们想要的东西。为了不走捷径，直接进入本文的主题，我们将采用更多的实践方法。

我会分解这个过程，并在必要的地方使用图片

首先，创建一个 cloudinary 帐户，然后按照以下步骤处理您的首次上传。

从您的仪表板中，找到您的 API 基本 URL 并将其复制出来。
[![API Base URL](img/3e8d96676e2f90561ab28ea67eadede5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--GtmB6x9m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/amkxt46qw9dbky7eyqc1.png)

接下来，我们需要获得我们的预设 ID，没有它，cloudinary 将不会处理我们的图像上传。要做到这一点，按照步骤(1 - 4)，如果你已经有这些，你可以跳到文章的结尾。

1.  点击右上方的设置图标，查看下图获取指导。![click on settings](img/b189f5f74b803c835919ac83bce104e3.png)
2.  在设置页面上，点击上传选项卡，向下滚动到`upload presets section`。![upload preset section](img/78407eecb6c0341bc9641e29d494270e.png)
3.  点击粗体文本**启用未签名上传**，这允许用户将图像和其他资产上传到您的 Cloudinary 帐户，而无需预先签署上传请求。出于安全原因，未签名的上传需要使用上传预设。去拿我们的`upload presets`；

4.  点击`Add upload presets link`，在打开的页面上，复制你的`Upload preset name`，将`signing mode`改为`unsigned`，然后保存。
    [![signing mode](img/f0f12833c105d44bc5d0a9172b5b2db0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---_kBV52X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p3unww85ht2pypjywqbu.png)

Haven 获取了您的`API base URL`和`Upload preset name`，您现在可以编写代码来方便图像上传，在这个示例中，我们将使用 JavaScript 和 fetch API，您可以使用您选择的任何语言。

我们有一个带有文件输入字段的 HTML 虚拟文件，

```
<input type="file" id="fileupload"> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只需要这段 Javascript 代码来处理这个上传请求，我会在代码之后解释这个过程。

```
const CLOUDINARY_URL = 'https://api.cloudinary.com/v1_1/lagos/image/upload';
const CLOUDINARY_UPLOAD_PRESET = 'moox1jnq';
const image = document.querySelector('#fileupload');
image.addEventListener('change', (e) => {
  const file = e.target.files[0];
  const formData = new FormData();
  formData.append('file', file);
  formData.append('upload_preset', CLOUDINARY_UPLOAD_PRESET);

  fetch(CLOUDINARY_URL, {
    method: 'POST',
    body: formData,
  })
    .then(response => response.json())
    .then((data) => {
      if (data.secure_url !== '') {
        const uploadedFileUrl = data.secure_url;
        localStorage.setItem('passportUrl', uploadedFileUrl);
      }
    })
    .catch(err => console.error(err));
}); 
```

Enter fullscreen mode Exit fullscreen mode

从代码中，我们看到，首先，我们需要两个信息，我们的`API base URL`和我们的`upload preset name`。对于我们的`base URL`，我们将把`/image/upload`添加到它上面，并把它赋给一个变量。然后我们同样把我们的`preset name`赋给一个变量。我给我的取名`CLOUDINARY UPLOAD PRESET`。

我们检查表单字段状态的事件变化，以便当用户选择一个图像时，我们检查上传的文件信息。现在，要将这些信息发送到 cloudinary，我们将使用“formData API”。它提供了一种方法，可以很容易地构造一组表示表单字段及其值的键/值对，然后可以很容易地作为请求发送。

我们会将上传的文件添加到 formData API 中，并将我们的`upload preset name`添加到 formData API 中，cloudinary 会在这里查找这些信息。请注意，键的名称应该始终是`upload_preset`，没有大小写只有下划线，如果不是 cloudinary 就不会处理上传。

最后，我们向`cloudinary Base API URL`发出一个获取请求，并将我们的 formData 作为请求体传入。

如果请求处理成功，将返回一个`secure_url`,将您链接到上传的图像。

嘣！！！！！！嘣！！！！！！嘣！！！！！

免责声明:请注意，这种方法可能会让其他人看到我们的`image upload URL`和`upload preset name`(当他们只是查看您的站点脚本时)，这可能会导致用户主动上传包含您详细信息的图像。

我将在教程结束时更改我的详细信息，所以请不要使用它们。英雄联盟