# 反应钩是活的。这是你的介绍

> 原文：<https://dev.to/nsebhastian/react-hooks-are-live-here-is-your-introduction-p6i>

React 团队的[说,](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines)班级可能是学习 React 的一大障碍。它们不能很好地缩小，并且使热重装变得不可靠。有了 React 钩子，你不需要把一个函数组件转换成类组件。您可以在功能组件中使用状态和生命周期方法*。*

好吧，但是钩子到底是什么？

它们是让你从功能组件中[“挂钩”到](https://reactjs.org/docs/hooks-overview.html#but-what-is-a-hook)反应内部状态和生命周期特性*的功能*

**太好了！我该怎么做？**

首先，更新项目中的 React 和 React DOM 依赖项。钩子今天在 React 16.8.0 新鲜出炉！

```
npm install --save react@^16.8.0 react-dom@^16.8.0 
```

现在让我们来看看`useState`挂钩。这是演示:

[https://codesandbox.io/embed/r1ryxqwvjo](https://codesandbox.io/embed/r1ryxqwvjo)

## `useState`钩

我们需要一个带有状态的简单类组件来进行比较。我能想到的最简单的例子是输入表单:

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: '' };
  }

  handleChange = event => {
    this.setState({ value: event.target.value });
  };

  render() {
    return (
      <form>
        <label>
          Name:
          <input
            type="text"
            value={this.state.value}
            onChange={this.handleChange}
          />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
} 
```

现在让我们用`useState`钩子重写它。我们将从`react`包中导入它，这样我们就不用一直写`React.useState`。

```
import React, { useState } from 'react'; 
```

现在把我们的`NameForm`改成一个函数组件

```
function NameForm(props) {} 
```

`useState` hook 取一个参数，就是初始状态，它返回两个值:当前状态和一个可以用来更新状态的函数。可以替换构造函数中的状态初始化:

```
this.state = { value: '' }; 
```

成这样:

```
function NameForm(props) {
  const [value, setValue] = useState('');
} 
```

注意声明状态变量时方括号的使用。这是 ES6 " [数组析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring)"语法，这意味着我们将 useState 返回的第一个值赋给`value`，将第二个值赋给`setValue`。

这意味着我们有一个名为`value`的状态，我们可以通过调用`setValue`函数来更新它。让我们把它用在我们的渲染方法上:

```
function NameForm(props) {
  const [value, setValue] = useState('');

  return (
    <form>
      <label>
        Name:
        <input
          type="text"
          value={value}
          onChange={event => setValue(event.target.value)}
        />
      </label>
      <input type="submit" value="Submit" />
    </form>
  );
} 
```

道具不再调用一个`handleChange`方法，取而代之的是我们有一个箭头函数，它将调用`setValue`函数，更新我们的`state`。哦，不像类组件中的`this.setState`，使用钩子*更新状态变量总是替换它而不是合并它*

## 有多个状态？

然后根据需要多次呼叫`useState`。

```
function SimpleForm(props) {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const [age, setAge] = useState('');

  return (
    <form>
      <label>
        First Name:
        <input
          type="text"
          value={firstName}
          onChange={event => setFirstName(event.target.value)}
        />
      </label>
      <label>
        Last Name:
        <input
          type="text"
          value={lastName}
          onChange={event => setLastName(event.target.value)}
        />
      </label>
      <label>
        Age:
        <input
          type="number"
          value={age}
          onChange={event => setAge(event.target.value)}
        />
      </label>
      <input type="submit" value="Submit" />
    </form>
  );
} 
```

而且就是这样用`useState`勾，真的！

## 从道具中设置初始状态

哦，太简单了！

```
function SimpleForm(props) {
  const [firstName, setFirstName] = useState(props.firstName);
  const [lastName, setLastName] = useState(props.lastName);
  const [age, setAge] = useState(props.age);

  //...
}

ReactDOM.render(
  <SimpleForm firstName="JOHN" lastName="Edward" age={30} />,
  document.getElementById('root')
); 
```

## 我可以在状态下使用对象吗？

当然可以！正如类`state`可以接受对象或数组一样，`useState`也可以拥有它们。但是为了让它工作，让我们给你的输入元素添加名称道具。我们还使用[扩展属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#Spread_in_object_literals)来更新我们的状态。

```
function SimpleForm(props) {
  //create object state
  const [form, setForm] = useState({
    FirstName: '',
    LastName: '',
    age: '',
  });

  const handleChange = event => {
    // use spread operator
    setForm({
      ...form,
      [event.target.name]: event.target.value,
    });
  };

  return (
    <form>
      <label>
        First Name:
        <input
          type="text"
          name="firstName"
          value={form.firstName}
          onChange={handleChange}
        />
      </label>
      <label>
        Last Name:
        <input
          type="text"
          name="lastName"
          value={form.lastName}
          onChange={handleChange}
        />
      </label>
      <label>
        Age:
        <input
          type="number"
          name="age"
          value={form.age}
          onChange={handleChange}
        />
      </label>
      <input type="submit" value="Submit" />
    </form>
  );
} 
```

## 数组呢？

这一个有点不寻常，但是是的，就像在课堂上一样。我们将在数组更新时使用 concat。

```
function SampleArrayState(props) {
  //create array state
  const [nameArray, setNameArray] = useState(['Carl']);

  const updateArray = () => {
    setNameArray(nameArray.concat('John'));
  };

  return (
    <React.Fragment>
      <button onClick={updateArray}>Click me!</button>
      <div>{nameArray.toString()}</div>
    </React.Fragment>
  );
} 
```

## 和布尔？

这里有你的消息:

```
function SampleBooleanState(props) {
  const [show, setShow] = useState(true);
  const visibility = show ? 'visible' : 'hidden';

  return (
    <React.Fragment>
      <h1 style={{ visibility }}>useState Hook is awesome!</h1>
      <button
        onClick={() => { setShow(!show) }}
      >{`${show ? 'Hide' : 'Show'} the Header!`}</button>
    </React.Fragment>
  );
} 
```

## 钩子的规则

重要的是要记住钩子在类组件中不起作用。它们是为功能组件制造的。

1.  不要从嵌套函数、循环或条件中调用钩子。
2.  不要从普通的 JavaScript 函数中调用钩子。
3.  本 [![no refactoring](img/54941e48584773fb8b45f65e2ee4ee55.png)](https://news.ycombinator.com/item?id=19094170)

## 结论

`useState`钩子使得函数组件能够访问 React 的内部状态并更新它。状态可以是任何数据类型:字符串、数字、布尔值、数组或对象。`useState`接受一个参数:初始数据，它返回两个值的数组:当前状态值和可用于更新状态的函数/方法。

钩子不仅仅只有`useState`个，但是让我们把每个钩子都放在它们自己的帖子里。

这里列出了所有[内置钩子](https://reactjs.org/docs/hooks-reference.html)(是的，你可以写一个自定义钩子！)在撰写本文时可用。我将在下一篇文章中介绍它们！

另外，我正在写一本关于学习在没有压力的情况下做出适当反应的书。你可能想看看这里的。

下次见！

[![](img/03523292e908aa2d830908a375bebf7f.png)](https://sebhastian.com/react-distilled)