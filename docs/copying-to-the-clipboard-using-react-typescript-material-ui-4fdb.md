# 使用 React、TypeScript 和 Material UI 复制到剪贴板

> 原文：<https://dev.to/kamranayub/copying-to-the-clipboard-using-react-typescript-material-ui-4fdb>

在应用程序中提供“复制到剪贴板”的方法是很常见的，这样用户就可以粘贴内容。

我使用 TypeScript 和 React 创建了一个简单的例子，使用 [render props 模式](https://pluralsight.pxf.io/x5PN5)可以将这样的行为应用于任何 React 元素。

[https://codesandbox.io/embed/x3564644kp?module=/src/CopyToClipboard.tsx](https://codesandbox.io/embed/x3564644kp?module=/src/CopyToClipboard.tsx)

然后，您可以像这样使用它来包装`<Button />`或任何其他元素:

```
<CopyToClipboard>
  {({ copy }) => (
    <Button
      variant="contained"
      color="primary"
      onClick={() => copy("some text!")}
    >
      Copy
    </Button>
  )}
</CopyToClipboard> 
```

这是使用超级简单的[剪贴板复制](https://www.npmjs.com/package/clipboard-copy)包。

您可以使用`TooltipProps`道具覆盖`<Tooltip />`道具:

```
<CopyToClipboard TooltipProps={{ title: "Copied XYZ!", leaveDelay: 3000 }}> 
```

尽情享受吧！