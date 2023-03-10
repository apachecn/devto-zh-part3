# 创建灵活的组件

> 原文：<https://dev.to/selbekk/creating-flexible-components-p7k>

创建可重用的组件很难。弄清楚 API 是一件痛苦的事，决定特性并不容易，只是粗制滥造代码也不适合胆小的人。

这篇博客文章将带你了解我们如何创建一个可重用的输入组，这使得组合表单域变得容易。我会解释什么是输入组，我们希望它做什么，并逐步实现它。当我们完成时，您将能够创建您自己的组件——并且有希望对以后编写组件感到更加安全。

请注意，我们正在创建的组件只是一个示例。您可能不需要在您的应用程序中使用这个特定的组件，但是您将来肯定会使用其中的一些技术。

## 那么什么是输入组呢？

我不认为你会在任何地方找到教科书的定义，但对我来说，输入组是一个组件，它显示某种形式的输入，创建一个相关的标签，显示一个错误消息(如果有的话)，并处理尽可能多的可访问性问题，而用户不必考虑它。基本上表单输入需要的所有东西，除了它自己。

> 一个输入组是一个组件
> <cite>克里斯托夫·塞尔贝克</cite>

这就是我们今天要创造的——一步一步，一点一点。所以让我们开始吧！

## 第一步:显示某种表单输入

首先，让我们创建一个名为`InputGroup`的组件，它呈现我们传递给它的任何子组件:

```
function InputGroup(props) {
  return (
    <div>{props.children}</div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以将任何形式的输入传递给我们的组件:

```
<InputGroup>
  <input />
</InputGroup> 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这并不难，是吧？还和我在一起吗？

## 第二步:创建相关标签！

我们希望能够向组件传递一个标签文本。让我们创建一个`label`道具:

