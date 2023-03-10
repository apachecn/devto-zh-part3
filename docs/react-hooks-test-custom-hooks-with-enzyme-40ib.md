# 反应钩子:用酶测试定制钩子

> 原文：<https://dev.to/flexdinesh/react-hooks-test-custom-hooks-with-enzyme-40ib>

**TL；DR** -将你的定制钩子包装在一个组件中，并对其进行浅层渲染以测试实现细节。

# 你将学到什么

*   反应测试策略
    *   **用户可观察的行为**
    *   **实施细节**
*   用酶测试定制挂钩

## 测试策略

大体上有两种策略来测试我们的 React 代码库。

1.  测试用户可观察的行为
2.  测试实施细节

### 测试用户可观察的行为

测试用户可观察的行为意味着针对测试的组件编写测试

*   组件的呈现方式
*   当用户与 DOM 交互时如何重新呈现组件
*   道具/状态如何控制渲染的内容

考虑以下组件- `Greet`

```
function Greet({ user = 'User' }) {
  const [name, setName] = React.useState(user);

  return <div onClick={() => setName('Pinocchio')}>Hello, {name}!</div>;
} 
```

Enter fullscreen mode Exit fullscreen mode

在`Greet`中测试用户可观察到的行为意味着

*   测试`Greet`渲染时是否没有崩溃
*   测试用户属性未通过时`Hello, User!`是否被渲染
*   测试当`Bruce`作为值传递给`user`属性时`Hello, Bruce!`是否被渲染
*   测试当用户点击元素时文本是否改变为`Hello, Pinocchio!`

### 测试实现细节

测试实现细节意味着根据测试的状态逻辑编写测试

*   如何用默认值/正确值初始化状态
*   调用处理程序时状态如何变化

考虑同一个组件- `Greet`

```
function Greet({ user = 'User' }) {
  const [name, setName] = React.useState(user);

  return <div onClick={() => setName('Pinocchio')}>Hello, {name}!</div>;
} 
```

Enter fullscreen mode Exit fullscreen mode

测试`Greet`中的实现细节意味着

*   当用户属性未传递给`Greet`时，测试`name`是否设置为默认值`User`
*   当用户属性被传递到`Greet`时，测试`name`是否被设置为属性值
*   测试调用`setName`时`name`是否更新

## 用酶测试定制挂钩

*注意:请确保你的 React 版本是`^16.8.5`。在以前的版本中，Hooks 不会使用 enzyme shallow render 重新渲染组件，React 团队在这个版本中修复了这个问题。如果你的 React 版本低于这个版本，你可能需要在每次修改后使用 enzyme mount 和【wrapper 来测试重新渲染。*

当您针对包含表示性(UI)逻辑和 DOM 呈现元素的组件编写测试时，测试实现细节可能看起来没有必要，甚至可能被认为是一种不好的做法。但是**定制钩子**只包含**状态逻辑**，我们必须彻底测试实现细节，这样我们才能确切地知道我们的定制钩子在组件中的行为。

让我们编写一个自定义挂钩来更新和验证表单字段。

```
/* useFormField.js */

import React from 'react';

function useFormField(initialVal = '') {
  const [val, setVal] = React.useState(initialVal);
  const [isValid, setValid] = React.useState(true);

  function onChange(e) {
    setVal(e.target.value);

    if (!e.target.value) {
      setValid(false);
    } else if (!isValid) setValid(true);
  }

  return [val, onChange, isValid];
}

export default useFormField; 
```

Enter fullscreen mode Exit fullscreen mode

尽管自定义钩子在抽象出代码中可重用的逻辑方面很棒，但它们也有一个限制。尽管它们只是 JavaScript 函数，但它们只能在 React 组件内部工作。你不能仅仅调用它们，并根据钩子返回的内容编写测试。您必须将它们包装在 React 组件中，并测试它返回的值。

*   定制钩子不能像 JavaScript 函数一样被测试
*   自定义挂钩应该包装在 React 组件中，以测试其行为

由于钩子的可组合性，我们可以将钩子作为一个道具传递给一个组件，一切都将按照它应该的方式工作。我们可以编写一个包装器组件来呈现和测试我们的钩子。

