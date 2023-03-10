# 使用反应测试库进行测试

> 原文：<https://dev.to/chriswcs/testing-with-react-testing-library-42l8>

### 反应-测试-库的哲学

"你的测试越像你的软件被使用的方式，它们就能给你越多的信心."-肯特·c·多兹

这是[反应-测试-库](https://github.com/kentcdodds/react-testing-library)中的指导原则。该库帮助您编写测试，这些测试以用户与组件交互的方式与组件进行交互。该库还侧重于测试，而没有测试实现细节。我将在本文中探讨这些想法，并希望说服您尝试一下这个神奇的库。非常感谢 Kent C. Dodds 和所有其他贡献者提供了这个伟大的软件。

### 基础知识

当我在下面展示一些例子时，请注意我使用 Jest 作为我的测试程序。react-testing-library 是一个测试 react 组件的工具。它不是 Jest 或 Jest 等其他工具的替代品。

我将首先呈现我的组件。当然，我将使用渲染函数来实现这一点😏，从 react-testing-library 导入。然后从 render 返回有用的函数，这些函数可以用来测试我们的组件。组件是完全渲染的(不是浅渲染的),因此一切都可以交互。

```
import React from 'react'
import { render, fireEvent } from 'react-testing-library'
import Counter from './Counter'

it("starts at 0 and it is incremented to 1", () => {
    // Render takes a component (props can be passed in as well
    // I am going to use getByTestId to access elements
    const { getByTestId } = render(<Counter />)

    // getByTestId will match elements by the attribute data-testid="count"
    // here I saved two elements in their respective variables
    const count = getByTestId('count')
    const plusButton = getByTestId('plus')

    // I can check properties on the element
    // I want to make sure the tag is showing the  count to be at 0 to begin
    expect(count.textContent).toBe('0')

    // fireEvent is a function that we imported from the library
    // it allows us to click on the button the way the user would.
    // I simply access the click function on it and pass a element for it to click
    fireEvent.click(plusButton)

    // Asserting that it now is now 1
    expect(count.textContent).toBe('1')
}) 
```

我测试的组件如下。除了放置 data-testid 属性，我没有测试实现。在本文的后面，我将展示访问元素的其他方法，这些方法不需要将 data-testid 属性放在。

```
import React from 'react'

class Counter extends React.Component {
    state = {
        count: 0
    }

    increment = () => {
        this.setState(x => ({
            count: x.count + 1
        }))
    }

    render(){
        return (
            <div>
                <h1 data-testid="count">{this.state.count}</h1>
                <button data-testid="plus" onClick={this.increment}>+</button>
            </div>
        )
    }
} 
```

下面我用钩子重构了同一个组件，上面的测试没有失败。这是非常令人满意的，并且在使用其他 react 测试工具时可能很难实现。

```
const Counter = () => {
    const [count, setCount] = React.useState(0)
    const increment = () => {
        setCount(count + 1)
    }

    return (
        <div>
            <h1 data-testid="count">{count}</h1>
            <button data-testid="plus" onClick={increment}>+</button>
        </div>
    )
} 
```

### 更深入的例子

下面的例子有一些我还没有展示的新东西。在这里，我使用 getByPlaceholderText 和 getByLabelText 来展示如何在不需要放置 data-testid 的情况下访问元素。我还使用 fireEvent.change 按照用户的方式在输入中放置一个新值。

```
import React from 'react'
import { render, fireEvent } from 'react-testing-library'
import Problem from './Problem'

it("has two inputs that I can change to see if it works", () => {
    // Arrange
    const { getByTestId, getByPlaceholderText, getByLabelText } = render(<Problem total={30}/>)
    const checkResult = getByTestId('check')
    // The getByPlaceholderText work as you would expect, the input can be found
    // via the value of the PlaceholderText attribute
    const input_one = getByPlaceholderText('Place the first number here!')
    // The getByLabelText grabs the input by the label
    const input_two = getByLabelText('Number 2')
    const result = getByTestId('result')

    // Act
    fireEvent.change(input_one, { target: { value: 10 } })
    fireEvent.change(input_two, { target: { value: 20 } })
    fireEvent.click(checkResult)

    // Asserting that it was correct
    expect(result.textContent).toBe('Good')
}) 
```

下面是组件。我用钩子是因为我喜欢钩子！查看[钩子文档](https://reactjs.org/docs/hooks-intro.html)以获得更多关于使用它们的信息。

```
import React from 'react'

export default ({ total }) => {
    const [input1, setInput1] = React.useState(0)
    const [input2, setInput2] = React.useState(0)
    const [status, setStatus] = React.useState(false)

    const update1 = e => setInput1(Number(e.target.value))
    const update2 = e => setInput2(Number(e.target.value))

    const checkAnswer = () => setStatus(input1 + input2 === total)

    return(
        <div>
            <label>Number 1
                <input type="Number" 
                       value={input1} 
                       placeholder='Place the first number here!'
                       onChange={update1}/>
            </label>
            <h2>+</h2>
            <label>Number 2
                <input type="Number" 
                       value={input2}
                       onChange={update2}/>
            </label>
            <h2>=</h2>
            <h2>total</h2>
            <button data-testid="check" onClick={checkAnswer}>Check Answer</button>
            <h2 data-testid="result">{status ? 'Good' : 'Wrong'}</h2>
        </div>
    )
} 
```

如果您有多个测试，从 react-testing-library 导入一个名为 cleanup 的函数是很重要的。这将删除测试之间的组件，为下一次渲染做准备。

```
import { render, cleanup} from 'react-testing-library'

afterEach(cleanup) 
```

如果您在 react 组件中使用钩子，请查阅文档，了解何时可能需要一些特殊的设置(由于 useEffect 的异步特性)。

### 结论

查看 [react-testing-library 文档](https://github.com/kentcdodds/react-testing-library)了解更多信息。我已经张贴在下面的项目，你可以找到我的例子。我鼓励每个人在你的下一个项目中使用 react-testing-library。感谢您抽出时间阅读。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[ChrisWcs](https://github.com/ChrisWcs)/[RTL——举例](https://github.com/ChrisWcs/rtl-example)

### 反应-测试-库示例

<article class="markdown-body entry-content container-lg" itemprop="text">

# 这个项目包含使用 react-testing-library 的测试示例

### 使用`npm test`运行示例。

</article>

[View on GitHub](https://github.com/ChrisWcs/rtl-example)