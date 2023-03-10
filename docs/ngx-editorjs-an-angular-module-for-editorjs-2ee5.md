# 一个用于编辑器的角度模块

> 原文：<https://dev.to/tanepiper/ngx-editorjs-an-angular-module-for-editorjs-2ee5>

今天我在 [NPM](https://www.npmjs.com/package/@tinynodes/ngx-editorjs) 上发布了我的第一个全角度模块- `@tinynodes/ngx-editorjs`。

该模块是 Angular (7+)创建和控制 [EditorJS](https://editorjs.io) 实例的一组特性。

一个[演示应用程序](https://tinynodes-ngx.firebaseapp.com/ngx-editorjs-demo)可用于查看编辑器的运行，源代码也可在 [`@tinynodes` monorepo](https://github.com/tanepiper/ngx-tinynodes) 中获得。

## 包含的特性

一旦`NgxEditorJSModule`被包含在您的项目中，库就会导出几个特征。

#### NgxEditorJSDirective

这是主指令，可以用在任何带有`[ngxEditorJS]`选择器和`id`属性的元素上。

```
<div id="my-editor" ngxEditorJS></div> 
```

Enter fullscreen mode Exit fullscreen mode

这将通过`NgxEditorJSService`处理编辑器实例的创建

#### NgxEditorJSComponent

使用`<ngx-editorjs>`标签，该组件可用于任何角度组件。同样，这个组件可以接受一组块，它还提供了一个`holder`输入来覆盖 ID。

```
<ngx-editorjs [holder]="holderProperty"></ngx-editorjs> 
```

Enter fullscreen mode Exit fullscreen mode

#### NgxEditorJSService

该服务提供对 EditorJS 实例生命周期的处理，并公开底层的[editor js API](https://editorjs.io/api)——在未来的版本中，更多的 API 将通过服务方法公开，以使控制容器更容易。

该模块是可配置的，并允许将 EditorJS 插件注入到库中-(参见 [README.md](https://github.com/tanepiper/ngx-tinynodes/blob/master/libs/ngx-editorjs/README.md) )如何以一种 AOT 友好的方式做到这一点。

## 下一步

在发布之前，我必须解决 AOT 编译和注入 EditorJS 插件的问题——这意味着版本`1.1.0`已经发布——现在这个问题已经解决了。我希望在编辑器中支持额外的功能，以及提供单元测试覆盖和更好的文档和演示。

如果您发现任何问题或有功能请求，可以留在 [GitHub 问题](https://github.com/tanepiper/ngx-tinynodes/issues)页面。