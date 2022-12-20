# 使用闭包

> 原文：<https://dev.to/thorning_m/using-closures-1n9e>

当我第一次学习 JavaScript 中的闭包时，我看到了很多这样的例子:

```
const counter = (function() {
  let count = 0
  return function() {
    count++
    return count
  }
})()

console.log(counter()) //1
console.log(counter()) //2
console.log(counter()) //3 
```

现在，我很清楚发生了什么；外层函数(直接调用的函数表达式或简称 IIFE)声明了`count`变量，给它赋值零，然后返回内层函数。这个内部函数在我们每次调用`count()`时被执行，并且“知道”了`count`变量，因为它有闭包。

所以一切都很好。我不明白的一点是*为什么*这将是有用的！因此，我想展示我在日常编码中使用闭包的几种方式。

## 例一

第一种技术是封装。这是一种创建模块的方式，其中内部工作是私有的，并且只有某些方法被暴露给其他模块来与它们交互。这正是上面例子中发生的事情，但它是(希望如此！)当我们看一些有更多内容的代码时，更容易明白为什么它是有用的。

```
const library = (function() {
  const books = ['Island', '1984', 'Dracula', 'Papillon']

  return {
    getBooks() {
      return [...books]
    },
    deleteBook(title) {
      books.splice(books.indexOf(title), 1)
    },
    addBook(title) {
      books.push(title)
    },
  }
})()

library.addBook('catch 22')
library.deleteBook('Dracula')
console.log(library.getBooks()) // [Island, 1984, Papillon, catch 22]

const libraryBooks = library.getBooks()
libraryBooks.push('Beowulf')
console.log(libraryBooks) // [Island, 1984, Papillon, catch 22, Beowulf]
console.log(library.getBooks()) // [Island, 1984, Papillon, catch 22] 
```

如您所见，我们的`books`数组受到内部函数对它的封闭保护，不受外界影响。与它交互的唯一方式是通过公共方法；`getBooks()`返回数组的副本，而不是对数组的引用，一次只能添加或删除一本书。

## 例二

第二个例子是我经常使用的一个模式。我将使用一个 React 组件来演示，如果您不知道 React，请不要担心，很容易看到发生了什么。

```
class StartEndDate extends React.Component {
  state = {
    start: null,
    end: null
  }

  // This is our closure, I have used es6 arrow functions this time
  updateTime = startOrEnd => newTime => (
    this.setState({
      [startOrEnd]: newTime
    })
  )

  render() {
    return (
      <DateInput label="From" updateTime={this.updateTime('start')} />
      <DateInput label="To" updateTime={this.updateTime('end')} />
    )
  }
}

function DateInput({ label, updateTime }) {
  return (
    <label for="timeInput">From</label>
    <input id="timeInput" type="time" onChange={updateTime}>
  )
} 
```

在这段代码中，我们有一个声明为 es6 类的有状态 React 组件和一个功能组件。你应该能看到功能组件用在哪里，是在`render`方法的返回中(看起来像 HTML 的代码其实是 JSX；它调用`DateInput`函数，将道具`label`和`updateTime`传递给它)。

`DateInput`函数返回一个`<label>`和`<input>`的标记。当检测到变更事件时，该值被传递给`StartEndDate`类上的`updateTime`方法。

如果你看看这个函数被传递给`DateInput`的`render`方法，它首先用值“start”或“end”来调用。我们的两个输入现在有了自己版本的`updateTime`方法，它对`startOrEnd`变量进行了闭包。使用计算出的属性键，我们可以使用 closure 中的变量将新值动态分配给组件的状态。

* * *

这是我最常用的两种闭包模式。我希望我已经展示了为什么它们是这门语言的一个如此有用的特性，并且是您的工具集中的一个重要工具。