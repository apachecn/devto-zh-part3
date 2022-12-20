# 如何使用 KendoReact 编辑器

> 原文：<https://dev.to/progresstelerik/how-to-use-the-kendoreact-editor-2900>

该编辑器是 KendoReact 的一个强大而通用的组件，可以很容易地添加和格式化文本和其他 HTML 内容。了解如何在 React 应用中使用和定制它。

[KendoReact](https://www.telerik.com/kendo-react-ui) 中的[编辑器](https://www.telerik.com/kendo-react-ui/components/editor/)组件是一个全功能、高度可定制的 WYSIWYG 富文本编辑器，可以集成到任何需要提供 HTML 编辑的地方(CMS、论坛、票务系统、邮件客户端、聊天客户端等)。它使用户能够输入自由格式的文本，应用各种格式选项，并插入 HTML 内容，如图像、表格和超链接。

该编辑器提供了大量内置工具。您还可以添加自定义工具，更改编辑器所有元素的渲染(自定义渲染)，并通过添加插件来扩展内置功能。与 KendoReact UI 库中的所有其他组件一样，该编辑器是在 TypeScript 中构建的。

在这篇博文中，我将向您展示如何使用编辑器，我们将浏览:

*   KendoReact 编辑器入门
*   如何定制 React 文本编辑器的内置工具
*   如何在 KendoReact 编辑器中实现定制工具
*   为什么要净化粘贴的内容？

## KendoReact 编辑器入门

首先需要从包中导入`Editor`组件和 [<u>`EditorTools`</u>](https://www.telerik.com/kendo-react-ui/components/editor/api/) 模块，[@ progress/kendo-react-editor](https://www.npmjs.com/package/@progress/kendo-react-editor)。然后从`EditorTools`那里拿到需要的工具，传到编辑器的`tools`道具里。通过`defaultContent`道具设置初始内容。使用由`EditorUtils`模块导出的助手`getHtml()`和`setHtml()`函数从`Editor`获取内容或设置新内容。

到目前为止，您不需要知道编辑器如何管理其内容或者工具如何工作。如果您的项目需要定制或需要扩展编辑器的功能，请继续阅读，我们将深入探讨定制或扩展编辑器功能的不同方法。

## 如何自定义 React 文本编辑器的内置工具

有两种方法可以定制该编辑器的内置工具:

1.  使用编辑器的工具函数生成工具
2.  将工具包装成一个[高阶组件](https://reactjs.org/docs/higher-order-components.html) (HOC)函数，通过它添加你需要的新道具

### 使用编辑器的实用功能生成工具

所有编辑器的工具都是 React 组件，由相应的 HOC 函数生成。每个工具还有一个 settings 对象，作为参数传递给它的生成函数。编辑器包导出工具生成所需的功能和设置。例如，粗体工具是通过以下方式创建的:

```
import { EditorToolsSettings, EditorTools } from '@progress/kendo-react-editor';
const BoldTool = EditorTools.createInlineFormatTool(EditorToolsSettings.bold); 
```

将修改后的版本`EditorToolsSettings.bold`传递给`EditorTools.createInlineFormatTool()`将创建一个定制工具。下面是粗体工具的默认设置:

```
const boldSettings = {
  mark: 'strong', // toggle the 'STRONG' tag
  altMarks: ['b'], // recognize the 'B' tag also as Bold

  // recognize an inline node with font-weight style and a
  // value matching the regular expression
  altStyle: { name: 'font-weight', value: /^(bold(er)?|[5-9]\d{2,})$/ },

  // props which will be passed to the Button component of the tool
  props: {
    icon: 'bold',
    type: 'button'
  }
};

// The messages keys used by the tool for localization. See
// also https://www.telerik.com/kendo-react-ui/components/editor/globalization/#toc-messages

{
  messages: {
    title: 'editor.bold'
  },

  // the name of the command that the tool executes
  commandName: 'Bold'
}; 
```

这种方法允许您轻松地修改工具的外观和行为，而不必深入了解整个组件是如何构建的。点击此链接查看编辑器工具生成的设置和功能的完整[列表。](https://www.telerik.com/kendo-react-ui/components/editor/tools/)

### 将工具包装成特设

HOC 将扩展所需工具的属性并返回自定义工具。然后将这个函数添加到您的工具集合中。就这么简单:

```
const CustomBold = (props) => {
  return (
    <Bold
      {...props}
      title="Custom Bold"
    />
  );
};

<Editor
  tools={[
    [CustomBold, /* ... */]
  ]}
  <!-- ... -->
/> 
```

目前所有工具的道具都扩展了 [KendoReact 按钮](https://www.telerik.com/kendo-react-ui/components/buttons/button/)和 [DropDownList](https://www.telerik.com/kendo-react-ui/components/dropdowns/dropdownlist/) 道具。在我们的例子中，自定义工具可用的道具在[按钮道具](https://www.telerik.com/kendo-react-ui/components/buttons/api/ButtonProps/)界面中列出。换句话说，在定制工具时，您还可以配置 KendoReact 按钮或 DropDownList 允许的一切。

## 如何在 KendoReact 编辑器中实现自定义工具

上述定制内置工具的方法也可以用于生成新工具。例如，如果我们采用粗体工具设置，将`mark`更改为`'code'`、`props.icon`更改为`'code-snippet'`，并删除`altMarks`和`altStyle`字段，我们可以生成一个完全不同的工具来切换`<code>`元素。

```
const codeSnippetSettings = {
  mark: 'code', // toggle the 'code' tag
  props: {
    icon: 'code-snippet',
    type: 'button'
  },
  messages: {},
  commandName: 'Code'
};

const CodeTool = EditorTools.createInlineFormatTool(codeSnippetSettings); 
```

编辑器包还导出内置工具使用的所有功能，包括格式化、插入内容等功能。这允许你创建你自己的工具，这些工具的基础已经奠定(例如，如何插入 HTML 内容或应用格式)。

以下是我们的自定义代码工具和一些用于格式化和插入内容的工具的示例:

## 为什么要净化粘贴的内容？

粘贴的 HTML 内容可能看起来很难看，尤其是从 MS Word 中复制的内容。列表显示为样式化的段落，内容可能包含无效的 HTML 样式、注释和 XML 字符串。

根据我们的经验，人们并不总是喜欢内置的粘贴功能。他们通常有特定于项目的需求，这些需求需要在外部处理。出于这个原因，我们决定将格式剥离功能移出编辑器，这样每个人都可以根据需要使用和编辑代码。

### 主题化

与 React 的所有 KendoReact UI 组件一样，编辑器也可以在所有三个现成的主题中进行样式化:Bootstrap 主题、Material 和我们自己的默认主题。如果您在自己的设计系统/主题中工作，您可以使用 CSS 轻松定制编辑器的样式，或者使用 [KendoReact ThemeBuilder](https://themebuilder.telerik.com/kendo-react-ui) 创建自己的主题。

## 引擎盖下

对于编辑器，我们决定使用外部引擎，而不是从头开始实现我们自己的引擎。由于 HTML 编辑已经存在了一段时间，目前有很多可用的引擎，从头开始使用编辑器没有什么价值。在评估了可用选项后，我们决定 [ProseMirror](http://prosemirror.net/) 工具包是我们用例的正确选择。它非常强大，是用纯 JavaScript 编写的。

KendoReact 中的[编辑器](https://www.telerik.com/kendo-react-ui/components/editor/)是一个多功能的 WYSIWYG 富文本编辑器，其功能可以定制或扩展以满足任何项目需求。它是专门为 React 构建的，和框架本身一样快速和轻量级，可以为您节省大量开发时间。