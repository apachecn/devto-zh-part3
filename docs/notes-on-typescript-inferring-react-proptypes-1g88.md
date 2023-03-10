# 关于类型脚本的注释:推断反应属性类型

> 原文：<https://dev.to/busypeoples/notes-on-typescript-inferring-react-proptypes-1g88>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找如何在特定情况下利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### PropTypes 和推论

更多的时候，我们可能正在开发一个现有的 **React** 应用程序，其中一个团队已经决定引入**类型脚本**。这也意味着如果组件道具必须在某个时候定义，要么一次全部定义，要么逐步定义。在某些情况下，还可能存在现有的*道具类型*定义。我们可以在这些 prop-type 和 TypeScript 之间建立一座桥梁，而不是移除现有的 prop-type 定义。

在“关于 TypeScript 的注释”的这一部分中，我们将学习如何利用 TypeScript 来推断这些组件属性类型。

在我们开始之前，重要的是要注意到`PropTypes`类型包提供了`PropTypes.InferProps`，它能够推断现有道具类型定义的类型，如下:

```
const userPropTypes = {
  id: PropTypes.number.isRequired,
  name: PropTypes.string.isRequired,
  active: PropTypes.bool
};

types UserProps = PropTypes.InferProps<typeof userPropTypes>; 
```

Enter fullscreen mode Exit fullscreen mode

但是让我们退一步想一想。对 PropType 定义调用`typeof`会返回什么？

```
type TypeOfUserProps = typeof userPropTypes;

/*
  type TypeOfUserProps = {
    id: PropTypes.Validator<number>;
    name: PropTypes.Validator<string>;
    active: PropTypes.Requireable<boolean>;
  }
*/ 
```

Enter fullscreen mode Exit fullscreen mode

如果我们仔细看看`userPropTypes`，我们可以看到`id`和`name`是必需的，而`active`标志是可选的。当推断这些 prop-type 定义时，我们可以看到返回的类型定义将定义的类型包装到一个`Validator`或`Requireable`中，这取决于该类型是否被定义为必需的。

所以在内部`PropTypes.InferProps`区分了必需类型和可选类型，然后在这两个组之间创建了一个交集。我们不会太深入到实现细节，但是有必要弄清楚是否需要一个 prop-type。一个内部的`IsOptional`类型检查一个类型是否是`null | undefined`，然后决定这个类型是否是可选的。

接下来，让我们构建一个小示例，以验证我们是否可以将任何 prop-type 定义转换为实际的 TypeScript 类型。我们有一个包含现有属性类型和默认属性定义的`User`组件。

```
const userPropTypes = {
  id: PropTypes.number.isRequired,
  name: PropTypes.string.isRequired,
  active: PropTypes.bool
};

const userDefaultProps = {
  name: "Test"
};

const User = (props /*: PropTypes? */) => {
  return (
    <div>
      id: {props.id}
      name: {props.name}
      status: {props.active ? "active" : "inactive"}
    </div>
  );
};

User.defaultProps = userDefaultProps; 
```

Enter fullscreen mode Exit fullscreen mode

我们如何推断这些`userPropTypes`并为`User`组件提供缺失的类型？

我们的第一个方法是回到这篇文章的第一个例子:

```
const userPropTypes = {
  id: PropTypes.number.isRequired,
  name: PropTypes.string.isRequired,
  active: PropTypes.bool
};

types UserProps = PropTypes.InferProps<typeof userPropTypes>; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们验证这个例子，我们可以看到它已经如预期的那样工作了。

```
const User = (props: UserProps) => {
  // ...
}

<User id={1} /> // Works!
<User id="1!" /> // Error! Type 'string' is not assignable to type 'number'
<User /> // Error! Property 'id' is missing 
```

Enter fullscreen mode Exit fullscreen mode

但是我们不考虑任何默认道具，虽然这不应该是这样的，他们可能有不同的类型。这意味着我们需要扩展现有的机制，以包括默认道具的推断。我们需要做的事情不多:

```
type InferPropTypes<
  PropTypes,
  DefaultProps = {},
  Props = PropTypes.InferProps<PropTypes>
> = {
  [Key in keyof Props]: Key extends keyof DefaultProps
    ? Props[Key] | DefaultProps[Key]
    : Props[Key]
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们仔细看看上面的`InferPropTypes`类型，我们可以注意到我们接受了 prop-type 和默认 prop 类型，然后推断出所提供的 prop-type。接下来，我们映射这些推断的属性类型，并检查默认属性中是否也定义了一个键。`key extends keyof DefaultProps`部分帮助我们发现一个键是否确实存在于默认的 props 中。如果是这种情况，我们返回属性和默认属性值类型的联合，否则我们只返回属性值类型。

最后，我们可以使用新定义的`InferPropTypes`，如下例所示。

```
type UserProps = InferPropTypes<typeof userPropTypes, typeof userDefaultProps>; 
```

Enter fullscreen mode Exit fullscreen mode

再次运行我们的`User`组件，显示一切正常。

```
const User = (props: UserProps) => {
  // ...
}

<User id={1} /> // Works!
<User id="1!" /> // Error! Type 'string' is not assignable to type 'number'
<User /> // Error! Property 'id' is missing 
```

Enter fullscreen mode Exit fullscreen mode

当使用 TypeScript 时，我们应该对如何在 React 应用程序中推断现有的 prop-type 定义有一个基本的了解。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)