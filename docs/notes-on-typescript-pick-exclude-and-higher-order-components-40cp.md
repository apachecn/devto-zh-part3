# 关于 TypeScript 的注释:挑选、排除和高阶组件

> 原文：<https://dev.to/busypeoples/notes-on-typescript-pick-exclude-and-higher-order-components-40cp>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找如何在特定情况下利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### 挑选并排除

这些笔记主要关注在 React 中输入高阶组件，但是理解`omit`和`Exclude`很有趣，因为我们将需要这两个函数来处理不同的高阶组件(hoc)实现。使用`Pick`,顾名思义，我们可以从提供的类型定义中选择特定的键。例如，我们可能正在使用对象展开，并希望选择特定的属性，然后展开其余的属性。让我们看看下面的例子，以便更好地理解:

```
const { name, ...rest } = props; 
```

Enter fullscreen mode Exit fullscreen mode

我们可能想在函数中使用 name 做一些事情，但是只传递其余的属性。

```
type ExtractName = {
  name: string
}

function removeName(props) {
  const {name, ...rest} = props;
  // do something with name...
  return rest:
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们给`removeName`函数添加类型。

```
function removeName<Props extends ExtractName>(
  props: Props
): Pick<Props, Exclude<keyof Props, keyof ExtractName>> {
  const { name, ...rest } = props;
  // do something with name...
  return rest;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事情，首先我们扩展了我们的泛型`Props`来包含 name 属性。
然后我们提取了`name`属性，并返回了剩余的属性。为了告诉 TypeScript 我们的泛型 rest 类型是如何构造的，我们需要移除所有 ExtractName 属性(在这个特定的例子中是 Name)。这就是`Pick<Props, Exclude<keyof Props, keyof ExtractName>>`所做的。为了更好地理解，让我们进一步分析一下。`Exclude`删除特定按键:

```
type User = {
  id: number;
  name: string;
  location: string;
  registeredAt: Date;
};

Exclude<User, "id" | "registeredAt"> // removes id and registeredAt 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用`Pick` :
达到同样的效果

```
Pick<User, "name" | "location"> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以重写我们上面的定义:

```
type Omit<T, K> = Pick<T, Exclude<keyof T, K>>;
type Diff<T, K> = Omit<T, keyof K>; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个`Diff`函数，我们可以重写我们的`removeName`函数:

```
function removeName<Props extends ExtractName>(
  props: Props
): Diff<Props, ExtractName> {
  const { name, ...rest } = props;
  // do something with name...
  return rest;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该对`Pick`和`Exclude`的功能有一个基本的了解，并且还添加了`Omit`和`Diff`，我们将在下一节键入 hocs 时使用它们。

### 高阶组件

为了更好地理解一些约定，我们将参考官方的 React 文档，然后输入不同的 hoc 变体。
有一个我们需要考虑的重要约定:*将不相关的道具传递给被包装的组件* ( [见文档](https://reactjs.org/docs/higher-order-components.html#convention-pass-unrelated-props-through-to-the-wrapped-component))。

我们的第一个例子基于文档中的一个例子，我们希望通过提供一个记录包装组件的组件来记录 props。

```
function withLogProps(WrappedComponent) {
  return class LogProps extends React.Component {
    componentWillReceiveProps(nextProps) {
      console.log("Currently available props: ", this.props);
    }
    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以利用`React.ComponentType`,它是一个 React 特定类型，允许我们将组件类或函数作为包装组件传入。由于我们没有扩展或缩小我们的`withLogProps`高阶组件中的任何属性，我们可以传递通用属性。

```
function withLogProps<Props>(WrappedComponent: React.ComponentType<Props>) {
  return class LogProps extends React.Component<Props> {
    componentWillReceiveProps() {
      console.log("Currently available props: ", this.props);
    }
    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们看看如何键入一个更高阶的组件，该组件期望附加的属性在错误发生时显示一条消息。

```
function withErrorMessage(WrappedComponent) {
  return function() {
    const { error, ...rest } = props;
    return (
      <React.Fragment>
        <WrappedComponent {...rest} />
        {error && <div>{error}</div>}
      </React.Fragment>
    );
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

`withErrorMessage`看起来类似于我们最初构建的例子。

```
 function withErrorMessage<Props>(WrappedComponent: React.ComponentType<Props>) {
  return function(props: Props & ErrorLogProps) {
    const { error, ...rest } = props as ErrorLogProps;
    return (
      <React.Fragment>
        <WrappedComponent {...rest as Props} />
        {error && <div>{error}</div>}
      </React.Fragment>
    );
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些有趣的方面，我们需要澄清。
我们的 hoc 通过期望一个`error`来扩展期望属性，除了从包装组件期望的所有属性之外，这可以通过将通用包装组件属性与所需的错误消息属性`Props & ErrorLogProps`相结合来实现。
另一个有趣的方面是，我们需要通过对我们被析构的道具进行类型化来明确定义哪些道具是`ErrorLogProps`:`const { error, ...rest } = props as ErrorLogProps`
TypeScript 在通过其余道具时仍然会报怨，所以我们也需要对其余道具进行类型化:`<WrappedComponent {...rest as Props} />`。这在将来可能会改变，但是对于 3.2 版的*来说，这是防止 TypeScript 抱怨所必需的。*

有些情况下，我们希望为包装的组件提供特定的功能和值，同时防止这些功能和值被提供的属性覆盖。
我们的下一个高阶组件应该会缩小 API 的范围。

让我们假设我们有一个期望
的`Input`组件

```
const Input = ({ value, onChange, className }) => (
  <input className={className} value={value} onChange={onChange} /> ); 
```

Enter fullscreen mode Exit fullscreen mode

高阶组件应该提供`value`和`onChange`属性。

```
function withOnChange(WrappedComponent) {
  return class OnChange extends React.Component {
    state = {
      value: "";
    };
    onChange = e => {
      const target = e.target;
      const value = target.checked ? target.checked : target.value;
      this.setState({ value });
    };
    render() {
      return (
        <WrappedComponent
          {...this.props}
          onChange={this.onChange}
          value={this.state.value}
        />
      );
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们首先定义需要的道具类型。

```
type InputProps = {
  name: string,
  type: string
};

type WithOnChangeProps = {
  onChange: (e: React.ChangeEvent<HTMLInputElement>) => void,
  value: string | boolean
}; 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们可以通过组合这些属性类型定义来定义我们的`Input`组件。

```
const Input = ({
  value,
  onChange,
  type,
  name
}: InputProps & WithOnChangeProps) => (
  <input type={type} name={name} value={value} onChange={onChange} /> ); 
```

Enter fullscreen mode Exit fullscreen mode

向`withOnChange`组件添加类型，我们可以应用到目前为止我们所学的一切。

```
type WithOnChangeState = {
  value: string | boolean;
};

function withOnChange<Props>(WrappedComponent: React.ComponentType<Props>) {
  return class OnChange extends React.Component<
    Diff<Props, WithOnChangeProps>,
    WithOnChangeState
  > {
    state = {
      value: ""
    };
    onChange = (event: React.ChangeEvent<HTMLInputElement>) => {
      const target = event.target;
      const value = target.type === "checkbox" ? target.checked : target.value;
      this.setState({ value });
    };
    render() {
      return (
        <WrappedComponent
          {...this.props as Props} // we need to be explicit here
          onChange={this.onChange}
          value={this.state.value}
        />
      );
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用我们之前定义的`Diff`类型，我们可以提取我们想要防止被覆盖的所有键。这使我们能够为我们的`Input`组件提供`onChange`和`value`属性。

```
const EnhancedInput = withOnChange(Input);

// JSX
<EnhancedInput type="text" name="name" />; 
```

Enter fullscreen mode Exit fullscreen mode

有些情况下，我们需要扩展 props，例如，我们希望开发人员能够使用`withOnChange`来提供初始值。我们可以通过提供一个`initialValue`属性来重写我们的组件。

```
type ExpandedOnChangeProps = {
  initialValue: string | boolean;
};

function withOnChange<Props>(WrappedComponent: React.ComponentType<Props>) {
  return class OnChange extends React.Component<
    Diff<Props, WithOnChangeProps> & ExpandedOnChangeProps,
    WithOnChangeState
  > {
    state = {
      value: this.props.initialValue
    };
    onChange = (event: React.ChangeEvent<HTMLInputElement>) => {
      const target = event.target;
      const value = target.type === "checkbox" ? target.checked : target.value;
      this.setState({ value });
    };
    render() {
      const { initialValue, ...props } = this.props as ExpandedOnChangeProps;
      return (
        <WrappedComponent
          {...props as Props} // we need to be explicit here
          onChange={this.onChange}
          value={this.state.value}
        />
      );
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有两件有趣的事情需要注意。我们通过定义`Diff<Props, WithOnChangeProps> & ExpandedOnChangeProps`扩展了`OnChange`类道具，另一个重要的是我们必须从传递给包装组件的道具中移除`initialValue`。我们在最初的例子中已经看到了这一点，方法是展开通用道具并移除`initialValue`:

`const { initialValue, ...props } = this.props as ExpandedOnChangeProps;`

另一种可能的情况是，当我们想要定义一个通用组件时，我们可能想要提供一个更高阶的组件，这需要一个包装的组件以及附加的配置或功能。让我们编写一个组件，该组件需要一个获取函数和一个组件，并返回一个组件，该组件根据获取的结果或者不显示任何内容，或者显示一个加载指示器、一条错误消息，或者在成功获取的情况下显示包装的组件。

```
function withFetch(fetchFn, WrappedComponent) {
  return class Fetch extends React.Component {
    state = {
      data: { type: "NotLoaded" }
    };
    componentDidMount() {
      this.setState({ data: { type: "Loading" } });
      fetchFn()
        .then(data =>
          this.setState({
            data: { type: "Success", data }
          })
        )
        .catch(error =>
          this.setState({
            data: { type: "Error", error }
          })
        );
    }
    render() {
      const { data } = this.state;
      switch (data.type) {
        case "NotLoaded":
          return <div />;
        case "Loading":
          return <div>Loading...</div>;
        case "Error":
          return <div>{data.error}</div>;
        case "Success":
          return <WrappedComponent {...this.props} data={data.data} />;
      }
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做一些工作来防止 TypeScript 抱怨。
我们可以做的第一件事是定义实际的组件状态:

```
type RemoteData<Error, Data> =
  | { type: "NotLoaded" }
  | { type: "Loading" }
  | { type: "Error", error: Error }
  | { type: "Success", data: Data };

type FetchState<Error, Data> = {
  data: RemoteData<Error, Data>
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以定义我们的`withFetch`组件在调用提供的函数时应该期望的承诺结果类型，这样我们可以保证返回的承诺结果类型与我们包装的组件中期望的数据属性相匹配。

```
function withFetch<FetchResultType, Props extends { data: FetchResultType }>(
  fetchFn: () => Promise<FetchResultType>,
  WrappedComponent: React.ComponentType<Props>
) {
  return class Fetch extends React.Component<
    Omit<Props, "data">,
    FetchState<string, FetchResultType>
  > {
    state: FetchState<string, FetchResultType> = {
      data: { type: "NotLoaded" }
    };
    componentDidMount() {
      this.setState({ data: { type: "Loading" } });
      fetchFn()
        .then(data =>
          this.setState({
            data: { type: "Success", data }
          })
        )
        .catch(error =>
          this.setState({
            data: { type: "Error", error }
          })
        );
    }
    render() {
      const { data } = this.state;
      switch (data.type) {
        case "NotLoaded":
          return <div />;
        case "Loading":
          return <div>Loading...</div>;
        case "Error":
          return <div>{data.error}</div>;
        case "Success":
          return <WrappedComponent {...this.props as Props} data={data.data} />;
      }
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以写更多的例子，但是作为主题的介绍，这些例子应该是进一步研究主题的基础。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)