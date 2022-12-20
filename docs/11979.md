# React:使用嵌套选择框的 useReducer 挂钩演示

> 原文：<https://dev.to/email2vimalraj/usereducer-hooks-demo-with-nested-select-boxes-3ad1>

今天我遇到了一个情况，我必须选择嵌套的选择框，也就是说，基于在第一个选择框中选择的值，这些值应该在第二个选择框中填充。

这是我第一次尝试 react 中的 hooks，想尝试一下。对于这个演示，我使用的是[反应-选择](https://react-select.com/home)组件。

要求是第一个选择框将有国家列表。在我选择了一个国家之后，第二个选择框应该显示所选国家的州列表，并且国家选择框应该被禁用。让我们建造它！

假设我有如下数据:

```
const data = {
  countries: [
    {
      value: 'INDIA',
      label: 'India',
      states: [
        { value: 'TAMIL NADU', label: 'Tamil Nadu' },
        { value: 'KERALA', label: 'Kerala' },
        { value: 'ANDHRA PRADESH', label: 'Andhra Pradesh' }
      ]
    },
    {
      value: 'US',
      label: 'USA',
      states: [
        { value: 'CA', label: 'California' },
        { value: 'NY', label: 'New York' }
      ]
    }
  ]
} 
```

我正在设置组件的初始状态:

```
const initialState = {
  disableCountry: false,
  disableState: true,
  loadingState: false,
  statesToBeLoaded: []
} 
```

然后，我创建 reducer 函数，其中包含从组件中触发的一系列动作。当我选择一个国家或地区时，以及当我单击清除按钮来清除我的选择时，会发生以下操作:

```
const POPULATE_STATE = 'populateState'
const CLEAR = 'clear'
function reducer(state, action) {
  switch (action.type) {
    case POPULATE_STATE:
      return {
        ...state,
        disableCountry: true,
        disableState: false,
        loadingState: true,
        statesToBeLoaded: data.countries.find(
          country => country.value === action.country
        ).states
      }
    case CLEAR:
    default:
      return initialState
  }
} 
```

现在我将构建我的功能组件，它呈现两个选择框:

```
function App() {
  const [state, dispatch] = useReducer(reducer, initialState)

  return (
    <div className="App">
      <h1>useReducer demo</h1> 
      <Select
        isDisabled={state.disableCountry}
        isLoading={state.loadingState}
        isClearable
        isSearchable
        placeholder="Select Country..."
        name="country"
        options={data.countries}
        onChange={e => dispatch({ type: POPULATE_STATE, country: e.value })}
      /> 
      <br />

      {!state.disableState && (
        <>
          <Select
            isDisabled={state.disableState}
            isLoading={false}
            isClearable
            isSearchable
            placeholder="Select State..."
            name="state"
            options={state.statesToBeLoaded}
          /> 
          <br />

          <button type="button" onClick={() => dispatch({ type: CLEAR })}>
            Clear
          </button>
        </>
      )}
    </div>
  )
} 
```

这里的`const [state, dispatch] = useReducer(reducer, initialState)`我使用 react 中的`useReducer`函数，它返回当前状态和触发动作的调度方法。

因此，我在第一个选择框中的`onChange`事件期间调度`POPULATE_STATE`来加载所选国家的状态，并且即使当用户点击`Clear`按钮来重置功能组件的状态时，我也调度`CLEAR`。

这工作完美！我们可以扩展这个例子，在我们的 dispatch 事件期间从外部 API 加载数据，并利用`loading`状态来显示`progress`指示器。

### 代码沙盒演示

[![Edit useReducer demo](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/o9461q0z59?fontsize=14)

最初发布在我的博客上:[vimalselvam.com](https://wp.me/p73pJa-bT)

如果你喜欢这篇文章，请点击❤️按钮并分享。