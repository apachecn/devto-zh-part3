# 像钩子一样对颤振实施作出反应

> 原文：<https://dev.to/alfredosalzillo/react-like-hooks-for-flutter-implementation-4cjm>

### 为什么？

对于工作，我使用 react，我讨厌编写类组件，所以当钩子被宣布时，我会立刻发疯，因为它们晦涩的魔力允许功能组件使用状态和副作用。

当钩子被允许用于产品代码时，在 React 中，我将能够只编写功能组件，避免冗长的类。另外，我可以在组件之间共享有状态逻辑，而不需要使用 orribles mixins、redux 或其他外部库和不熟悉的模式。

现在我在个人项目中使用 Flutter，所以我开始实现类似 react hooks 的东西来避免编写类。

最初当我开始使用 flutter 时，我试图避免只使用函数的类

```
final StatefulWidgetBuilder HelloWorld = (context, setState) {
 ...,
} 
```

Enter fullscreen mode Exit fullscreen mode

并将它们用作`StatefulBuilder`中的构建函数。

但是我不能处理事情，例如流订阅。本质上我无法消除副作用。

所以我开始用另一种方式思考，我创建了一个`HookBuilder`，一个`StatefulBuilder`实现。

在构建之前，`StatefulBuilder`，初始化一个 HookContext，并在释放时，释放所有注册的`Hook`。

我定义了一个`use`函数，如何使用一个`HookTransformer`函数，将结果存储在钩子库中并返回值。
用`use`我定义了`useMemo`、`useCallback`、`useState`，后来还有`useEffect`。

所以现在我可以在代码中写一些类似 React functional hooked 组件的东西。

```
final StatefulWidgetBuilder HelloWorld = (context) {
 final name = useState('');
 final nameInfo = useAsync(getUserInfo, null, [name]);
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

并在我的`StatefulBuilder`中使用它们作为构建函数。

```
...
return StatefulBuilder(builder: HelloWorld); 
```

Enter fullscreen mode Exit fullscreen mode

我现在已经用了一周了，所以我决定拆分代码，把我的钩子实现作为 flutter 包发布在 github [flhooks](https://github.com/alfredosalzillo/flhooks) 上。

看一看它。