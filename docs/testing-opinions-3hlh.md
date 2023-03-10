# 测试意见

> 原文：<https://dev.to/juliang/testing-opinions-3hlh>

在工作中，我和我的同事经常讨论测试特定 React 组件的最佳方式或正确方式。我们并不总是意见一致。

> 测试一个函数的输出和测试它的实现细节是不同的。

## 避免测试实现细节

(几乎)我们所有人都同意应该避免[测试实现细节](https://kentcdodds.com/blog/testing-implementation-details)。问题有时是要对实现细节的定义达成一致。

想象我们有一个`<Banana>`组件(当然)。

```
export function Banana({ type, color, length }) {
  return (
    <div className="banana">
      <BananaType type={type} />
      <BananaColor color={color} />
      <BananaLength length={length} />
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

单元测试纯化论者会认为我们需要孤立地测试每个组件。对于组件隔离，我们需要模仿孩子或者做一个简单的渲染，看看它如何在孩子身上设置道具。差不多是这样:

```
 it("renders a banana properly", () => {
    const banana = { type: "Cavendish", color: "yellow", length: 12 };
    const wrapper = shallow(<Banana {...banana} />); // from 'enzyme'

    const bananaType = wrapper.find(BananaType); // from 'enzyme'
    const bananaColor = wrapper.find(BananaColor);
    const bananaLength = wrapper.find(BananaLength);

    // make sure each child has the correct props
    expect(bananaType.props()).toHaveProperty("type", "Cavendish");
    expect(bananaColor.props()).toHaveProperty("color", "yellow");
    expect(bananaLength.props()).toHaveProperty("length", 12);
    // 🤔
  }); 
```

Enter fullscreen mode Exit fullscreen mode

### 这里有什么问题吗？

1.  测试试图找到主题的实现依赖项`BananaType`、`BananaColor`和`BananaLength`。
2.  测试期望在内部孩子身上设置某些道具；再次:实现细节。

### 我们能做什么来代替？

我们的`<Banana>`组件**拥有**这三个子组件，所以“被测试的主题”可以被认为是整个事物:组件及其子组件。

我更喜欢“渲染”组件并检查其输出。

```
 it("renders a banana properly", () => {
    const banana = { type: "Cavendish", color: "yellow", length: 12 };

    const {
      getByText
    } = render(<Banana {...banana} />); // from 'react-testing-library'

    getByText('Cavendish Banana');
    getByText('yellow');
    getByText('12cm');
  }); 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，[快照](https://jestjs.io/docs/en/snapshot-testing.html)测试可能更好:

```
 it("renders a banana properly", () => {
    const banana = { type: "Cavendish", color: "yellow", length: 12 };
    const result = render(<Banana {...banana} />); // from 'react-testing-library'
    expect(result.container).toMatchSnapshot();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

### 等等！快照测试不依赖于实现细节吗？

一些人认为对渲染输出的测试(即快照测试)类似于测试实现细节。例如，假设我们的`<Banana>`组件改变了它的实现，而不是呈现`<BananaLength>`，而是呈现`<div class="banana-length">12 cm</div>`(注意‘12’和‘cm’之间的空格)。那么我上面的测试就会失败。

但是我认为呈现的标记是函数的输出，而不是它的实现细节。如果渲染改变了，那么我的模块的输出也改变了，那么**测试必须中断**。

### 关于反应-测试-库

我在一些项目中使用了 [reac-testing-library](https://testing-library.com/react) 。但是第一个例子是用[酶](https://airbnb.io/enzyme/)代替。当然，你可以避免用 Enzyme 测试实现细节，但是我认为 react-testing-library 更容易避免那些不好的做法。

我的观点可能有偏见，因为我一直在学习 Kent C. Dodds 的[测试 javascript](https://testingjavascript.com/) 课程。

请在评论中不同意我的观点。