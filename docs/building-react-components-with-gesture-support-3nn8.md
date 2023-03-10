# 构建支持手势的 React 组件

> 原文：<https://dev.to/bmcmahen/building-react-components-with-gesture-support-3nn8>

手势支持在网络上并不常见，这也许并不奇怪。添加与原生手势相匹配的直观手势可能会令人望而生畏，而且通常不值得这么麻烦。但是拜托，现在是 2019 年，移动网络应该得到一些像样的手势支持！

这篇文章将带你创建一个基本的列表项目，允许你向左滑动来“喜欢”它——这种模式在原生应用程序中很常见，也是我在 Spotify 中经常使用的一种模式。这是我们最终版本的样子:

[https://codesandbox.io/embed/jnz1q8ymvy](https://codesandbox.io/embed/jnz1q8ymvy)

在开始之前，对[反应](http://reactjs.org)和[反应钩子](https://reactjs.org/docs/hooks-intro.html)有一个基本的了解是很有用的。准备好了吗？我们开始吧。

#### 基本组件布局

在添加任何手势之前，我们需要一个基本的组件框架&布局。让我们创建一个包含包装器 div 元素和两个子元素的组件:一个是我们的心形图标背景，另一个提供我们的滑动前景元素。

我们的组件将如下所示:

```
import React from 'react'
import { Heart } from 'react-feather'

function Slider({ children }) {
  return (
    <div className="list-item">
      <div className="background">
        <Heart />
      </div>
      <div className="sliding-pane">{children}</div>
    </div>
  )
} 
```

我们的风格相当简单。让我们为`list-item`元素使用预定义的宽度和高度，并给它一个相对位置。然后让我们给我们的`sliding-pane`分配一个绝对位置。我们完整的 css 将如下所示:

```
.list-item {
  position: relative;
  width: 300px;
  height: 75px;
  box-sizing: border-box;
  display: flex;
  align-items: center;
  text-align: center;
  border-radius: 0.5rem;
}

.sliding-pane {
  cursor: -webkit-grab;
  background-color: #121212;
  color: rgba(255, 255, 255, 0.9);
  position: absolute;
  height: 100%;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 0.5rem;
  font-size: 1.25em;
  font-weight: 600;
}

.sliding-pane:active {
  cursor: -webkit-grabbing;
}

.background {
  align-self: stretch;
  display: flex;
  flex: 1;
  justify-content: flex-end;
  align-items: center;
} 
```

好了，我们有了基本的框架。但是它什么都不做！该做些手势了。

#### 用 react-gesture-responder 添加拖拽手势。

为了添加手势，我们将使用我开发的名为 [react-gesture-responder](https://github.com/bmcmahen/react-gesture-responder) 的库。React 手势响应器是一个挂钩，它绑定到一个元素，并为该元素提供手势回调。这是一个相当低级的库，允许您构建(并在其间进行协商)复杂的手势。让我们将它添加到组件中:

```
import { useGestureResponder } from 'react-gesture-responder'

function Slider({ children }) {
  const { bind } = useGestureResponder({ // the view should claim the responder when touched onStartShouldSet: () => true, onMove: state => { // move callback }, onRelease: state => { // release callback }, })
  return (
    <div className="list-item">
      <div className="background">
        <Heart />
      </div>
      <div {...bind} className="sliding-pane"> {children}
      </div>
    </div>
  )
} 
```

`useGestureResponder`钩子返回一个`bind`对象并接受一些回调。这些回调帮助我们控制和回应我们的手势。

`bind`对象应该在你想要接受手势控制的元素上展开。到底什么是`bind`对象？是事件回调的集合，比如`onTouchStart`、`onTouchMove`等。，钩子用它来决定它的动作。

`onStartShouldSet`回调告诉视图给`claim`它自己的响应者。通常，除非你想完全禁用手势交互，否则你总是返回 true。(注意:在更复杂的情况下，包括竞争手势之间的协商，您可能需要返回 false。但那是另一个教程。)

当执行拖动时调用`onMove`函数，当拖动结束时调用`onRelease`。

让我们使用这些回调来更新滑块的位置。

```
function Slider({ children }) {
  const [x, setX] = useState(0)
  const { bind } = useGestureResponder({
    onStartShouldSet: () => true,
    onMove: state => { const [x] = state.delta setX(x) }, onRelease: state => { setX(0) }, })

  return (
    <div className="list-item">
      <div className="background">
        <Heart />
      </div>
      <div
        {...bind} style={{ transform: `translateX(${x}px)`, transition: `transform 0.2s ease`,
        }}
        className="sliding-pane"
      >
        {children}
      </div>
    </div>
  )
} 
```

我们的滑块现在可以响应我们的拖动动作，并在释放时自动回到原位。

但是这段代码有几个问题:

*   即使在拖动时也会执行过渡动画。如果它在拖动时能立即响应，但在释放时有动画效果就好了。
*   性能不是很好，因为组件状态的每次更新都会导致整个组件重新呈现。对于这样的小组件来说，这可能不是一个大问题，但是您可以想象随着时间的推移，对于更复杂的组件，性能会下降。

我对这些问题的首选解决方案是使用像 [react-spring](https://www.react-spring.io/) 这样的动画库。

#### 使用 react-spring 实现更好的控制和性能。

React-spring 公开了一个`useSpring`钩子，我们可以用它来代替我们的`x`状态。

让我们替换这个:

```
const [x, setX] = useState(0) 
```

用`useSpring`:

```
import { useSpring } from 'react-spring'

const [{ x }, set] = useSpring(() => {
  return { x: 0 }
}) 
```

我们现在通过调用`set`函数而不是`setX`来执行动画更新。

```
set({ x: 100 }) 
```

React-spring 还导出了一个`animated`组件。当使用弹簧值(如我们的`x`值)时，这些应该用来代替常规的`div`元素。让我们把这些放在一起:

```
import { useSpring, animated } from 'react-spring'

function Slider({ children }) {
  const [{ x }, set] = useSpring(() => { return { x: 0 } })
  const { bind } = useGestureResponder({
    onStartShouldSet: () => true,
    onMove: state => {
      const [x] = state.delta
      set({ x, immediate: true }) // the immediate flag bypasses the transition animation },
    onRelease: state => {
      set({ x: 0, immediate: false }) },
  })

  return (
    <div className="list-item">
      <div className="background">
        <Heart />
      </div>
      <animated.div {...bind} style={{ transform: x.interpolate(x => `translateX(${x}px)`), }} className="sliding-pane"
      >
        {children}
      </animated.div>
    </div>
  )
} 
```

这提供了与前一个示例相同的功能，但解决了前面提到的两个问题。性能得到提高。并且在调用我们的 set 函数时注意到了`immediate`选项？这将禁用弹簧过渡动画，该动画为我们提供了响应性拖动。我们在释放时禁用`immediate`来启用动画。

#### 确定用户何时“喜欢”列表项。

最后，我们需要确定用户何时向左做了足够远的手势来触发“喜欢”，并且我们需要在用户达到这个阈值时向用户提供视觉反馈。

```
function Slider({ children }) {
  const [isLiking, setIsLiking] = React.useState(false);
  const [{ x }, set] = useSpring(() => {
    return { x: 0 }
  })

  function shouldLike(x) { return x < -100 }
  const { bind } = useGestureResponder({
    onStartShouldSet: () => true,
    onMove: state => {
      const [x] = state.delta
      const like = shouldLike(x) if (like !== isLiking) { setIsLiking(like) }
      set({ x, immediate: true })
    },
    onRelease: state => {
      if (shouldLike(state.delta[0])) { cosole.log('User has liked!) }
      set({ x: 0, immediate: false })
    },
  })

  return (
    <div className="list-item">
      <div className="background">
        <Heart
          style={{
            color: 'white', fill: isLiking ? 'white' : 'transparent', transition: 'transform 0.3s ease' }}
         />
      </div>
      <animated.div
        {...bind}
        style={{
          transform: transform: x.interpolate(x => `translateX(${x}px)`),
        }}
        className="sliding-pane"
      >
        {children}
      </animated.div>
    </div>
  )
} 
```

我们的 move 回调检查用户是否已经对类似的东西做了足够远的手势，并相应地更新我们的`isLiked`状态。我们的心形图标会相应地改变外观。但是我们实际上不会触发 like 事件，直到手势结束，以便给用户取消手势的机会。

#### 进一步增强

这个手势可以在几个方面进行改进:

*   理想情况下，一旦手势进入“喜欢”模式，我们应该提供一些阻力。
*   心脏应该水平移动以响应手势。

你可以在最终版本中看到这两个:

[https://codesandbox.io/embed/jnz1q8ymvy](https://codesandbox.io/embed/jnz1q8ymvy)

#### 野外的例子

我已经创建了几个开源组件，可以对手势做出反应，这可能对学习有用。

*   [反应-手势-视图](https://github.com/bmcmahen/react-gesture-view)提供左右动画的可旋转视图。
*   桑丘-UI 提供了许多接受手势的组件，特别是[表单](https://sancho-ui.com/components/sheet/)。你可以在这里查看[源](https://github.com/bmcmahen/sancho/blob/master/src/Sheet.tsx)。
*   React-spring 有一些绝对杀手级的[例子](https://www.react-spring.io/docs/hooks/examples)，包括我们的例子所基于的[滑块](https://codesandbox.io/embed/zrj66y8714)。

你还有其他想分享的吗？我很想看看他们！

(这是一篇发表在我 benmcmahen.com 博客上的文章。点击这里[可以在线阅读。)](https://www.benmcmahen.com/building-react-components-with-gesture-support/)