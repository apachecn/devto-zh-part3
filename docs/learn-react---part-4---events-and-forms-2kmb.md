# 学习 React -第 4 部分-事件和表单

> 原文：<https://dev.to/felipegalvao/learn-react---part-4---events-and-forms-2kmb>

[最初发表于我的博客](http://felipegalvao.com.br/blog/2019/01/27/learn-react-events-and-forms/)

嘿伙计们。在我们的[上一篇](https://dev.to/felipegalvao/learn-react---part-3---components-state-and-props-1h0g)中，我们谈到了**组件、状态和道具**。在这一集里，我们将谈论**事件和形式**。这两个主题对于任何 web 应用程序来说都是至关重要的，尽管 React 并不太难，但它们呈现出某些特殊性。

让我们从我们在之前所有关于 React 的帖子中使用的样板文件开始。你可以在这里找到它:[https://github.com/felipegalvao/webpack-4-react-boilerplate](https://github.com/felipegalvao/webpack-4-react-boilerplate)

我们走吧！

## 事件

首先，什么是事件？事件用于通知您的代码正在发生有趣的事情。这个有趣的事情可以由用户触发，也可以由页面本身触发。例如，用户点击一个按钮，或者页面加载完毕，或者文本输入的值发生变化。

这样一来，我们可以继续了。在 **React** 中，事件有点类似于基本的 **HTML + Javascript** ，但是有一些不同。让我们看看`onClick`事件的例子。而在 HTML 中我们会做:

```
<button href="#" onclick="alert('row was added!');">
  Add row
</button> 
```

html

在 React 中，我们将代码放在花括号内，看起来像这样(组件的代码):

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import './styles/main.scss';

class Index extends Component {
  render() {
    return <div>
      <button onClick={ () => alert('row was added!') }>add row</button>
    </div>;
  }
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

在 React 中，要在事件中直接运行 Javascript 代码，我们必须使用一个**箭头函数**并以这种方式运行代码，这样当组件被渲染时代码就不会运行。也可以调用预定义的函数。在 HTML 中，我们会这样做:

```
<button onclick="handleClick()">
  add row
</button> 
```

在 React 中，我们将把函数放在花括号内。正如我们已经在关于状态和道具的[帖子中看到的，为了让`this`关键字工作，我们需要在组件的`constructor`中`bind`它:](https://dev.to/felipegalvao/learn-react---part-3---components-state-and-props-1h0g)

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import './styles/main.scss';

class Index extends Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    alert('row was added!');
  }

  render() {
    return <div>
      <button onClick={ this.handleClick }>add row</button>
    </div>;
  }
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

如果这困扰着你，还有两种方法可以让它工作，就像在同一篇文章中解释的那样。我特别喜欢用箭头函数来处理这类问题。

好了，写完上面的代码，你可以点击按钮，你会得到浏览器的提醒。`onClick`是按钮最常见的事件。

另一个非常常见的事件是`onChange`事件，通常与 input 元素一起使用。每次输入值改变时，触发`onChange`事件。让我们使用下面的代码来看看这种行为:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import './styles/main.scss';

class Index extends Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
    this.handleTextChange = this.handleTextChange.bind(this);
    this.state = {currentText: ''}
  }

  handleClick() {
    alert('row was added!');
  }

  handleTextChange(event) {
    this.setState({currentText: event.target.value});
  }

  render() {
    return <div>
      <button onClick={ this.handleClick }>add row</button>
      <input
        type="text"
        placeholder="enter your name here"
        onChange={ this.handleTextChange }
      />
      <p>{ this.state.currentText }</p>
    </div>;
  }
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

开始在文本输入上打字，看看段落会如何相应地改变。注意，在这种情况下，我们必须用一个空字符串来设置初始状态。如果我们不这样做，段落将在试图呈现时中断，因为我们将试图引用的状态部分将不会被定义。

## 表单控制组件

学习了事件之后，现在让我们来谈谈形式。正如我们已经讨论过的，表单是大量 web 应用程序的重要组成部分，正因为如此，理解它们如何在 React 上工作非常重要。

在 React 中，推荐使用表单的方式是通过名为**受控组件**的概念。在这种工作方式下，我们使得状态成为表单中值的**唯一来源**，并且我们使用表单元素上的事件来不断更新状态上的值。然后调用函数在表单的 **onSubmit** 事件上提交表单。对于一个带有文本输入的简单表单，应该是这样的:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import './styles/main.scss';

class Index extends Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.handleTextChange = this.handleTextChange.bind(this);
    this.state = {name: ''}
  }

  handleSubmit(event) {
    alert('Your name was sent to our API, ' + this.state.name);
    event.preventDefault();
  }

  handleTextChange(event) {
    this.setState({name: event.target.value});
  }

  render() {
    return <div>
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input
            type="text"
            placeholder="enter your name here"
            onChange={ this.handleTextChange }
            value={ this.state.currentText }
          />
        </label>
        <input type="submit" value="Send" />
      </form>
    </div>;
  }
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

键入您的姓名，然后点击`Send`按钮，您将看到带有您在输入中键入的姓名的警告。让我们添加一个`select`元素:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import './styles/main.scss';

class Index extends Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.handleTextChange = this.handleTextChange.bind(this);
    this.handleColorSelect = this.handleColorSelect.bind(this);
    this.state = {name: '', favoriteColor: 'blue'}
  }

  handleSubmit(event) {
    alert(
      `Your name is ${this.state.name} and your favorite color is ${this.state.favoriteColor}`
    );
    event.preventDefault();
  }

  handleTextChange(event) {
    this.setState({name: event.target.value});
  }

  handleColorSelect(event) {
    this.setState({favoriteColor: event.target.value});
  }

  render() {
    return <div>
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input
            type="text"
            placeholder="enter your name here"
            onChange={ this.handleTextChange }
            value={ this.state.currentText }
          />
        </label>
        <select value={this.state.favoriteColor} onChange={this.handleColorSelect}>
          <option value="blue">Blue</option>
          <option value="red">Red</option>
          <option value="green">Green</option>
          <option value="black">Black</option>
        </select>
        <input type="submit" value="Send" />
      </form>
    </div>;
  }
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

对于`select`元素，您在元素内部创建每个选项，每个选项都有自己的值，然后将事件传递给当 select 元素值发生变化时要调用的函数。继续测试，选择你最喜欢的颜色，然后点击按钮提交表单。

为了结束这篇文章，让我们看看`radio`按钮在 React 中是如何工作的。它非常类似于`select`元素。我将添加一些`div`来更好地组织代码和布局，然后我将添加`radio`按钮:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import './styles/main.scss';

class Index extends Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.handleTextChange = this.handleTextChange.bind(this);
    this.handleColorSelect = this.handleColorSelect.bind(this);
    this.handleAnimalSelect = this.handleAnimalSelect.bind(this);
    this.state = {name: '', favoriteColor: 'blue', favoriteAnimal: ''}
  }

  handleSubmit(event) {
    alert(
      `Your name is ${this.state.name}, your favorite color is ${this.state.favoriteColor}` +
      `and your favorite animal is ${this.state.favoriteAnimal}`
    );
    event.preventDefault();
  }

  handleTextChange(event) {
    this.setState({name: event.target.value});
  }

  handleColorSelect(event) {
    this.setState({favoriteColor: event.target.value});
  }

  handleAnimalSelect(event) {
    this.setState({favoriteAnimal: event.target.value});
  }

  render() {
    return <div>
      <p>Insert your name, your favorite color and your favorite animal.</p>
      <form onSubmit={this.handleSubmit}>
        <div>
          <label>
            Name:
            <input
              type="text"
              placeholder="enter your name here"
              onChange={ this.handleTextChange }
              value={ this.state.currentText }
            />
          </label>
        </div>
        <div>
          <select value={this.state.favoriteColor} onChange={this.handleColorSelect}>
            <option value="blue">Blue</option>
            <option value="red">Red</option>
            <option value="green">Green</option>
            <option value="black">Black</option>
          </select>
        </div>
        <div>
          <label>
            <input
              type="radio"
              name="react-tips"
              value="dog"
              checked={this.state.favoriteAnimal === "dog"}
              onChange={this.handleAnimalSelect}
            />
            Dog
          </label>
        </div>
        <div>
          <label>
            <input
              type="radio"
              name="react-tips"
              value="cat"
              checked={this.state.favoriteAnimal === "cat"}
              onChange={this.handleAnimalSelect}
            />
            Cat
          </label>
        </div>
        <div>
          <input type="submit" value="Send" />
        </div>
      </form>
    </div>
  }
};

ReactDOM.render(<Index />, document.getElementById("index")); 
```

注意，对于`radio`按钮，我们通过比较它的值和当前状态的值来定义它是否是`checked`。

这样，我们能够学习如何在 React 中使用表单，以及如何使用最常见的表单元素。

在“现实生活”的例子中，表单的提交可能会用它的值向 API 发出请求，可能使用 Axios、fetch 或您喜欢的任何其他方式。但是这是另一篇文章的主题。

干杯，我希望这是有帮助的。