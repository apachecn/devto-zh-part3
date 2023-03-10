# 可访问的 React 手风琴组件

> 原文：<https://dev.to/stereobooster/accessible-react-accordion-component-4p99>

关于编写 React accessible accordion 组件有多简单的小实验(转到教程中)。我以前没有可访问性方面的经验(嗯，也许是一些基本的东西，比如使用 alt param 不要使用链接作为按钮)。

我遵循 WAI-ARIA 创作实践 1.1 中的[手风琴式设计模式，别无其他。](https://www.w3.org/TR/wai-aria-practices/examples/accordion/accordion.html)

本教程的重点是 a11y 和 React，所以我们不会关心如何打包到 npm 或 CSS-in-JS 或其他任何东西。在这种情况下，最简单的方法是使用 create-react-app。

### 自举

让我们启动我们的项目

```
npx create-react-app my-app
cd my-app
npm start 
```

[去掉一切无关的东西](https://github.com/stereobooster/react-accessible-accordion/commit/6664bb290e5d68f415bd236202c61a091a5cc80e)。

### 设计 API

是时候考虑一下组件的 API 了。让我们来看看典型的手风琴:

[![](img/2d020c3ba3c6fb0153abf62364ed2977.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3HeGWSiw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i8ca76wwevbvyewidy8v.png)

有一个根组件和几个部分。每个部分都有一个标题和内容。对吗？基于此，我们可以想象我们的 API 会是什么样子:

```
const App = () => (
  <Accordion>
    <AccordionSection title="section 1">content 1</AccordionSection>
    <AccordionSection title="section 2" expanded>
      content 2
    </AccordionSection>
  </Accordion> ); 
```

让我们为给定的 API
编写组件的初稿

```
import React from "react";

export const Accordion = ({ children }) => <div>{children}</div>; 
export const AccordionSection = ({ children, title, expanded }) => (
  <>
    <div>{title}</div>
    <div>{expanded && children}</div>
  </> ); 
```

### 添加 a11y

很好。让我们在 WAI-ARIA 创作实践 1.1 中打开[手风琴式设计模式，并复制粘贴所有需要的标记:](https://www.w3.org/TR/wai-aria-practices/examples/accordion/accordion.html) 

```
export const AccordionSection = ({ children, title, expanded, id }) => {
  const sectionId = `section-${id}`;
  const labelId = `label-${id}`;

  return (
    <>
      <div
        role="button"
        aria-expanded={expanded}
        aria-controls={sectionId}
        id={labelId}
        tabIndex={0}
      >
        {title}
      </div>
      <div
        role="region"
        aria-labelledby={labelId}
        id={sectionId}
        hidden={!expanded}
      >
        {expanded && children}
      </div>
    </>
  );
}; 
```

`Accordion`不需要改变。有两个元素:标题和面板。表头(`role="button"`)有`id`和`aria-controls`(对应面板的`id`)。面板(`role="region"`)有`id`和`aria-labelledby`(对应表头的`id`)。`aria-expanded`节是否展开。`hidden`与截面是否展开相反。在我看来很简单。

[再来加点风格](https://github.com/stereobooster/react-accessible-accordion/commit/2eb6ad679485339213d08e8c9c887faab5ecae10)。

[![](img/46e565e1db8a7a40f072be6fedbc6763.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vffIVe2T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fn7hcxim89iqbr7ptbxy.png)

[现在是添加状态和事件处理](https://github.com/stereobooster/react-accessible-accordion/commit/8f014094701860a879efa7179223af282590e980)的时候了。

状态和回调(我们正在创建[受控组件](https://reactjs.org/docs/forms.html#controlled-components) ):

```
function App() {
  const [expanded1, setExpanded1] = useState(false);
  return (
      <Accordion>
        <AccordionSection
      ...
          expanded={expanded1}
          onToggle={() => setExpanded1(!expanded1)}
        > 
```

事件处理

```
export const AccordionSection = ({
  ...
  expanded,
  onToggle
}) => {
  ...
  return (
    <>
      <div
        role="button"
        ...
        onClick={onToggle}
        onKeyDown={e => {
          switch (e.key) {
            case "  ":
            case "Enter":
              onToggle();
              break;
            default:
          }
        }}
      > 
```

### 让我们暂停一下

在这一点上，已经是相当不错的成绩了。我们完成了 a11y 的一半要求，没有那么多的工作。

*   `Space`或`Enter`
    *   当焦点在折叠部分的折叠标题上时，展开该部分。
*   `Tab`
    *   将焦点移到下一个可聚焦的元素。
    *   折叠面板中所有可聚焦的元素都包含在页面选项卡序列中。
*   `Shift` + `Tab`
    *   将焦点移到上一个可聚焦的元素。
    *   折叠面板中所有可聚焦的元素都包含在页面选项卡序列中。

如果你至少做到了这一点，那已经比什么都没有强了。

### 更 a11y

[下一节](https://github.com/stereobooster/react-accessible-accordion/commit/3d894bf54170b0ad0b643058118b615876250ea2)有点复杂(更好玩)。

*   `Down Arrow`
    *   当焦点在折叠标题上时，将焦点移动到下一个折叠标题。
    *   当焦点在最后一个折叠项标题上时，将焦点移动到第一个折叠项标题。
*   `Up Arrow`
    *   当焦点在折叠标题上时，将焦点移动到上一个折叠标题。
    *   当焦点在第一个折叠项标题上时，将焦点移动到最后一个折叠项标题。

要做到这一点，我们需要跟踪哪里是焦点，能够选择下一个或上一个部分。我们需要为每个手风琴存储一次变量。所以也许`useState`？嗯，但是我们不想在焦点改变时触发组件的重新呈现。然后`useRef`我猜。

```
export const Accordion = ({ children }) => {
  const focusRef = useRef(null); 
```

`focusRef`将包含当前聚焦部分的`id`或者在没有选择的情况下包含`null`。我们需要跟踪标题上的`focus`和`blur`事件。

```
<div
  role="button"
  ...
  onFocus={() => {
    focusRef.current = id;
  }}
  onBlur={() => {
    focusRef.current = null;
  }} 
```

那么我们如何将`focusRef`从`Accordion`传递到`AccordionSection`？我们可以通过道具(用`React.Childre.map`和`React.CloneElement`)或者上下文来完成。我更喜欢上下文思想，因为它创建了更干净的 API。

创建上下文:

```
const AccordionContext = createContext({
  focusRef: {}
});
export const useAccordionContext = () => useContext(AccordionContext); 
```

将`focusRef`传递给`Context`(我使用`useMemo`来确保我们不会因为`Context`中的更新而触发不需要的重新呈现器)

```
const context = useMemo(
  () => ({
    focusRef
  }),
  []
);

return (
  <AccordionContext.Provider value={context}>
    {children}
  </AccordionContext.Provider>
); 
```

而在`AccordionSection`和

```
const { focusRef } = useAccordionContext(); 
```

好了，这样我们就可以捕捉当前选中的部分。现在我们需要响应键盘事件，让我们在根组件中添加一个处理程序:

```
export const AccordionSection = ({}) => {
  ...
  return (
    <div
      onKeyDown={e => {
        switch (e.key) {
          case "ArrowDown":
            break;
          case "ArrowUp":
            break;
          case "Home":
            break;
          case "End":
            break;
        }
      }}
    >
      <AccordionContext.Provider value={context}> 
```

在`ArrowDown`的情况下，我们需要找到`children`中的`focus`元素，并选择下一个。我们可以得到一个包含`children`元素
的所有`id`的数组

```
const ids = React.Children.map(children, child => child.props.id); 
```

然后找到聚焦元素的索引

```
const index = ids.findIndex(x => x === focusRef.current); 
```

然后找到下一个值

```
if (index >= ids.length - 1) {
  return ids[0];
} else {
  return ids[index + 1];
} 
```

很好。但是我们将如何引发焦点的改变呢？🤔
对此我们可以用 [`focus()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) 的方法。为了得到一个 DOM 元素，我们需要使用 reference。

```
export const AccordionSection = ({}) => {
  const labelRef = useRef();
  ...
  return (
    <>
      <div
        role="button"
        ...
        ref={labelRef}
      > 
```

我们还需要使用`useEffect`来实际调用 DOM 元素上的方法。问题是什么时候触发这个效果？我们需要在每次标签页的选择改变时，每次用户触发`ArrowDown`或`ArrowUp`等时触发它。所以我们需要将它存储在某个变量中，并在它每次改变时触发效果

```
export const AccordionSection = ({}) => {
  ...
  useEffect(() => {
    if (id === selected && labelRef.current) {
      labelRef.current.focus();
    }
  }, [id, selected]); 
```

每次选择发生变化，并且所选项目与当前项目相同时，将焦点放在它上面。

我们在哪里存储`selected`值？在根中，因为我们每个`Accordion`需要一个变量。我们如何通过它？通过上下文，就像我们通过`focusRef`一样。在`Accordion` :

```
const focusRef = useRef(null);
const [selected, setSelected] = useState(null);
const context = useMemo(
  () => ({
    focusRef,
    selected
  }),
  [selected]
);
...
case "ArrowDown":
  {
    const ids = React.Children.map(children, child => child.props.id);
    const index = ids.findIndex(x => x === focusRef.current);
    if (index >= ids.length - 1) {
      setSelected(ids[0]);
    } else {
      setSelected(ids[index + 1]);
    }
  } 
```

并且在`AccordionSection` :

```
const { focusRef, selected } = useAccordionContext(); 
```

唷！我们成功了。完全可访问的组件。不要忘记添加逻辑

*   主页
    *   当焦点在折叠标题上时，将焦点移动到第一个折叠标题。
*   结束
    *   当焦点在折叠标题上时，将焦点移动到最后一个折叠标题。

### 开发者体验

我们照顾了用户，让我们照顾开发者。我们非常依赖`id` s，以防开发者忘记提供它，他们将会得到非常细微的错误。[让我们检查它是否存在，否则发出警告](https://github.com/stereobooster/react-accessible-accordion/commit/45237580410f3e671e34347be43a306d75560dbf) :

```
AccordionSection.propTypes = {
  id: PropTypes.oneOfType([PropTypes.string, PropTypes.number]).isRequired,
  title: PropTypes.string.isRequired,
  expanded: PropTypes.bool,
  onToggle: PropTypes.func
}; 
```

同样我们假设`id` s 是唯一的，[让我们也检查一下](https://github.com/stereobooster/react-accessible-accordion/commit/8edb4dfcaaf38fa59e8f50534ec0711ca991cdb1) :

```
if (process.env.NODE_ENV === "development") {
  const uniqueIds = new Set();
  React.Children.forEach(children, child => {
    if (uniqueIds.has(child.props.id)) {
      console.warn(
        `AccordionSection id param should be unique, found the duplicate key: ${
          child.props.id
        }`
      );
    } else {
      uniqueIds.add(child.props.id);
    }
  });
} 
```

到目前为止，我们组件的 API 需要一个 onToggle 回调函数，这个回调函数被绑定到它的 id 或者对每个部分都是唯一的。这个 API 很难用。[让我们改为通过`id`来回调](https://github.com/stereobooster/react-accessible-accordion/commit/37131d743e145f6cad25f12779782f4377061f1b)。这样，开发者可以对所有部分使用一个存储和一个回调:

```
const [expanded, setExpanded] = useState({ "2": true });
const toggle = id => {
  setExpanded({
    ...expanded,
    [id]: !expanded[id]
  });
};
...
<AccordionSection
  title="section 1"
  id="1"
  expanded={expanded["1"]}
  onToggle={toggle}
>
...
<AccordionSection
  title="section 2"
  id="2"
  expanded={expanded["2"]}
  onToggle={toggle} 
```

我不喜欢我们需要为每个部分重复`expanded`和`onToggle`，相反，我们可以将它传递给`Accordion` :

```
<Accordion expanded={expanded} onToggle={onToggle}>
  <AccordionSection title="section 1" id="id1">
  ...
  </AccordionSection>
  <AccordionSection title="section 2" id="id2">
  ...
  </AccordionSection>
</Accordion> 
```

这样看起来更干净。还有一些缺点，比如你需要确保 state 中的 id 和`AccordionSection`中的 id 是相同的(否则某些部分可能无法工作)。

我们可以更进一步，[为默认行为](https://github.com/stereobooster/react-accessible-accordion/commit/04d9070aa797f7b1f180dce3f9ce558850c9fd72)提供一个定制的钩子。

```
import { useState } from "react";

export const useAccordionState = intialState => {
  const [expanded, setExpanded] = useState(intialState);
  const onToggle = id => {
    setExpanded({
      ...expanded,
      [id]: !expanded[id]
    });
  };
  return { expanded, onToggle };
}; 
```

所以最终的代码会是这样的:

```
function App() {
  const accordionProps = useAccordionState({ });
  return (
      <Accordion {...accordionProps}>
        <AccordionSection title="section 1" id="id1"> 
```

### 结论

没我想的那么可怕。WAI-ARIA 创作实践写得很好👏。我鼓励你使用适当的标记和键盘事件(每次使用`onClick`)。实现完全可访问的组件可能是一个有趣的学习练习。

在线演示是[这里](https://stereobooster.github.io/react-accessible-accordion/)。完整的源代码是[这里](https://github.com/stereobooster/react-accessible-accordion)。

## PS

如果我不会偷懒，如果这篇文章会引起我的兴趣，我会写如何用 Cypress 测试这个组件，以及如何修复我写完这篇文章后注意到的一个偷偷摸摸的错误。