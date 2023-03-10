# webping 你的网站:减少图像文件大小，提高网站性能

> 原文：<https://dev.to/gksander/webp-ing-your-site-reduce-image-file-size-increase-site-performance-4ho8>

## TL；速度三角形定位法(dead reckoning)

`.webp`图像格式可以极大地改善图像文件的大小...我说的是高达 90%的尺寸缩减(从他们的`.jpg`或`.png`同行)。除了 Safari 和 IE，`.webp`图像拥有广泛的浏览器支持，通过像 [sharp](http://sharp.pixelplumbing.com/en/stable/) 这样的工具，你可以很容易地将你收集的`.jpg`和`.png`图像转换成`.webp`图像，提供给可以处理它们的浏览器。检测不支持`.webp`的浏览器相对简单，我将向您展示一种处理这些浏览器的方法。

这个例子的代码可以在 GitHub 上找到[。](https://github.com/gksander/webp-vue)

## 背故事

我是一名软件工程师，在图形/印刷行业工作，所以我处理图像...很多。我构建的大多数应用程序都包含大量图像(用户上传和编辑它们，并显示大量用户上传的内容)。在向用户显示图像时，优化图像符合我们的最佳利益(减少带宽)，但更重要的是，它可以极大地提高网站性能和页面加载速度(因为发送给用户的数据明显减少)。最近，我过渡到尽可能使用`.webp`，我将向你展示我学到的一些技巧。

## 想跟着一起吗？

如果你想跟进，可以查看 GitHub 上的代码[。您可以克隆存储库，然后从文件夹的根目录运行一个`npm install`(只需要一个节点模块，但它很重要)。](https://github.com/gksander/webp-vue)

## 如何创建`.webp`图像？

**[锐利](http://sharp.pixelplumbing.com/en/stable/)。就是这样。**

很有可能没有人给你发电子邮件发送过`.webp`图像文件，或者你从来没有用图形应用程序打开过图像文件。那么，在你的网站中过渡到使用`.webp`图片是一件非常痛苦的事情吗？一开始，我是这么想的。事实证明，没那么糟。

由于我经常处理用户上传的大型图像文件，我习惯于将图像转换成网络版。我选择的后端技术是 node，幸运的是有一个惊人的处理图像的 Node 库: [sharp](http://sharp.pixelplumbing.com/en/stable/) 。夏普**在处理图像方面快得惊人。您可以在几毫秒内调整照片的大小和裁剪照片。这是我的救命稻草。**

您可以向夏普提供各种图像类型(`.jpg`、`.png`、`.tiff`等)。)，操纵图像，以各种格式输出——包括`.webp`。我通常使用 sharp 来对用户上传的图片进行 web ify，但它在编写脚本来处理你自己的文件时同样有用。我将向你展示一种使用 sharp 制作网站图片的`.webp`副本的方法。

如果你打开了我的样本库，你会看到有一个名为`/images`的文件夹，里面有一些图片。唯一的“原始”图像是`dog.jpg`和`chair.png`。我们将生成其他文件。

还有一个名为 [process-images.js](https://github.com/gksander/webp-vue/blob/master/process-images.js) 的文件，其内容如下:

```
// Import sharp (processing images) and path (traversing directory)
const sharp = require('sharp');
const path = require('path');

// Create an asynchronous IIFE
(async function(){
  // Where are our image files located?
  const imageDirectory = path.join(__dirname, './images');
  // Which images should we process?
  const imageNames = ["dog.jpg", "chair.png"];
  // What size should we crop to?
  const maxSize = 1000;

  // Loop through the images and process them one at a time.
  for (let imageName of imageNames) {
    try {
      // Start by creating a jpg version
      await sharp(path.join(imageDirectory, imageName)) // This inputs the file into sharp
        .resize(maxSize, maxSize, { fit: "inside" }) // This resizes our image
        .toFile(
          path.join(imageDirectory, imageName.replace(/\.(jpg|png)$/, `_${maxSize}$&`)) // Replace file extensions with .jpg (assumes .jpg or .png)
        ); // This writes the new image.

      // Same thing, but create a .webp version
      await sharp(path.join(imageDirectory, imageName))
        .resize(maxSize, maxSize, { fit: "inside" })
        .toFile(
          path.join(imageDirectory, imageName.replace(/\.(jpg|png)$/, `_${maxSize}.webp`)) // Replace file extensions with .webp (assumes .jpg or .png)
        ); // This writes the new image.

    } catch (_) {}
  } // End loop

  process.exit();
})(); 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本将获取我们的“原始”图像文件并创建它们的`.webp`版本。这个文件中的内容如下:

我们导入了`path`和`sharp`节点模块(`path`是原生的)。然后，我们将运行一个函数来处理我们的文件。在该功能的顶部，您会看到:

```
// Where are our image files located?
const imageDirectory = path.join(__dirname, './images');
// Which images should we process?
const imageNames = ["dog.jpg", "chair.png"];
// What size should we crop to?
const maxSize = 1000; 
```

Enter fullscreen mode Exit fullscreen mode

这设置了一些要使用的值，比如文件存储的位置(`imageDirectory`变量)，要处理的图像文件(`imageNames`数组)，以及将它们裁剪成什么大小(`maxSize`)。然后，我们将遍历`imageNames`中的每个文件并处理它们。

我们将从调整“原始”图像的大小开始:

```
await sharp(path.join(imageDirectory, imageName)) // This inputs the file into sharp
  .resize(maxSize, maxSize, { fit: "inside" }) // This resizes our image
  .toFile(
    path.join(imageDirectory, imageName.replace(/\.(jpg|png)$/, `_${maxSize}$&`))
  ); // This writes the new image. 
```

Enter fullscreen mode Exit fullscreen mode

这将图像输入到 sharp，告诉 sharp 调整它的大小，然后输出文件。吓人的表情

```
imageName.replace(/\.(jpg|png)$/, `_${maxSize}$&`) 
```

Enter fullscreen mode Exit fullscreen mode

只是告诉夏普在文件扩展名前加一个“_1000”，这样`dog.jpg`就变成了`dog_1000.jpg`，`chair.png`就变成了`chair_1000.png`。

我们将运行一个类似的过程，但是给文件添加一个扩展名`.webp`。Sharp 将自动把那个文件写成一个`.webp`文件- *，这就是神奇发生的地方*。对于我们的每个“原始”文件，我们应该有一个裁剪过的变体，以及一个裁剪过的`.webp`变体——都在同一个文件夹中。

一旦我们写好了脚本，我们需要从命令行运行下面的命令:

```
node process-images.js 
```

Enter fullscreen mode Exit fullscreen mode

这就是处理我们的文件所需要的一切！作为一个有趣扩展，您可以轻松地扩展该脚本，为每个图像创建多种不同的大小(比如，一个用于缩略图，一个用于“英雄”照片)。

## 使用我们的新图像

大多数浏览器支持`.webp`图片——但是 Safari 和 IE 不支持。我认为 Safari 拥有足够大的浏览器市场份额，足以证明为不支持`.webp`图像的浏览器提供后备是合理的(我试图假装 IE 不再存在，但这应该也能处理 IE)。出于说明的目的，我将显示一个简单的 Vue“应用程序”,它将在可能的情况下显示`.webp`图像，并在必要时返回到`.jpg`或`.png`。

在代码回购中，你会发现一个文件【index.html[T3】，其中包含*非常裸露的* HTML 和一些 Vue 来显示你如何可以在`.webp`图像中喷洒。`index.html`文件包含一小段 HTML:](https://github.com/gksander/webp-vue/blob/master/index.html) 

```
<div id="app">
  <h1>Webp supported: {{ webpSupported ? 'Yes' : 'No' }}</h1>

  <!-- Show the chair photo -->
  <img
    :src="transformImgExtimg/chair_1000.png')"
    width="150px"
  />

  <!-- Show the dog photo -->
  <img
    :src="transformImgExtimg/dog_1000.jpg')"
    width="150px"
  />

</div> 
```

Enter fullscreen mode Exit fullscreen mode

标签是我们展示新图片的地方。如果你不熟悉 Vue.js，图像标签的`:src`属性向 Vue 表明我们想要一个带有给定值的动态`src`属性。我们将编写一个函数`transformImgExt`，它将获取一个图片 URL，如果合适的话，用一个`.webp`版本替换它。例如，`transformImgExtimg/chair_1000.png')`会给我img/chair_1000.png`的相对 url，但是如果浏览器支持`.webp`图片，请尝试img/chair_1000.webp`替换它。

## 检测浏览器支持

让我们深入研究一下检测对`.webp`的支持所需的 JavaScript。这里是`index.html`文件中的 JS。(如果你不熟悉 Vue.js，也不用太在意细节。)

```
let app = new Vue({
  // What should we mount our Vue instance to?
  el: "#app",

  // App data
  data: {
    // We'll initially assume webp is supported
    webpSupported: true
  },

  // Methods
  methods: {
    /**
     * Helper to transform image extension.
     * Checks if webp is supported, and will swap out the image extension accordingly.
     */
    transformImgExt (url) {
      // If webp is supported, transform the url
      if (this.webpSupported) {
        return url.replace(/\.\w{1,5}$/, ".webp");
      } else { // Otherwise, just return the original
        return url;
      }
    }
  },

  /**
   * When app is "created", we'll run some checks to see if the browser supports webp
   */
  created() {
    (async () => {
      // If browser doesn't have createImageBitmap, we can't use webp.
      if (!self.createImageBitmap) {
        this.webpSupported = false;
        return;
      }

      // Base64 representation of a white point image
      const webpData = 'data:image/webp;base64,UklGRiQAAABXRUJQVlA4IBgAAAAwAQCdASoCAAEAAQAcJaQAA3AA/v3AgAA=';
      // Retrieve the Image in Blob Format
      const blob = await fetch(webpData).then(r => r.blob());
      // If the createImageBitmap method succeeds, return true, otherwise false
      this.webpSupported = await createImageBitmap(blob).then(() => true, () => false);

    })();
  } // End created

}) 
```

Enter fullscreen mode Exit fullscreen mode

您将在代码中看到一个`data`属性:

```
// App data
data: {
  // We'll initially assume webp is supported
  webpSupported: true
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的“应用”状态。我们将创建一个名为`webpSupported`的状态属性来保存一个布尔值，表明我们是否支持`.webp`。一旦我们做了一些“嗅探”来看看我们的浏览器是否能处理`.webp`图像，我们可能会改变这个值。

接下来，让我们跳到`created()`部分:

```
/**
 * When app is "created", we'll run some checks to see if the browser supports webp
 */
created() {
  (async () => {
    // If browser doesn't have createImageBitmap, we can't use webp.
    if (!self.createImageBitmap) {
      this.webpSupported = false;
      return;
    }

    // Base64 representation of a white point image
    const webpData = 'data:image/webp;base64,UklGRiQAAABXRUJQVlA4IBgAAAAwAQCdASoCAAEAAQAcJaQAA3AA/v3AgAA=';
    // Retrieve the Image in Blob Format
    const blob = await fetch(webpData).then(r => r.blob());
    // If the createImageBitmap method succeeds, return true, otherwise false
    this.webpSupported = await createImageBitmap(blob).then(() => true, () => false);

  })();
} // End created 
```

Enter fullscreen mode Exit fullscreen mode

这是我在[这篇文章](https://ourcodeworld.com/articles/read/630/how-to-detect-if-the-webp-image-format-is-supported-in-the-browser-with-javascript)中发现的一个技巧。这个代码片段检查浏览器是否有一个`createImageBitmap`方法——如果没有，就不支持`.webp`。然后，我们将创建一个 base64 编码的 webp 图像作为 blob，并尝试从它创建一个图像位图。如果可以，那么浏览器支持`.webp`。这背后有一些技术细节，但这超出了本文的范围。

在代码片段的结尾，您会注意到语句:

```
this.webpSupported = await createImageBitmap(blob).then(() => true, () => false); 
```

Enter fullscreen mode Exit fullscreen mode

该语句的 RHS 试图创建图像位图，如果成功，将运行`() => true`函数表达式(返回`true`，否则将运行`() => false`函数表达式(返回`false`)。这给了我们一个`this.webpSupported`的值，它引用了我们之前看到的应用程序状态属性。此时，我们的`data.webpSupported`属性持有一个布尔值，它实际上告诉我们我们的浏览器是否支持`.webp`图像。

我们还有最后一件要看的东西:方法

```
/**
 * Helper to transform image extension.
 * Checks if webp is supported, and will swap out the image extension accordingly.
 */
transformImgExt (url) {
  // If webp is supported, transform the url
  if (this.webpSupported) {
    return url.replace(/\.\w{1,5}$/, ".webp");
  } else { // Otherwise, just return the original
    return url;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法将获取一个 url，如果支持`.webp`，它将交换出`.webp`的文件扩展名。否则，它只会把网址还给你。

让我们检查一下线路

```
return url.replace(/\.\w{1,5}$/, ".webp"); 
```

Enter fullscreen mode Exit fullscreen mode

虽然有点远。如果您不熟悉 JS 中的正则表达式，这可能看起来像随机字符。我们使用字符串“替换”方法。`/\.\w{1,5}$/`是一个寻找文件扩展名的正则表达式。开头的`\.`表示“.”，则`\w{1,5}`会寻找 1 到 5 个字母(单词字符？)，末尾的`$`表示应该在字符串的最末尾。如果我们找到匹配的内容，我们将用“替换我们找到的内容。webp”。这应取代"。jpg“或”。png“同”。webp”。(小心！这也会转变”。pdf“至”。webp”。如果需要的话，你可以更紧一点。)

现在，我们可以使用`transformImgExt`来尝试提供一个`.webp`图像扩展，如果我们的浏览器能够处理它的话。我们之前看到过。

## 见其行动

我把这些资源放在一个代码栏里来展示这个。(图片的网址不同，因为它们是通过 CodePen 上传的。)如果你在 Chrome、FireFox 或 Edge 中打开笔，你应该会看到图像确实是`.webp`。右键单击并检查它，或者在新标签中打开图像，注意它确实是一个`.webp`图像。如果您在 Safari 或 IE 中打开笔，您应该会看到图像是`.jpg`或`.png`图像。

[https://codepen.io/gksander/embed/QPEMeZ?height=600&default-tab=result&embed-version=2](https://codepen.io/gksander/embed/QPEMeZ?height=600&default-tab=result&embed-version=2)

## 我们存了多少钱？

压缩文件的方式非常简洁，但是不同的图片会给你带来不同的好处。让我们来看看这个例子带来的节约。

*   `dog_1000.jpg`大小为 122 KB。`dog_1000.webp`大小为 90 KB。节省了 25%的费用。*不错！*
*   `chair_1000.png`大小为 778 KB。`chair_1000.webp`有一个`81KB`的大小。**节省了 89.5%**。太神奇了。

在实际使用中，我平均节省了 40-60%的成本。`.png`图像似乎提供了最大的节约，`.webp`图像支持透明！

## 结束语

`.webp`整齐。使用它们可能会节省大量文件大小。然而，要把它们整合到一个网站上还需要一点工作。以下是与此相关的一些杂项结束语。

*   使用 sharp，您可以控制“to-webp”转换的各个方面，例如您是否希望压缩是无损的，等等。
*   夏普的速度非常快，所以做这种转换并不昂贵。
*   如上所示，我一直在“浏览”静态网站资产，但也“浏览”用户上传的内容。通常，当用户上传文件时，我会创建一个调整过大小的`.jpg`版本，以及一个调整过大小的`.webp`。以闪电般的速度撕开这些伤口。
*   我通常创建一个方法，比如上面显示的`transformImgExt`，但是在我的应用程序中公开它，并且在我想要显示`.webp`图像的任何地方使用它。这使得它可以重复使用，并且将新的图像格式整合到你的应用程序中并不需要太多的工作(具有对 Safari 和 IE 的后备支持)。