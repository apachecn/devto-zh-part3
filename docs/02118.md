# 用样式组件获取物理单元

> 原文：<https://dev.to/antonholmberg/getting-physical-units-with-styled-components-3pcl>

今天我遇到了一个有趣的问题。我必须在浏览器中绘制一个 React 组件，并且它必须是以厘米为单位的特定宽度和高度。

所以我天真地认为这就像使用 *cm* 单位在 css 中设置宽度和高度一样简单。我很快发现这是行不通的。我在 stack overflow 上找到了[这个答案](https://stackoverflow.com/questions/18483955/web-and-physical-units)，所以如果你有兴趣找出它为什么不工作，请点击链接。

### 我的初始代码

让我们从我最初的尝试开始:

```
const SomeComponent = styled.div`
  background-color: black;
  width: 8.4cm;
  height: 2.2cm;
`;

function App() {
  return <SomeComponent />;
} 
```

如前所述，这是行不通的。所以让我们试着修复它！

### 一些先决条件

我找到了一个适合我的解决方案。它并不完美，但它完成了任务。如果有人有更好的解决方案，我很想听听！

我将假设你熟悉[样式组件](https://www.styled-components.com)；如果没有，你可能应该读一点，稍后再回来！

我的解决方案的一个先决条件是，我知道代码将在什么设备上运行。我从测量我的实际屏幕开始。在这个例子中，我将使用 13.3 英寸的 MacBook 实际上，我可以在网上找到尺寸，所以不需要尺子(11.25 英寸 x 7 英寸)。

### 解

我采取的第一步是定义一个变量，该变量在 *vw* 和厘米之间映射。我决定在一个[风格的组件](https://www.styled-components.com)主题中定义它，因为这将允许我从组件树的任何地方轻松访问它:

```
function App() {
  return (
    <ThemeProvider theme={{ oneCmInVw: 100 / 28.575 }}>
      <SomeComponent />
    </ThemeProvider>
  );
} 
```

所以我的屏幕是 28.575 厘米(11.25 英寸)宽。如果我用 100 除以 28.575，我会得到一厘米有多少个 vw(假设我的 13.3 英寸 MacBook 上的页面是全屏的)。我现在可以访问这个变量，并以厘米为单位设置*某个组件*的实际宽度和高度。

```
const SomeComponent = styled.div`
  background-color: black;
  width: ${props => props.theme.oneCmInVw * 8.4}vw;
  height: ${props => props.theme.oneCmInVw * 2.2}vw;
`;

function App() {
  return (
    <ThemeProvider theme={{ oneCmInVw: 100 / 28.575 }}>
      <SomeComponent />
    </ThemeProvider>
  );
} 
```

最终结果是一个 84 x 22 毫米的黑匣子。