```
function InputGroup(props) {
  return (
    <div>
      <label>{props.label}</label>
      {props.children}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们要确保标签以某种方式附加到输入上。我们为此使用了`htmlFor`属性。我们接受 ID 作为道具，然后将它应用于标签:

```
function InputGroup(props) {
  return (
    <div>
      <label htmlFor={props.id}>{props.label}</label>
      {props.children}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这有点烦人——我们需要将 ID 传递给我们的`InputGroup`和我们的输入。让我们将其自动应用到输入:

```
function InputGroup(props) {
  return (
    <div>
      <label htmlFor={props.id}>
        {props.label}
      </label>
      {React.Children.map(
        props.children, 
        child => React.cloneElement(child, { id: props.id })
      )}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

什么，`React.Children`？`React.cloneElement`？这些 API 很少使用，但是它们非常强大。基本上发生的事情是:为每个传入这个组件的子组件创建一个副本，并添加一个额外的`id`道具。

> 让我们把孩子映射成一个数组。详见[文档](https://reactjs.org/docs/react-api.html#reactchildren)。
> 
> 创建一个 React 元素的副本，并让我们用自己的版本覆盖任何传入的属性。详见[文档](https://reactjs.org/docs/react-api.html#cloneelement)。

有了这个，我们就可以一次传入我们的 ID，并为表单标签提供一个可访问的标签。

```
<InputGroup id="first-name" label="First name">
  <input />
</InputGroup> 
```

Enter fullscreen mode Exit fullscreen mode

### 奖励:完全跳过 ID

很有可能，你并不真正关心 ID。我们在这里需要一个标签的事实是标签和输入工作方式的实现细节。如果我们可以完全跳过传递它，不是很好吗？

事实证明，这是很有可能的。我们可以使用一个随机的字符串生成器来创建一个 ID，如果没有提供 ID，就用它来创建一个 ID。

```
import uuid from 'uuid/v4';

function InputGroup(props) {
  const id = React.useMemo(() => props.id || 'input-'+ uuid(), [props.id])
  return (
    <div>
      <label htmlFor={id}>
        {props.label}
      </label>
      {React.Children.map(
        props.children, 
        child => React.cloneElement(child, { id })
      )}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用`React.useMemo`钩子来避免在每次渲染时创建新的 id。我们将`props.id`传递给它的依赖数组，确保我们只在 id 属性由于某种原因改变时才重新创建 ID。

还要注意，如果出于某种原因需要，我们让消费者设置自己的 ID。这是组件 API 设计的一个重要原则:

> 允许消费者覆盖任何自动生成的值！

## 第三步:添加错误处理

大多数表单都实现了某种验证。有很多很棒的验证库(我甚至创建了自己的-[calidization](https://github.com/selbekk/calidation)！)，留给读者一个选择。我们将要添加的是一种在我们的`InputGroup`组件中显示验证错误的方法。

我们从添加一个`error`道具开始，并将其渲染到我们的`children` :
下面

```
function InputGroup(props) {
  const id = React.useMemo(() => props.id || 'input-'+ uuid(), [props.id])
  return (
    <div>
      <label htmlFor={id}>
        {props.label}
      </label>
      {React.Children.map(
        props.children, 
        child => React.cloneElement(child, { id })
      )}
      {props.error && (
        <div>{props.error}</div>
      )}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，但是让我们把游戏向前推进一点点。为了帮助屏幕阅读器和其他辅助技术，我们应该将输入字段标记为无效。我们可以通过在表单输入:
上设置`aria-invalid`属性来做到这一点

```
function InputGroup(props) {
  const id = React.useMemo(() => props.id || 'input-'+ uuid(), [props.id])
  const isInvalid = props['aria-invalid'] || String(!!props.error);
  return (
    <div>
      <label htmlFor={id}>
        {props.label}
      </label>
      {React.Children.map(
        props.children, 
        child => React.cloneElement(child, { id, 'aria-invalid': isInvalid })
      )}
      {props.error && (
        <div>{props.error}</div>
      )}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们将表单输入的`aria-invalid`属性设置为“true ”,如果传递了一个非 false 的`error`属性，如果错误为空或未定义，则设置为“false”。注意，我们将这个属性强制转换成一个字符串，因为这是 DOM 所期望的。

最后，我们还让消费者自己传入`aria-invalid`来覆盖这个值。

## 第四步:让它灵活🧘‍♂️

到目前为止，我们已经创建了一个相当可靠的输入组组件。它处理可访问性问题，显示输入标签和错误，让我们传递任何想要的输入。尽管如此，还有工作要做。

由于我们不知道人们将如何使用我们的组件，我们可能希望让人们覆盖一些东西。在某些情况下，`label`和`error`元素可能需要被切换出来，或者以稍微不同的方式呈现。似乎是我们能做的事！

```
function InputGroup(props) {
  const id = React.useMemo(() => props.id || 'input-'+ uuid(), [props.id])
  const isInvalid = props['aria-invalid'] || String(!!props.error);

  const label = typeof props.label === 'string' ? 
    <label htmlFor={id}>{props.label}</label> :
    React.cloneElement(props.label, { htmlFor: id });

  const error = typeof props.error === 'string' ?
    <div>{props.error}</div> :
    props.error;

  return (
    <div>
      {label}
      {React.Children.map(
        props.children, 
        child => React.cloneElement(child, { id, 'aria-invalid': isInvalid })
      )}
      {props.error && error}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在上面实现的 API 允许我们将一个字符串或一些 JSX 传递给我们的标签和错误属性。如果我们传递一个字符串值，默认的用户界面被渲染，但是如果我们传递一些 JSX，我们让消费者决定它看起来怎么样。用法可能是这样的:

```
<InputGroup
  label={<MyCustomLabelComponent>First name</MyCustomLabelComponent>}
  error="some error occurred"
>
  <input />
</InputGroup> 
```

Enter fullscreen mode Exit fullscreen mode

允许这种定制使您的组件对于大多数用例足够灵活，同时保持一个小的可预测的 API。

## 第五步:让它更加灵活

这个组件还有最后一个假设，我很想去掉。假设我们只传入一个孩子，这个孩子是一个表单输入。我们可能想要几个输入，或者输入周围的一些文本，或者只是一些需要呈现的自定义 UI。让我们解决这个问题。

```
function InputGroup(props) {
  const id = React.useMemo(() => props.id || 'input-'+ uuid(), [props.id])
  const isInvalid = props['aria-invalid'] || String(!!props.error);

  const label = typeof props.label === 'string' ? 
    <label htmlFor={id}>{props.label}</label> :
    React.cloneElement(props.label, { htmlFor: id });

  const error = typeof props.error === 'string' ?
    <div>{props.error}</div> :
    props.error;

  return (
    <div>
      {label}
      {props.children({ id, 'aria-invalid': isInvalid })}
      {props.error && error}
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们现在用我们的增强道具调用`props.children`。这被称为“渲染道具”模式，你可以在[文档](https://reactjs.org/docs/render-props.html)中读到更多。这导致了这样的用法:

```
<InputGroup label="Amount">
  {inputProps => (
    <div>
      $ <input {...inputProps} />
    </div>
  )}
</InputGroup> 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以完全控制 UI 的呈现方式。我们将为表单输入提供的道具作为子函数的参数，并让消费者将它们放在正确的元素上。

不过这种方法也有缺点——语法看起来很糟糕，消费者需要手动传播道具。考虑这对于你的项目是否是一个好的模式。

## 总结

为 React 组件创建一个好的、可靠的 API 不是一件容易的事情。组件应该可以被不同的消费者重用，默认情况下所有的东西都应该是可访问的，组件做的任何事情都应该是可重写的。

这篇文章经历了几种“到达那里”的方法。这肯定会使事情变得有点复杂，但是它让你可以创建非常灵活的 ui，而不必每周都给组件添加新的道具。

如果你想玩这个 API，你可以在这个 CodeSandbox 中这样做:

[https://codesandbox.io/embed/23nqr6kr6y](https://codesandbox.io/embed/23nqr6kr6y)

感谢阅读！