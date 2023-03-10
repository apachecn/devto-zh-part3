# 如何用 vue 和 firepond 上传文件

> 原文：<https://dev.to/pqina/how-to-upload-files-with-vue-and-filepond-1m02>

[FilePond](https://pqina.nl/filepond/) 文件上传库已经存在一年了，让我们探索一下如何将它与 Vue 集成。在本文中，我们将设置一个新的 Vue 应用程序，并将该应用程序与 Vue FilePond 适配器链接起来。完成后，我们将建立一个连接到后端，将接收我们上传的文件。

我们将使用 [Vue CLI](https://cli.vuejs.org/) 快速创建我们的基本 Vue 应用程序。如果您熟悉这个过程，您可以跳到与 FilePond 集成一节。

## 创建 Vue 应用

假设您已经安装了 Node 和 Vue CLI(如果您还没有安装它们，这将是一个好时机)，从您的终端运行以下命令。它将创建一个文件夹“我的应用程序”，其中将包含我们的 Vue 应用程序。

```
vue create my-app 
```

Enter fullscreen mode Exit fullscreen mode

我们会遇到一个或多个问题，我们会选择默认选项。

安装完成后，使用以下命令导航到项目。

```
cd my-app 
```

Enter fullscreen mode Exit fullscreen mode

现在用下面的命令启动开发服务器。

```
npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以导航到`http://localhost:8080/`来查看我们的 Vue 应用程序。

您可以通过同时按下`CTRL`和`C`键从命令行停止 Vue 应用程序。

## 与 FilePond 集成

有了我们的 Vue 应用程序，我们现在可以添加 FilePond Vue 适配器。

让我们停止应用程序(按`CTRL` + `C`)并安装适配器。

```
npm install filepond vue-filepond --save 
```

Enter fullscreen mode Exit fullscreen mode

现在适配器文件已经安装好了，让我们再次启动应用程序，这样我们就可以开始进行更改了。

我们将把 FilePond 组件添加到应用程序登录页面，实际上您可能会把它移动到其他地方，但是对于本文的目的来说，这已经足够了。

我们首先需要导入 FilePond 组件及其 CSS 文件。

打开“src/App.vue”文件，将以下几行添加到`<script>`块的顶部。

```
import vueFilePond from 'vue-filepond';
import 'filepond/dist/filepond.min.css'; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要注册我们的`FilePond`组件。我们通过将它添加到组件列表中来做到这一点，如下所示。

```
export default {
  name: 'app',
  components: {
    FilePond: vueFilePond()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下一步是将组件添加到 HTML 中。让我们编辑`<template>`标签中的 HTML，并用`<FilePond/>`标签替换`<div>`的内容。

```
<template>
  <div id="app">
    <FilePond/>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

文件池拖放区域现在应该呈现在屏幕上🚀

[![](img/8604453199d6453278d53d4318b54df8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SJNESsnK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uaffpd6b6fjm70zt0px0.png)

现在，我们可以开始按照自己的意愿配置 FilePond 了。

默认情况下，FilePond 只接受一个文件，这是因为在水下它增强了默认的[文件输入元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/file)，并复制了它的标准行为。

[![](img/e625beee41b483ef9e7cec97948e63cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_9Fa58s4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogfdpkalgvyvfgh7e407.gif)

要启用多文件模式，让我们添加`allowMultiple`属性。

```
<FilePond allowMultiple="true"/> 
```

Enter fullscreen mode Exit fullscreen mode

让我们放下一个文件夹或选择多个文件来看看它的作用。

[![](img/77567584b0a7267c29184090e2cda2eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tRGuGhKc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3d8bvisu48jwn5jqx5ft.gif)

就像经典的文件输入一样，文件被加载到 FilePond，但不做任何其他事情，它们只是呆在那里。我们很可能希望将它们发送到我们的后端。我们将通过为 FilePond 提供服务器属性来实现这一点。

如果你已经准备好了一个服务器，并且它被设置为处理文件对象，你或许可以使用它，如果没有，我们可以设置 [FilePond PHP 样板文件](https://github.com/pqina/filepond-boilerplate-php)(下载存储库并运行`vagrant up`)。

```
<FilePond allowMultiple="true" server="http://192.168.33.10"/> 
```

Enter fullscreen mode Exit fullscreen mode

配置好服务器位置后，FilePond 会自动将放置和选择的文件发布到提供的 URL。

[![](img/648cae41b16bd4fece59e2986ea69cc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m4156kW7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jgnww7jtt5uhx67wfbeo.gif)

默认文件池服务器调用在[服务器配置文档](https://pqina.nl/filepond/docs/patterns/api/server/)中描述。服务器属性可以在很大程度上进行微调，这使得 FilePond 基本上可以与任何远程或本地文件存储解决方案集成。

让我们注册 [FilePond 图像预览插件](https://github.com/pqina/filepond-plugin-image-preview)来使拖放的图像看起来更好一些。

```
npm install filepond-plugin-image-preview --save 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要导入并向 FilePond 核心注册插件。我们必须修改`vueFilePond`调用来使用图像预览插件。

```
import vueFilePond from 'vue-filepond';
import 'filepond/dist/filepond.min.css';

import FilePondPluginImagePreview from 'filepond-plugin-image-preview';
import 'filepond-plugin-image-preview/dist/filepond-plugin-image-preview.min.css';

export default {
  name: 'app',
  components: {
    FilePond: vueFilePond(FilePondPluginImagePreview)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。让我们重新启动应用程序，并删除我们的第一张图片。

[![](img/6ec6649a70ac51362ad7b4b4f773a8ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mnx0VNDc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sug3f1yeer5rvyl8k7dt.gif)

这就是用 Vue 和 FilePond 上传文件的基础。

你可以通过自动 [EXIF 方向校正](https://github.com/pqina/filepond-plugin-image-exif-orientation)、[图像裁剪](https://github.com/pqina/filepond-plugin-image-crop)、[大小调整](https://github.com/pqina/filepond-plugin-image-resize)、[客户端图像变换](https://github.com/pqina/filepond-plugin-image-transform)、[图像编辑](https://pqina.nl/doka/)、[各种其他插件](https://pqina.nl/filepond/plugins.html)等特性进一步增强组件。

有一个完整的[属性](https://pqina.nl/filepond/docs/patterns/api/filepond-instance/#properties)和[事件](https://pqina.nl/filepond/docs/patterns/api/filepond-instance/#callbacks)列表，您可以对其进行配置以使 FilePond 满足您的需求。

如果您有任何问题，请在 [Twitter](https://twitter.com/rikschennink/) 上找到我，或者在下面留言。