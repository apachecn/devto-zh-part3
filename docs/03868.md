# 如何编写一个渐进的图像加载钩子

> 原文：<https://dev.to/selbekk/how-to-write-a-progressive-image-loading-hook-1jj2>

当我们优化 JavaScript 包中的每一千字节时，我们经常忘记以同样的方式优化我们的图像加载策略。在英雄图像加载之前，我们可能会看着一个空白的屏幕几秒钟，为你的白色文本提供背景。

这篇文章将向你展示如何编写一个钩子来处理你的渐进式图片加载！

> ## 什么是渐进式图像加载？
> 
> 渐进式图像加载——至少在这种情况下——首先加载图像的低分辨率版本，同时在背景中加载高分辨率版本。加载高分辨率版本后，图像将被交换。

我们将把我们的钩子命名为`useProgressiveImage`，并把一个`src`道具和一个`fallbackSrc`道具的对象传递给它。它将返回已经加载的最佳可用图像 src，或者如果两者都没有加载，则返回`null`。

```
function useProgressiveImage({ src, fallbackSrc }) {
  return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过创建一个新的`Image`实例，并设置它的`src`属性来预加载这样的图像。我们可以监听它的`onload`事件，并对其做出相应的反应。让我们写出一些样板代码:

```
function useProgressiveImage({ src, fallbackSrc }) {
  const mainImage = new Image();
  const fallbackImage = new Image();

  mainImage.onload = () => {}; // Still todo
  fallbackImage.onload = () => {}; // Still todo

  mainImage.src = src;
  fallbackImage.src = fallbackSrc;

  return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将在每次渲染时运行——这将触发大量无用的网络请求。相反，让我们把它放在一个`useEffect`里面，只在`src`或者`fallbackSrc`道具改变的时候运行它。

```
function useProgressiveImage({ src, fallbackSrc }) {
  React.useEffect(() => {
    const mainImage = new Image();
    const fallbackImage = new Image();

    mainImage.onload = () => {}; // Still todo
    fallbackImage.onload = () => {}; // Still todo

    mainImage.src = src;
    fallbackImage.src = fallbackSrc;
  }, [src, fallbackSrc]);

  return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要跟踪哪个图像已经被加载。我们不希望我们的后备图像“覆盖”我们的主图像，如果那将首先加载(由于缓存或只是巧合)，所以我们需要确保实现这一点。

我将用 [`React.useReducer`](https://reactjs.org/docs/hooks-reference.html#usereducer) 钩子跟踪这种状态，它接受一个*减速器函数*。这个 reducer 函数接受以前的状态(加载的源代码)，并根据我们调度的动作类型返回新的状态。

```
function reducer(currentSrc, action) {
  if (action.type === 'main image loaded') {
    return action.src;
  } 
  if (!currentSrc) {
    return action.src;
  }
  return currentSrc;
}

function useProgressiveImage({ src, fallbackSrc }) {
  const [currentSrc, dispatch] = React.useReducer(reducer, null);
  React.useEffect(() => {
    const mainImage = new Image();
    const fallbackImage = new Image();

    mainImage.onload = () => {
      dispatch({ type: 'main image loaded', src });
    };
    fallbackImage.onload = () => {
      dispatch({ type: 'fallback image loaded', src: fallbackSrc });
    };

    mainImage.src = src;
    fallbackImage.src = fallbackSrc;
  }, [src, fallbackSrc]);

  return currentSrc;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里实现了两种类型的操作——加载主图像时和加载后备图像时。我们将业务逻辑留给我们的缩减器，它决定何时更新源，何时保持不变。

> ### 动作类型是什么？
> 
> 如果你和我一样，你习惯于阅读`CONSTANT_CASE`或至少`camelCase`中的动作类型。然而，事实证明，你可以随心所欲地称呼它们。我在这里觉得好玩，只是写下了意图。因为为什么不呢？😅因为它们只是这个小钩子内部的，所以不管怎样，这真的没什么关系。

使用我们的钩子也很简单。

```
const HeroImage = props => {
  const src = useProgressiveImage({ 
    src: props.src,
    fallbackSrc: props.fallbackSrc 
  });
  if (!src) return null;
  return <img className="hero" alt={props.alt} src={src} />; }; 
```

Enter fullscreen mode Exit fullscreen mode

我已经创建了一个 CodeSandbox，如果你想的话，你可以看看并玩玩！

[https://codesandbox.io/embed/pkpqn1vo1x](https://codesandbox.io/embed/pkpqn1vo1x)

感谢阅读我的小迷你文章！我总是感谢分享或喜欢或评论，让我知道我是否应该继续这些来。

下次见！