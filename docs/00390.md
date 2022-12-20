# ⛓使用 React 进行非受控表单验证

> 原文：<https://dev.to/bluebill1049/uncontrolled-form-for-react-2b3n>

当我们使用表单验证时，我们大多数人都熟悉 Formik 和 Redux-form 之类的库。两者在社区中都很受欢迎，并且都是用受控组件构建的。

## 什么是受控元件？

反应驱动着自身的内部状态。每个输入交互或更改都会触发 React 的组件生命周期。拥有它的好处是:

> 每个状态突变都有一个相关的处理函数。这使得修改或验证用户输入变得非常简单。

这个特性非常适合处理表单验证。然而，有一个隐藏的成本。如果您运行下面的代码并注意开发人员控制台；

```
function Test() {
  const [numberOfGuests, setNumberOfGuests] = useState();
  console.log('rendering...');

  return (
    <form onSubmit={() => console.log(numberOfGuests)}>
      <input
        name="numberOfGuests"
        value={numberOfGuests}
        onChange={setNumberOfGuests} />
    </form>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到`console.log`重复“渲染...”在开发控制台中。显然，表单每次都会被重新呈现。我想对于简单的用例来说，这不会引起太大的问题。让我们试着实现一些更接近真实世界的例子。

```
function Test() {
  const [numberOfGuests, setNumberOfGuests] = useState();
  expensiveCalculation(numberOfGuests); // Will block thread
  console.log('rendering...');

  return (
    <form onSubmit={() => console.log(numberOfGuests)}>
      <input
        name="numberOfGuests"
        value={numberOfGuests}
        onChange={setNumberOfGuests} />
    </form>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这几乎是相同的代码，除了这一次每个渲染将在渲染之前执行一个昂贵的函数。(假设它会做一些繁重的计算并阻塞主线程)hmmm...现在我们有一个问题，因为用户交互可能会被中断。事实上，就表单性能优化而言，这种类型的场景确实让我头疼。

## 解

当然，桌面上有解决方案，你可以使用一个记忆函数来防止每次渲染都执行这个函数。下面举个例子:

```
function Test() {
  const [numberOfGuests, setNumberOfGuests] = useState();
  // The following function will be memoried with argument and avoid recalculation
  const memoizedValue = useMemo(() => computeExpensiveValue(numberOfGuests), [numberOfGuests]);

  return (
    <form onSubmit={() => console.log(numberOfGuests)}>
      <input
        name="numberOfGuests"
        value={numberOfGuests}
        onChange={setNumberOfGuests} />
    </form>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们实际上有另一个选项，可以在用户输入时跳过重新呈现表单。

## 不受控制的组件

[![](img/de8a0c4e9005fd0cb014400f81f4a28b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7XwhJreW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0pj35m3gs69v9tubiv49.jpg)

什么是非受控组件？

> 在大多数情况下，我们建议使用受控组件来实现表单。在受控组件中，表单数据由 React 组件处理。另一种方法是不受控制的组件，表单数据由 DOM 本身处理。

这意味着如果你要构建一个不受控制的表单，你将会处理 DOM 和表单交互的方法。那么让我们试一个例子。

```
function Test() {
  const numberOfGuests = useRef();
  expensiveCalculation(this.state.numberOfGuests);

  return (
    <form onSubmit={() => console.log(numberOfGuests.current.value)}>
      <input
        name="numberOfGuests"
        ref={numberOfGuests}
        value={numberOfGuests} />
    </form>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

通过利用不受控制的组件，我们获得了以下好处:

1.  用户交互不再触发更改时重新渲染。
2.  需要编写的潜在代码更少。
3.  访问 input 的 ref 可以让您做更多的事情，比如关注一个错误字段。

我想一个简单的问题会出现在你的脑海中，如果我想听输入的变化呢？现在你是输入的驱动者，你可以通过本地 DOM 事件来处理它。(都只是 javascript)下面的例子:

```
function Test() {
  const numberOfGuests = useRef();
  const handleChange = (e) => console.log(e.target.value)

  useEffect(() => {
    numberOfGuests.current.addEventListener('input', handleChange);
    return () => numberOfGuests.current.removeEventListner('input', handleChange);
  })

  return (
    <form onSubmit={() => console.log(numberOfGuests.current)}>
      <input
        name="numberOfGuests"
        ref={numberOfGuests} />
    </form>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们正在编写比受控组件更多的代码。但是，如果我们可以构建一个自定义挂钩来处理所有这些，并在应用程序中的多个表单中重用相同的逻辑，会怎么样呢？

## 挂钩

看看下面的例子；自定义表单验证挂钩:

```
import useForm from 'react-hook-form';

function App() {
  const { register, handleSubmit } = useForm();
  const onSubmit = (data) => { console.log(data) };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input name="numberOfGuests"ref={register({ required: true })} />
    </form>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您从上面看到的，实现是干净和简单的。没有`render-props`包装表单，没有包装单个字段的外部组件，验证规则也是集中的。

## 结论

不受控制的组件可能是一个更好的性能整洁和干净的方法，并可能编写更少的代码和更好的性能。如果你对上面的自定义钩子例子感兴趣并且喜欢它的语法。您可以在下面找到 Github repo 和 docs 链接:

github:[https://github.com/bluebill1049/react-hook-form](https://github.com/bluebill1049/react-hook-form)
网站:[https://react-hook-form.com](https://react-hook-form.com)

☕️感谢您的阅读。