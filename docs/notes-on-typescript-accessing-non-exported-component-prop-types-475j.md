# 关于 TypeScript 的注释:访问非导出的组件属性类型

> 原文：<https://dev.to/busypeoples/notes-on-typescript-accessing-non-exported-component-prop-types-475j>

### 问题定义

有时我们需要访问一些组件道具类型，但是它们还没有被导出。还有一些情况下，我们宁愿避免导出任何额外的类型，以降低复杂性。为了在需要时访问这些类型，使用非导出类型的组件的开发人员可以做什么？使用 **TypeScript** 有不同的方法来实现这一点，所以让我们看看这个问题是如何解决的。

### 低水平接近

访问非导出类型的一种方法是编写自己的类型提取助手:

```
type componentProps<T> = T extends
  | React.ComponentType<infer Props>
  | React.Component<infer Props>
  ? Props
  : never; 
```

Enter fullscreen mode Exit fullscreen mode

让我们分解助手，以便更好地理解 TypeScript 如何推断 React 组件属性。

`React.ComponentType`是一个类或函数组件的别名，通过使用`infer P`，我们让 TypeScript 为`ComponentType`或`React.Component`本身推断任何`Props`。

然后我们或者返回推断出的`Props`或者`never`。
`never`类型用于表示任何从未出现的值，即抛出错误的函数。

通过使用`componentProps`助手，我们现在可以访问组件道具。

```
type User = {
  id: number;
  name: string;
  title: string;
  location: string;
};

const User = (props: User) => {
  return (
    <div>
      {/* ... */}
    </div>
  )
};

type ActionPropTypes = React.ComponentProps<typeof User>;

const UserWrapper = (props: ActionPropTypes) => {
  return (
    <div>
      <div>{props.name}</div>
      <User {...props} />
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 现代方法

有趣的是，我们现在也可以使用 React 类型的`React.ComponentProps`。
上面的例子可以改写为:

```
type ActionPropTypes = React.ComponentProps<typeof User>; 
```

Enter fullscreen mode Exit fullscreen mode

### 更多信息

[React-TypeScript-Cheatsheet:基本](https://github.com/sw-yx/react-typescript-cheatsheet#the-types-i-need-werent-exported)

[React-TypeScript-Cheatsheet:高级](https://github.com/sw-yx/react-typescript-cheatsheet/blob/master/ADVANCED.md#extracting-prop-types-of-a-component)

[stack overflow 上的回答](https://stackoverflow.com/questions/43230765/typescript-react-access-component-property-types/50136346#50136346)

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)