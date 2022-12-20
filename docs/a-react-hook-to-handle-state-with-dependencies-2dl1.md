# 处理依赖状态的 React 挂钩

> 原文：<https://dev.to/jonrimmer/a-react-hook-to-handle-state-with-dependencies-2dl1>

为了试验 React 的新 hooks API，我一直在开发一款名为 [Fretfull](https://jonrimmer.github.io/fretfull) 的应用，它可以让你探索在吉他上弹奏和弦的不同方式。该应用的[源代码](https://github.com/jonrimmer/fretfull)在上下文和状态中使用钩子。

在构建应用程序时，我创建了一个我称之为“依赖状态”的自定义钩子。当您有一个包含一系列基于一个或多个依赖项计算的有效值的状态时，这很有用。如果依赖关系发生变化，状态的当前值可能不再有效，需要检查并可能重置。

更具体地说，考虑以下情况:

1.  一个应用程序从服务器接收产品类别列表。
2.  该应用程序在用户界面中显示类别列表。
3.  用户选择一个类别。
4.  该应用程序从服务器接收更新的类别列表。

此时，选择的类别可能有效，也可能无效，这取决于它是否仍然存在于更新的类别列表中。因此，应用程序需要智能地应用更新。如果类别不再存在，保持其选中状态将导致不一致和无效的应用程序状态。然而，如果类别*仍然有效，自动重置它将导致糟糕的用户体验。代码将需要检查更新的列表，如果没有找到选择，只重置选择*。**

 *让我们考虑如何使用 React 钩子实现这个场景。

```
function Categories({ apiData }: { apiData: CategoriesApiResult }) {
  const categories = useMemo(() => {
    return apiData.data.map(cat => cat.name);
  }, [apiData]);

  const [category, setCategory] = useState(categories[0]);

  return <OptionList
    options={categories}
    selected={category}
    onSelect={e => setCategory(e.value)}
  />;
} 
```

这里，Categories 组件通过映射来自作为道具接收的 API 调用的数据来创建类别选项列表。我们对计算进行了记忆，因此只有当 API 数据发生变化时才执行计算。我们还将选择的类别存储为一个状态，默认为列表中的第一个类别。

但是，这段代码有一个 bug:如果`categories`发生变化，`category`的值可能不再有效。我们需要检查它是否仍然有效，并有选择地重置它。我们可以这样做:

```
let [category, setCategory] = useState(null);

const categories = useMemo(() => {
  const result = apiData.data.map(cat => cat.name);

  if (!result.includes(category) {
    setCategory(category = result[0]);
  }
}, [apiData]); 
```

现在我们避免了这个错误，但是代价是混淆了我们的渲染逻辑。我们必须使`category`可重新赋值，在`categories`之前定义它，并在我们的`categories`记忆函数中包含一个副作用来重置`category`。

我们可以通过实现一个自定义钩子来使这种方法更加简洁和可重用，我们称之为`useDependentState` :

```
function useDependentState<S>(
  factory: (prevState?: S) => S,
  inputs: ReadonlyArray<any>,
): [S, Dispatch<SetStateAction<S>>] {
  let [state, setState] = useState<S>(factory());

  useMemo(() => {
    const newState = factory(state);

    if (newState !== state) {  
      setState(state = newState);
    }
  }, inputs);

  return [state, setState];
} 
```

这个钩子以一种通用的形式抓住了上述逻辑的本质。它定义了一个状态，并运行一个只有当依赖关系改变时才运行的记忆函数。这个记忆化的函数委托给一个我们必须提供的工厂函数，这个工厂函数负责生成初始值，或者在当前值不再有效时改变它。让我们看看如何在前面的例子中使用它:

```
const categories = useMemo(() => {
  return apiData.data.map(cat => cat.name);
}, [apiData]);

const [category, setCategory] = useDependentState(prevState => {
  return (prevState && categories.includes(prevState)) ?
    prevState
    :
    categories[0];
}, [categories]); 
```

我们的自定义钩子意味着我们可以将`category`保持为一个常量，保持原来的定义顺序，我们必须实现的唯一逻辑是检查`prevState`值是否仍然有效。

### 结论

希望这个定制钩子可以对任何面临类似问题的人有用，这些问题的有效性依赖于一些依赖关系。

我看到的这个定制钩子的唯一缺点是，当它改变时，它必须调用`setState`来更新状态值，这将导致第二次渲染。但我看不出有什么办法可以避免这种情况。我已经[提交了一个 React 特性建议](https://github.com/facebook/react/issues/14738)，目的是增强常规`setState`钩子的能力，提供依赖关系，以类似于这个定制钩子的方式重新初始化。如果实现的话，这将消除额外渲染的需要，因为`prevState`值不会“泄露”，因为检查逻辑将发生在`useState`调用中。*