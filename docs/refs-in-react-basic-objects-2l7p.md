# React 中的引用，基本对象

> 原文：<https://dev.to/nickymeuleman/refs-in-react-basic-objects-2l7p>

> TL；DR: Refs 是形式为`{ current: //something }`的简单对象。在功能组件中，选择`useRef`，否则，选择`createRef`。

在 React 中，ref 是一个具有可变`current`属性的对象。
就是这样。您可以查看下面的[实现](#implementations)，但是它们可以归结为:
`ref = { current: //something }`

`current`属性通常被设置为保存对 DOM 元素的*引用*，就像一个`<audio>`标记

获取一个元素的引用允许你直接给它命令。这对于控制媒体播放、管理焦点等非常有用。

在任何地方都这样做可能很诱人，但是要抵制这种诱惑。

## 基本用法

目标是获取对一个`<audio>`标签的引用。
这将允许你在那个元素上调用`.play()`和`.pause()`。

有相当多的方法可以达到同样的目的。

然而步骤总是一样的。

几个例子可以在这个 codesandbox 演示
[https://codesandbox.io/embed/4jzlzr7937](https://codesandbox.io/embed/4jzlzr7937) 中查看

> 所示代码:在带有构造函数的基于类的组件中使用 createRef

*   创建引用对象

```
// inside the constructor
this.audioRef = React.createRef();
// this.audioRef = { current: null } 
```

*   将对 DOM 元素引用附加到 ref 对象

```
// inside the render method
<audio ref={this.audioRef} />
// this.audioRef = { current: <audio /> } 
```

*   使用它

```
// a handler that reacts to a click
handlePlay = () => {
  this.audioRef.play();
}; 
```

## 其他用例

### 泛指起反应的成分

上面的例子展示了如何获取对 DOM 元素的引用。这不是裁判唯一擅长的事情。它们也可以指向一个 React 组件对象。

[https://codesandbox.io/embed/9jxqj20wqo?module=%2Fsrc%2FParentComponent.js](https://codesandbox.io/embed/9jxqj20wqo?module=%2Fsrc%2FParentComponent.js)

重要提示:你只能用这种方式**引用基于**类的**组件**

### 引用 React 组件内部的 DOM 元素

当你给自己做了一个奇特的`Input`组件，并希望一个引用直接指向`<input>`标签，这样你就可以调用`inputRef.current.focus()`了，那该怎么办？

```
<Input ref={inputRef} /> 
```

有问题！如果你的`Input`是一个类组件，它要么指向 React 组件对象，要么如果你的`Input`是一个函数组件，它抛出一个大错误。

那个*大错*其实真的很有帮助:

```
Warning: Function components cannot be given refs. Attempts to access this ref will fail. Did you mean to use React.forwardRef()? 
```

接收一个函数，这个函数被你的组件接收到的 props 和 ref 调用。它返回组件通常会返回的内容。在那里，您可以将引用附加到您想要特定 DOM 元素。

```
// in the file you want to use the ref
import Input from './Input';

function App() {
  const inputRef = React.useRef();

  // ...

  return (
    <div>
      <Input />
      {/* attach the ref to our Input component */}
      <Input ref={inputRef} />
      <Input />
    </div>
  );
}

// in your Input.js file
export default React.forwardRef((props, ref) => {
  return (
    // ...
    // forward the ref our Input component received to the HTML <input/>tag
    <input ref={ref} placeholder={props.placeholder} />
    // ...
  );
}); 
```

这个[代码沙盒演示](https://codesandbox.io/s/pyqq0o9mk7)展示了三个`Input`组件。
一个`<input />`标签通过 ref a 组件聚焦，使用`React.forwardRef`接收并转发。

不得不在`React.forwardRef`中包装你的函数组件会变得非常乏味。
在未来，`forwardRef`的这种用法可能会成为默认行为。

> ![Dan Abramov profile image](img/17c386e6839df2dd490f297a2c139b1e.png)丹阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@耶鲁安 _ 兰斯金](https://twitter.com/Jeroen_Ransijn) [@reactjs](https://twitter.com/reactjs) 你见过钩子吗？我们宁愿在 API 层面解决问题，而不是 codegen。例如，forwardRef 将成为未来版本中的默认行为。2019 年 3 月 23 日下午 17:49[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1109512531209584640)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1109512531209584640)5[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1109512531209584640)62

## createRef vs. useRef

而`createRef`和`useRef`与**非常**相似。有一些微妙的区别。

`useRef`是所谓的[钩子](https://reactjs.org/docs/hooks-intro.html)，仅在基于功能的组件中可用。

```
// in a function component
const myRef = React.useRef('my initial value'); 
```

`useRef`创建一个对象`{ current : initialValue }`并在第一次被调用时返回它。如果该对象已经存在，`useRef`将返回现有的对象。

`createRef`会在每次被调用时创建一个对象`{ current : null }`。

```
// in the constructor of a class based component
this.myRef = React.createRef();
// even in a function based component
const myRef = React.createRef(); 
```

### 实例变量

在基于类的组件中，如果你想要一个可以改变但不会触发渲染循环的值。实例变量就是你想要的。例如:`this.palindrome = 'racecar'`和后来的`this.palindrome='pullup'`

在功能组件中，`useRef`可以填充那个角色！

因为`useRef(initialValue)`返回一个具有可变`current`属性的对象。没有什么能阻止我们像使用实例变量一样使用它。

```
const palindrome = useRef('racecar');
// const palindrome = { current: 'racecar' }
// ...
palindrome.current = 'pullup';
// const palindrome = { current: 'pullup' } 
```

这个演示使用`useRef`指向`<form>` DOM 元素**，使用**指向一个内部计数变量。
[https://codesandbox.io/embed/vmwxjnv433](https://codesandbox.io/embed/vmwxjnv433)

## 实现

官方 [React 代码库](https://github.com/facebook/react)中`createRef`和`useRef`的实现出奇的简单。

### createRef

```
export function createRef() {
  const refObject = {
    current: null
  };
  if (__DEV__) {
    Object.seal(refObject);
  }
  return refObject;
} 
```

React 代码库中的 [`createRef`实现](https://github.com/facebook/react/blob/f33e5790b83dc1ae41b2b7d59d53420e7c8383aa/packages/react/src/ReactCreateRef.js)

### useRef

```
export function useRef(initialValue) {
  currentlyRenderingFiber = resolveCurrentlyRenderingFiber();
  workInProgressHook = createWorkInProgressHook();
  let ref;

  if (workInProgressHook.memoizedState === null) {
    ref = { current: initialValue };
    if (__DEV__) {
      Object.seal(ref);
    }
    workInProgressHook.memoizedState = ref;
  } else {
    ref = workInProgressHook.memoizedState;
  }
  return ref;
} 
```

React 代码库中的 [`useRef`实现](https://github.com/facebook/react/blob/8482cbe22d1a421b73db602e1f470c632b09f693/packages/react-reconciler/src/ReactFiberHooks.js#L500-L515)