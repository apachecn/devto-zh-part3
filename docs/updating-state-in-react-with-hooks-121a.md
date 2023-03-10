# 用挂钩更新 React 中的状态

> 原文：<https://dev.to/thorning_m/updating-state-in-react-with-hooks-121a>

在 React 中编写应用程序时，经常需要更新子组件的一些状态。对于编写为 ES6 类的组件，通常的方法是将函数作为绑定到父类上下文的道具向下传递给子类。React 新的 useState 钩子让事情变得更简单；事实上，自从钩子发布以来，我还没有写过一个类，所以我不再需要将函数绑定到保存状态的父组件的上下文中。将 useState 钩子返回的 setState 函数传递给孩子仍然容易出错，不过，现在我想向您展示另一种方法。

## 支柱钻孔

通过几层组件将支柱向下传递到需要它们的地方称为支柱钻孔。这里有一个例子:

```
import React, { useState } from 'react'
import ReactDOM from 'react-dom'
import InputComponent from './InputComponent'

function App() {
  const [items, setItems] = useState([])

  return (
    <>
      <InputComponent title="Add an Item:" items={items} setItems={setItems} />
      <ul>
        {items.map(item => (
          <li>{item}</li>
        ))}
      </ul>
    </>
  )
}

const rootElement = document.getElementById('root')
ReactDOM.render(<App />, rootElement) 
```

这是我们的顶级组件。它呈现一个`InputComponent`和一个无序列表`items`。在返回要渲染的元素之前，调用了`useState`函数，这建立了一个`items`数组(在`ul`元素中渲染)，你可以看到我们将`items`和`setItems`连同另一个名为`title`的道具一起传递给了`InputComponent`。

即使不看`InputComponent`，这段代码要做什么也应该很清楚。用户将能够输入一个项目的名称，该项目将被添加到列表中。尽管如此，我们还是来看看`InputComponent`吧！

```
import React from 'react'
import InputControls from './InputControls'

export default function InputComponent({ title, items, setItems }) {
  return (
    <>
      <h3>{title}</h3>
      <InputControls items={items} setItems={setItems} />
    </>
  )
} 
```

这是一个愚蠢的简单组件，它只显示`title`道具，然后渲染另一个名为`InputControls`的组件。我不建议在现实中编写这样的组件，我只需要几层来说明我的观点！下面是`InputControls`组件:

```
import React, { useState } from 'react'

export default function InputControls({ items, setItems }) {
  const [userInput, setUserInput] = useState('')

  function onInputChange(e) {
    setUserInput(e.target.value)
  }

  function onButtonClick() {
    setItems([...items, userInput])
    setUserInput('')
  }

  return (
    <>
      <input value={userInput} onChange={onInputChange} />
      <button onClick={onButtonClick}>Add</button>
    </>
  )
} 
```

这是接受用户输入的地方。有一个输入框，无论用户输入什么，它都会更新本地状态。还有一个按钮，当按下时，调用从顶层组件传下来的`setItems`函数。因为我们想要向数组`items`中添加新的条目(而不仅仅是替换已经存储在那里的内容)，并且状态是不可变的，所以我们还需要通过要在新数组中使用的组件层来传递它。

这行得通，那还有什么问题呢？好吧，如果我们在树的顶端附近重构我们的一些组件，并且忘记向下传递道具，我们可能会无意中进一步分解其他组件而没有意识到。很明显，你可以采取一些措施来防止这种情况发生，或者在发生这种情况时提醒你(想想回归测试或 [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html) ),但最好是完全排除这种情况发生的可能性。

## 传递道具通过

在这篇文章中，我想谈谈几个技巧。第一种是我经常使用的，我有一个组件包装另一个组件，并希望它使用自己的一些道具，然后将其余的传递给它的子组件。

```
export default function InputComponent(props) {
  const { title, ...rest } = props
  return (
    <>
      <h3>{title}</h3>
      <InputControls {...rest} />
    </>
  )
} 
```

通过使用 [ES6 rest 参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)，我们可以获取任何我们不需要的道具，并将它们赋给一个变量，然后通过使用[析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)，该变量可以作为道具传递给子组件。现在我们的`InputComponent`不需要知道所有的道具，它只需要得到它需要的东西，并传递其他的东西。如果我们重构`InputControls`，使它需要更多的道具，我们不需要改变`InputComponent`中的任何东西来使它工作，我们可以只在`App`中添加它们。

这是一个进步，但是我们仍然需要将`items`和`setItems`传递给`InputControls`作为道具。相反，我们可以使用 React 的上下文 API 和 useContext 挂钩，让我们可以从组件树中的任何一点访问我们的状态。

## 上下文和使用上下文

首先，我们将更改顶级组件，如下所示:

```
import React, { useState } from 'react'
import ReactDOM from 'react-dom'
import InputComponent from './InputComponent'

export const ItemsContext = React.createContext()

function App() {
  const [items, setItems] = useState([])

  return (
    <div>
      <ItemsContext.Provider value={[items, setItems]}>
        <InputComponent title="Add an Item:" />
      </ItemsContext.Provider>
      <ul>
        {items.map(item => (
          <li>{item}</li>
        ))}
      </ul>
    </div>
  )
}

const rootElement = document.getElementById('root')
ReactDOM.render(<App />, rootElement) 
```

在*5 号线*，我们增加了对`React.createContext`的呼叫。这将返回一个包含两个组件的对象，一个是提供者，另一个是消费者。我正在导出包含提供者和消费者的变量`ItemsContext`，这样我就可以将它导入到任何需要访问它的模块中，您可能希望将它保存在一个单独的文件中，以便更容易找到它；为了简单起见，我把它留在这里。

提供器在*线 12* ( `ItemsContext.Provider`)处使用，并包裹`InputComponent`。提供者可以包装任意多的组件，所有嵌套在其中的组件都可以访问提供者的`value` prop 的内容。

您可能还注意到，我们现在只将`title`道具传递给`InputComponent`。因为我们之前在使用`rest`的地方做了改动，所以`InputComponent`不需要做进一步的改动，我们可以保持原样，如果以后我们需要给`InputControls`组件添加新的道具，我们可以把它们传给`InputComponent`，它们就会失败。

让我们转到`InputControls`组件，看看如何从上下文提供者
中获取`items`和`setItems`

```
import React, { useState, useContext } from 'react'
import ItemsContext from './App'

function InputControls() {
  const [items, setItems] = useContext(ItemsContext)
  const [userInput, setUserInput] = useState('')

  function onInputChange(e) {
    setUserInput(e.target.value)
  }

  function onButtonClick() {
    setItems([...items, userInput])
    setUserInput('')
  }

  return (
    <>
      <input value={userInput} onChange={onInputChange} />
      <button onClick={onButtonClick}>Add</button>
    </>
  )
} 
```

在文件的顶部，我们需要从`App`导入`useContext`钩子和我们的`ItemsContext`。在第 5*行*中，我们调用`useContext`并传入`ItemsContext`，注意我们传入的是整个对象，而不仅仅是消费者。这将返回我们的`items`和`setItems`函数，我们可以像以前一样使用它们。还要注意，这个组件不再需要任何道具来运行，我们可以将它移动到应用程序中我们想要的任何位置，只要提供者组件在组件树中位于它之上，它就会继续工作。

* * *

使用这些技术可以使您的应用程序更加健壮，并且在添加、删除或移动组件时不太可能崩溃。这并不是对每种情况都理想的方法，但是它们确实是你可以使用的有用的方法。感谢阅读，希望对您有所帮助。😃