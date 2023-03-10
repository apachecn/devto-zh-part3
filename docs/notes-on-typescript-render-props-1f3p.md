# 关于 TypeScript 的注释:渲染道具

> 原文：<https://dev.to/busypeoples/notes-on-typescript-render-props-1f3p>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找如何在特定情况下利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 关于打字稿的更多说明

[关于打字稿的注释:选择、排除和高阶组件](https://dev.to/busypeoples/notes-on-typescript-pick-exclude-and-higher-order-components-40cp)

### 渲染道具

`Render Props`是用附加功能增强 React 组件的流行模式。它可以与更高阶的组件互换，选择渲染道具模式还是更高阶的组件是一个风格问题，取决于特定的用例。

为了更好地理解这个主题，让我们构建一个使用渲染道具的组件。在前面的[“关于类型脚本的注释”](https://dev.to/busypeoples/notes-on-typescript-pick-exclude-and-higher-order-components-40cp)中，我们构建了一个组件，它提供了一个具有`onChange`和`value`属性的`Input`组件。我们可以将这个高阶组件重写为渲染道具实现。

```
class OnChange extends React.Component {
  state = {
    value: this.props.initialValue
  };
  onChange = event => {
    const target = event.target;
    const value = target.type === "checkbox" ? target.checked : target.value;
    this.setState({ value });
  };
  render() {
    return this.props.render({
      value: this.state.value,
      onChange: this.onChange
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 React 应用程序中使用重构的`OnChange`:

```
<OnChange
  initialValue="hello"
  render={onChangeProps => <Input {...props} {...onChangeProps} />} /> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以重用大多数以前定义的类型。

```
type InputProps = {
  name: string,
  type: string
};

type OnChangeProps = {
  onChange: (e: React.ChangeEvent<HTMLInputElement>) => void,
  value: string
};

type ExpandedOnChangeProps = {
  initialValue: string | boolean,
  render: (onChangeProps: onChangeProps) => JSX.Element
};

type OnChangeState = {
  value: string
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们的`Input`组件没有改变，我们也可以在这个例子中重用那个组件。

```
const Input = ({ value, onChange, type, name }: InputProps & OnChangeProps) => (
  <input type={type} name={name} value={value} onChange={onChange} /> ); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经万事俱备，让我们看看如何键入`OnChange`。
有趣的是，我们不需要做太多的事情来键入`onChange`组件。

```
class OnChange extends React.Component<ExpandedOnChangeProps, OnChangeState> {
  state = {
    value: this.props.initialValue
  };
  onChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const target = event.target;
    this.setState({ value: target.value });
  };
  render() {
    return this.props.render({
      value: this.state.value,
      onChange: this.onChange
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

与高阶组件实现相比，我们只需要为`OnChange`定义属性和状态，在这种情况下，使用已经存在的`ExpandedOnChangeProps`和`OnChangeState`，并如下定义类组件:

`class OnChange extends React.Component<ExpandedOnChangeProps, OnChangeState>`。

我们可能希望在应用程序中的多个地方重用该功能。通过定义一个新的组件，即`ControlledInput`，我们可以组合我们的`OnChange`和`Input`，让开发者定义`initialValue`以及`name`和`type`。

```
type ControlledInputProps = InputProps & { initialValue: string };

const ControlledInput = ({ initialValue, ...props }: ControlledInputProps) => (
  <OnChange
    initialValue={initialValue}
    render={onChangeProps => <Input {...props} {...onChangeProps} />}
  />
); 
```

Enter fullscreen mode Exit fullscreen mode

现在`ControlledInput`可以在另一个组件中使用，当`name`、`type`或`initialValue`缺失时，TypeScript 会报错。

```
<ControlledInput initialValue="testdrive" type="text" name="test" /> 
```

Enter fullscreen mode Exit fullscreen mode

### 高级

我们可能希望启用通过 render 或 children prop 传递渲染回调。这要求我们对我们的`OnChange`组件做一些改变。如果我们回忆一下，我们的`ExpandedOnChangeProps`有如下形状:

```
type ExpandedOnChangeProps = {
  initialValue: string | boolean,
  render: (onChangeProps: onChangeProps) => JSX.Element
}; 
```

Enter fullscreen mode Exit fullscreen mode

将回调作为子属性传递的一种方法是将定义改为:

```
type ExpandedOnChangeProps = {
  initialValue: string,
  render?: (onChangeProps: onChangeProps) => JSX.Element,
  children?: (onChangeProps: onChangeProps) => JSX.Element
}; 
```

Enter fullscreen mode Exit fullscreen mode

但是上面的定义有问题，因为现在两个变量都不能提供或者都不能提供。我们实际上想要的是确保定义了这些属性中的一个，这有可能定义一个显式的`RenderProp`类型:

```
type RenderProp =
  | { render: (onChangeProps: OnChangeProps) => JSX.Element }
  | { children: (onChangeProps: OnChangeProps) => JSX.Element }; 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们可以将我们的`ExpandedOnChangeProps`定义重写为:

```
type ExpandedOnChangeProps = {
  initialValue: string
} & RenderProp; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要更新渲染函数来处理两种可能的情况:

```
class OnChange extends React.Component<ExpandedOnChangeProps, OnChangeState> {
  state = {
    value: this.props.initialValue
  };
  onChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const target = event.target;
    this.setState({ value: target.value });
  };
  render() {
    if ("render" in this.props) {
      return this.props.render({
        value: this.state.value,
        onChange: this.onChange
      });
    }

    if ("children" in this.props) {
      return this.props.children({
        value: this.state.value,
        onChange: this.onChange
      });
    }

    throw new Error("A children or render prop has to be defined");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`"render" in this.props`，我们可以检查`render`是否被定义，或者检查一个`children`属性是否被定义。如果两个属性都没有定义，我们抛出一个错误。
我们之前定义的`ControlledInput`可以改写为:

```
const ControlledInput = ({
  initialValue,
  ...props
}: InputProps & { initialValue: string }) => (
  <OnChange initialValue={initialValue}>
    {onChangeProps => <Input {...props} {...onChangeProps} />}
  </OnChange> ); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在应该对渲染道具如何用 TypeScript 打字有了基本的了解。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)