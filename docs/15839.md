# 错误:您的呈现方法应该有 return 语句

> 原文：<https://dev.to/kayut/-error-your-render-method-should-have-return-statement-2hin>

React 报错如下代码:你的 render 方法应该有 return 语句。

你能解释一下我在 ToDoItems.js 中的代码到底出了什么问题吗？

**ToDoItems.js**

```
import React, { Component } from 'react';

class Kir extends Component {
  state = {
    todos: [
      {
        id: 1,
        text: 'Take out the trash',
      },
      {
        id: 2,
        text: 'Grocery shopping',
      },
      {
        id: 3,
        text: 'Clean gecko tank',
      }
    ]
  };

  render() {
    const todoItems = this.state.todos.map((todoItem, index) => {
      return (
        <div key={index}>
          <label>
            <input
              type="checkbox"
              value={todoItem.text}
            />
            {todoItem.text}
          </label>
        </div>
      );
    });
  }
}

export default todoItems; 
```

**ToDo.js**

```
import React, { Component } from 'react';
import todoItems from './ToDoItems';

class ToDo extends Component {
  render() {
    return (
      <form>
        {todoItems}
      </form>
    );
  }
}

export default ToDo; 
```