```
/* useFormField.test.js */

function HookWrapper(props) {
  const hook = props.hook ? props.hook() : undefined;
  return <div hook={hook} />;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像访问 JavaScript 对象一样访问钩子，并测试它的行为。

```
/* useFormField.test.js */

import React from 'react';
import { shallow } from 'enzyme';
import useFormField from './useFormField';

function HookWrapper(props) {
  const hook = props.hook ? props.hook() : undefined;
  return <div hook={hook} />;
}

it('should set init value', () => {
  let wrapper = shallow(<HookWrapper hook={() => useFormField('')} />);

  let { hook } = wrapper.find('div').props();
  let [val, onChange, isValid] = hook;
  expect(val).toEqual('');

  wrapper = shallow(<HookWrapper hook={() => useFormField('marco')} />);

  // destructuring objects - {} should be inside brackets - () to avoid syntax error
  ({ hook } = wrapper.find('div').props());
  [val, onChange, isValid] = hook;
  expect(val).toEqual('marco');
}); 
```

Enter fullscreen mode Exit fullscreen mode

定制钩子的完整测试套件如下所示。

```
/* useFormField.test.js */

import React from 'react';
import { shallow } from 'enzyme';
import useFormField from './useFormField';

function HookWrapper(props) {
  const hook = props.hook ? props.hook() : undefined;
  return <div hook={hook} />;
}

describe('useFormField', () => {
  it('should render', () => {
    let wrapper = shallow(<HookWrapper />);

    expect(wrapper.exists()).toBeTruthy();
  });

  it('should set init value', () => {
    let wrapper = shallow(<HookWrapper hook={() => useFormField('')} />);

    let { hook } = wrapper.find('div').props();
    let [val, onChange, isValid] = hook;
    expect(val).toEqual('');

    wrapper = shallow(<HookWrapper hook={() => useFormField('marco')} />);

    // destructuring objects - {} should be inside brackets - () to avoid syntax error
    ({ hook } = wrapper.find('div').props());
    [val, onChange, isValid] = hook;
    expect(val).toEqual('marco');
  });

  it('should set the right val value', () => {
    let wrapper = shallow(<HookWrapper hook={() => useFormField('marco')} />);

    let { hook } = wrapper.find('div').props();
    let [val, onChange, isValid] = hook;
    expect(val).toEqual('marco');

    onChange({ target: { value: 'polo' } });

    ({ hook } = wrapper.find('div').props());
    [val, onChange, isValid] = hook;
    expect(val).toEqual('polo');
  });

  it('should set the right isValid value', () => {
    let wrapper = shallow(<HookWrapper hook={() => useFormField('marco')} />);

    let { hook } = wrapper.find('div').props();
    let [val, onChange, isValid] = hook;
    expect(val).toEqual('marco');
    expect(isValid).toEqual(true);

    onChange({ target: { value: 'polo' } });

    ({ hook } = wrapper.find('div').props());
    [val, onChange, isValid] = hook;
    expect(val).toEqual('polo');
    expect(isValid).toEqual(true);

    onChange({ target: { value: '' } });

    ({ hook } = wrapper.find('div').props());
    [val, onChange, isValid] = hook;
    expect(val).toEqual('');
    expect(isValid).toEqual(false);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

呈现自定义钩子并作为道具访问它，应该可以让我们完全访问它的返回值。

如果你在你的定制钩子中使用了`useEffect`钩子，确保你用[reactsetutils . act()](https://reactjs.org/docs/test-utils.html#act)包装了`shallow`或`mount`调用，以便在断言之前清除效果。酶可能会很快在内部支持这一点，但现在，这是必需的。更多信息请点击这里- [钩子-常见问题解答](https://reactjs.org/docs/hooks-faq.html#how-to-test-components-that-use-hooks)。

```
act(() => {
  wrapper = shallow(<HookWrapper />);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章中的所有代码片段都可以在 repo - [testing-hooks](https://github.com/flexdinesh/testing-hooks) 中找到，并附有一个工作示例。

测试愉快！🎉