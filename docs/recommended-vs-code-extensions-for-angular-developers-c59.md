# 推荐 Angular 开发人员使用的 VS 代码扩展

> 原文：<https://dev.to/azure/recommended-vs-code-extensions-for-angular-developers-c59>

经常有人问我，“你最喜欢 Angular 的哪些 VS 代码扩展？”。这篇文章涵盖了我在角度开发中最常用的 VS 代码扩展。

> 合适的工具适合合适的工作。

我们经常听到这种说法。当我们使用好的工具时，我们的开发生活变得更加高效。我创建了一个扩展包，通过安装这个扩展就可以很容易地获得所有这些。

虽然还有其他值得注意和优秀的扩展，但这个列表是我在一个扩展包中维护的(它是可变的)。扩展包的酷之处在于你可以一次安装一组相关的扩展。

## 棱角分明的要领

你可以通过安装我的[Angular Essentials extension pack for VS Code](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular-essentials&wt.mc_id=devto-blog-jopapa)来最容易地获得它们。

*   点击[从扩展包中安装所有的](https://marketplace.visualstudio.com/items?itemName=johnpapa.angular-essentials&wt.mc_id=devto-blog-jopapa)

...或者...如果您不想全部安装，也可以逐个安装。

### 包含扩展名

以下是该软件包包含的扩展列表，按字母顺序排列:

*   这个扩展扩展了 javascript 和 typescript 语言，为内嵌模板和样式表增加了 Angular2 特有的特性。当您使用反勾字符定义内联模板或内联样式表时，内容将由该扩展处理。

*   Angular 语言服务(Angular Language Service)这个扩展为 Angular 模板提供了丰富的编辑体验，包括内联模板和外部模板。这个扩展是 Angular 团队的成员带给你的。它非常有助于在 html 模板中编写可靠的代码。

*   [Angular snippet](https://marketplace.visualstudio.com/items?itemName=johnpapa.Angular2&wt.mc_id=devto-blog-jopapa)-遵循官方风格指南的 Angular snippet，用于打字稿、模板和 RxJS。

*   Chrome 调试器-VS Chrome 的代码调试器。

*   [编辑器配置](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig&wt.mc_id=devto-blog-jopapa)-VS 代码的编辑器配置。非常适合保持一致的编辑器设置。

*   [素材图标主题](https://marketplace.visualstudio.com/items?itemName=pkief.material-icon-theme&wt.mc_id=devto-blog-jopapa)在资源管理器中显示素材图标

*   [npm](https://marketplace.visualstudio.com/items?itemName=eg2.vscode-npm-script&wt.mc_id=devto-blog-jopapa) 这个扩展支持运行 package.json 文件中定义的 npm 脚本，并根据 package.json 中定义的依赖项验证已安装的模块。

*   [孔雀](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa)这个扩展巧妙地改变了你工作区的工作区颜色。当您有多个 VS 代码实例并且想要快速识别哪个是哪个时，这是理想的选择。

*   更漂亮的与更漂亮的代码插件相比，代码格式一致

*   使用 TypeScript TSLint 语言服务插件将 tslint 添加到 VS 代码中。

*   冬天来了，我喜欢用深蓝色的主题

## 调试 VS 代码中的角度

Chrome Debugger 扩展非常出色，它使您能够在 VS 代码中调试前端 javascript。您应该按照文档中的指南通过[进行设置。](https://code.visualstudio.com/docs/nodejs/angular-tutorial#_configure-the-chrome-debugger?wt.mc_id=devto-blog-jopapa)

tldr

如下所示，将`chrome`的配置添加到您的`launch.json`中。

```
{  "version":  "0.2.0",  "configurations":  [  {  "type":  "chrome",  "request":  "launch",  "name":  "Launch Chrome against localhost",  "url":  "http://localhost:4200",  "webRoot":  "${workspaceFolder}"  }  ]  } 
```

然后用`ng serve -o`运行你的 angular app，在 VS 代码中设置一些断点，按`F5`。

## 还有什么

如果你有其他适合 Angular Essentials 的扩展的想法，请在这里的评论中讨论，或者作为 GitHub 中的一个问题。

我有一些想法，目前正在评估中...包括[角控制台](https://marketplace.visualstudio.com/items?itemName=nrwl.angular-console&wt.mc_id=devto-blog-jopapa)，极有前途！