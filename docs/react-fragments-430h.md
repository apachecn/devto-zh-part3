# 反应碎片

> 原文：<https://dev.to/sarah_chima/react-fragments-430h>

JSX 语法的一部分是相邻的 JSX 元素被包装在封闭标签中。也就是说，JSX 表达式必须只有一个最外层元素。遵守这一规则可能意味着即使在不必要的时候也要添加额外的 DOM 节点。感谢 React 片段，我们不需要这样做。

让我用一个例子来进一步解释这个问题。

### 问题是

假设我们有一个表单，我们希望输入字段是带有标签的自定义组件。

```
class Form extends Component {
    render() {
        return (
            <form>
                <InputField name="Name"/>
                <button>Submit</button>
            </form>
        )
    }
} 
```

然后对于`InputField`组件，我们有如下:

```
class InputField extends Component {
    render() {
        return (
            <div>
                <label>{this.props.name}</label>
                <input placeholder={this.props.name} />
            </div>
        )
    }
} 
```

注意我们是如何在一个`div`中包装`label`和`input`字段的。我们这样做是为了遵守 JSX 语法。这是上面在 DOM 中的输出。

```
<form>
    <div>
        <label>Name</label>
        <input placeholder="Name">
    </div>
    <button>Submit</button>
</form> 
```

以下是与此添加相关的一些问题:

1.  在 DOM 中引入额外的、不必要的节点会导致更多的 DOM 混乱。如果我们对许多其他组件也这样做，我们将在 DOM 中有如此多的额外节点。这对小型应用程序的影响可能很小。但是在性能非常重要的大型应用程序中，添加额外的 DOM 节点会导致内存使用量增加。

2.  它使得像 FlexBox 和 CSS Grid 这样的 CSS 机制的使用更加复杂。这些机制取决于父元素和子元素之间存在的关系。因此，通过引入额外的`div`来打破这种关系会使造型更加复杂。

好消息是 React 片段解决了这个问题。让我们看看如何。

### 正式见面片段

片段可以让您分组一个子列表，而无需向 DOM 添加额外的节点。我们使用它的方式与使用`div`将元素包装在`InputField`元素中的方式完全相同。我们开始吧。

```
class InputField extends Component {
    render() {
        return (
            <React.Fragment>
                <label>{this.props.name}</label>
                <input placeholder={this.props.name} />
            </React.Fragment>
        )
    }
} 
```

这将被传输到

```
 <form>
    <label>Name</label>
    <input placeholder="Name">
    <button>Submit</button>
</form> 
```

耶！！没有添加额外的`div`。

我们可以在不引用 react 对象的情况下使用它，方法是使用对象析构从 React 导入它。

```
import React, { Component, Fragment } from 'react'

class InputField extends Component {
    render() {
        return (
            <Fragment>
                <label>{this.props.name}</label>
                <input placeholder={this.props.name} />
            </Fragment>
        )
    }
} 
```

### 片段的属性

或者说，碎片的属性。片段只支持一个属性——`key`属性。这对于每个列表项都需要键的列表尤其有用。下面的描述列表就是一个例子:

```
 class TodoList extend Component {
    render() {
        return (
            <dl>
            ${this.props.todos.map(todo =>
                <React.Fragment key={todo.id}>
                    <dt>{todo.name}</dt>
                    <dd>{todo.description}</dd>
                </React.Fragment>
            )
            </dl>
        )
    }
} 
```

### 更新更短的语法

一种新的较短的编写片段的语法是声明片段。就是用`<>`和`</>`代替`Fragment`。我知道，它们看起来像空标签。

```
class InputField extends Component {
    render() {
        return (
            <>
                <label>{this.props.name}</label>
                <input placeholder={this.props.name} />
            </>
        )
    }
} 
```

这种语法的缺点是它不支持键或属性。许多工具也不支持它，所以您可能希望显式地写出`Fragments`。

#### 结论

React 片段很酷，你应该使用它们。

感谢您的阅读。😊