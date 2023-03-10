# 组成和反应

> 原文：<https://dev.to/varenya/composition-and-react-2a9b>

所以让我们从本题题目的第一项开始，即**作文**。

## 是什么，为什么重要？

它意味着把不同的东西放在一起，创造出比单个碎片更大的东西。一个很好的例子就是语言本身，不，不是编程语言，而是我们自己的语言。

为什么

*字母*拼起来形成*词*
T5】*词*拼起来形成*句*

*句*拼起来形成*段*。

你明白我的意思吗？只要把 **`put`** 换成 **`compose`** ，你就会明白我的意思了。我们能随机组合单词来造句吗？不。造句是有规则的，即语法。

所以让我们试着在编程的上下文中定义这个术语。所以总的想法是把一种类型的东西组合起来创造出其他类型的东西。

在编程语言中，我们有像整数、字符串、函数、对象这样的原语，我们将它们组合起来产生软件，而不是字母、单词、句子。

那么对于编程语言来说，语法或规则的必然结果是什么呢？在最基本的层面上，它只不过是编译器强制执行的`syntax`，除非你遵循`syntax`，否则你将无法得到可工作的软件。
类似于如果我们不遵循语法，你就不会得到一个合适的句子。

好吧，很公平，但这有什么重要的？因为作为人类，一个人头脑中能保存的信息是有限的，所以我们需要想出不同的方法来对事物进行逻辑分组和组合，这样我们才能更可靠地构建东西。

那么作曲软件有规则吗？我之前提到过`syntax`，但这不足以指导我们创建组合软件。

软件合成没有硬性规定。我们最接近规则的东西是设计模式。

程序员可能会误用设计模式，因为:

1.  他们是直觉驱动的
2.  编译器不能强制它
3.  正确使用需要一些经验
4.  会导致难以适应的错误抽象。

一些设计模式的例子:

*   工厂模式
*   立面图案
*   递归
*   依赖注入
*   ...

原来逻辑学家/数学家已经研究了这一领域，并提出了法律。这是一个值得更多关注的话题(也许是另一篇博文？)我们需要继续进行`React`。

## 成分在起反应

最优秀的程序员都擅长作曲。创作有效作品的过程大概是这样的:

1.  找出问题域中的基本原语。
2.  使用语言原语和设计模式将它们结合起来解决给定的问题。
3.  基于使用试探法和抽象的变化需求`iterate`。

让我们列出 React 中的原语:

*   也许是最重要的一个也是最基本的单位:`Component`
*   语境
*   生命周期方法
*   状态和道具
*   焦虑
*   参考文献
*   钩子！
*   因为我们用很好的旧 JS 编写 React，所以我们拥有该语言提供的所有功能，如循环、数组、堆栈、生成器等。

因此，作为一名开发者，我们的工作基本上就是尽可能最好地利用上述内容来创建一个应用程序！

React 中最基本的组成单位是`Component`和街区里的新小子`Hooks`。

我们来看看`React`中的一些基本的构图例子。

### 标题组件:

```
const Title = props => <h1>{props.title}</h1> 
```

Enter fullscreen mode Exit fullscreen mode

### 描述组件:

```
const Description = props => <p>{props.description}</p> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将以上两者结合起来创建一个新的组件:

```
const Card = props => (
  <Fragment>
    
    <Description description={props.description} />
  </Fragment>
)

// Usage

<Card title="Composition" description="jibber jabber" /> 
```

Enter fullscreen mode Exit fullscreen mode

我认为以上是我们今天使用 React day 的一种非常直接的方式，也是 React 中作文的一个基本例子。

现在博客的主要部分是:

## 有效成分发生反应

我将以先/后的模式来做这件事，也就是说，我将展示一种做事方式，展示为什么它不好，并展示实现同样目标的更好方式:

## 简单按钮:

```
 function BadButton(props) {
    if (props.primary) {
      return <button className={`btn btn-primary`}>{props.children}</button>;
    }

    if (props.secondary) {
      return <button className={`btn btn-secondary`}>{props.children}</button>;
    }

    return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的组件看起来相当简单，但很快就会变坏，让我们看看它在实践中是如何使用的:

```
 <BadButton primary /> // gives a primary button
    <BadButton secondary /> // gives a secondary button
    <BadButton primary secondary /> // gives what??? 
```

Enter fullscreen mode Exit fullscreen mode

明白我的意思了吧，这里的根本原因是因为我们将按钮的类型建模为布尔值，这很快导致了无效状态。

别担心，这样做我们可以做得更好:

```
 function GoodButton(props) {
  if (props.buttonType === "primary") {
    return <button className={`btn btn-primary`}>{props.children}</button>;
  }
  if (props.buttonType === "secondary") {
    return <button className={`btn btn-secondary`}>{props.children}</button>;
  }

  return null;
}

GoodButton.propTypes = {
  buttonType: PropTypes.oneOf(["primary", "secondary"])
}; 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？只需将其更改为简单的枚举就可以完全消除无效状态(JavaScript 没有枚举，但通过使用简单的字符串和属性类型，我们可以模拟它)

让我们使用上面的组件向前迈进一步:

```
 function PrimaryButton(props) {
    const { buttonType, ...rest } = props;
    return <GoodButton buttonType="primary" {...rest} />;
  }

 function SecondaryButton(props) {
    const { buttonType, ...rest } = props;
    return <GoodButton buttonType="secondary" {...rest} />;
 } 
```

Enter fullscreen mode Exit fullscreen mode

看到我做了什么吗？我用道具创造了新的组件！这有什么好的？它隐藏了如何创建`PrimaryButton`的实现细节，消费者不必担心首先要传递哪些道具才能使其成为`PrimaryButton`。

假设明天你的设计师来了，说`PrimaryButton`需要一个斜体文本，你可以像这样继续添加和修改代码:

```
 function PrimaryButton(props) {
    const { buttonType, ...rest } = props;
    return <GoodButton buttonType="primary" textStyle="itallic" {...rest} />;
  } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，用户不需要改变任何东西！

以下是 codesanbox 链接，其中包含完整代码:

[https://codesandbox.io/embed/w73y3kq93l](https://codesandbox.io/embed/w73y3kq93l)

让我们看看另一个重要的例子，以及其他一些原语。

## 一个下拉组件

现在，像这样的组件通常是以惯用的方式实现的，在这种方式中，我们希望输入的形状符合特定的方式，然后我们将它传递给组件，该组件呈现所需的组件，组件中封装了行为。

大概是这样:

```
function DropDown(props) {
  const [selectedItem, handleSelectedItem] = useState(props.initialValue);
  return (
    <select value={selectedItem} onChange={(e) => handleSelectedItem(e.target.value)}>
      {props.options.map(eachOption => (
        <option value={eachOption.value}>{eachOption.label}</option>
      ))}
    </select>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，组件需要两个道具，即一个`initialValue`和第二个列表选项，如下所示:

```
 const options = [
      {option: 'One', value '1'},
      {option: 'Two', value '2'}
  ]
  // Usage
  <DropDown initialValue="1" options={options} /> 
```

Enter fullscreen mode Exit fullscreen mode

对于大多数基本用例来说，这很好，但很快就很难适应不同的需求:

1.  我们限制`options`以一种特定的方式传递，这就给消费者施加了一个约束，让他们的所有数据都适应这个结构，这是一个人必须做的额外的事情。
2.  假设我们希望禁用第三个选项，我们该怎么做？添加另一个带索引或`id`的道具，很好，但是让我们说你想添加一个搜索栏来过滤你的选项另一个道具？或者如果我们现在想增加选择多个选项的能力，组件会变得越来越复杂，越来越容易出错。
3.  如果我们想在底部以外的其他地方呈现选项，该怎么办？
4.  在一个特定的场景中，我希望选项以相反的方式显示或者按照某种逻辑排序！

看看事情是如何发展的，如果我们像往常一样添加越来越多的道具，我们最终会引入大量的复杂性，并可能引入许多 bug。

## 作文营救！

让我们把上面的重构得更复杂一点。首先，让我们将碎片分解，并从中创建组件:

```
 function DropDown(props) {
  const [selectedItem, handleSelectedItem] = useState(props.initialValue)
  return <select>{props.children}</select>
}

function Option(props) {
  const { value , label, ...rest } = props;
  return <option value={value} {...rest}>{label}</option>
} 
```

Enter fullscreen mode Exit fullscreen mode

我知道上面的实现还不能工作，但是这是我的目标。这解决了大部分问题，即如果你想禁用一个特定的选项，消费者只需将一个禁用标志传递给`Option`组件就行了！如果你仔细想想，应该意识到这条信息的是`Option`组件，而不是父`DropDown`组件。它没有对消费者设置任何限制，选项结构可以是任何形式！

此外，如果你想添加基于某些术语的搜索，我们不需要做任何事情，消费者可以很容易地实现它，因为它现在是可组合的:

```
 <DropDown>
   {options
      .filter(option === option.label.indexOf(searchTerm) !== -1)
      .map(option => <Option {...option}/>)}
</DropDown> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！希望大家清楚构图是如何降低复杂度的？它的工作方式是，基本上向消费者提供各个部分，让他们按照自己需要的方式将它们组合在一起，同时处理核心逻辑部分，也就是说，在这种情况下，在下拉列表中选择一个项目。用于此的奇特术语叫做`inversion of control`。

我们现在知道了我们需要的 API，让我们来填充我们需要的部分，让它按照我们想要的那样工作。到目前为止，我们已经使用了`Hooks`原语，当然还有`Component`，现在我们将使用`Context`来连接现在分离的`DropDown`和`Option`组件。

```
 const DropDownContext = React.createContext('')

function DropDown(props) {
  const [selectedItem, handleSelectedItem] = useState(props.initialValue)
  return (
    <ul className="custom_dropdown">
      <DropDownContext.Provider value={{ selectedItem, handleSelectedItem }}>
        {props.children}
      </DropDownContext.Provider>
    </ul>
  )
}

function Option(props) {
  const { selectedItem, handleSelectedItem } = useContext(DropDownContext)
  return (
    <li
      className="custom_dropdown_item"
      selected={selectedItem === value}
      onClick={() => handleSelectedItem(value)}
      value={props.value}
    >
      {option.label}
    </li>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这应该可以了！请注意，我已经将原生的`select`更改为`ul`和`li`，这已经无关紧要了，因为消费者只会看到`DropDown`和`Option`是如何实现的，这与他们无关！

使用`Context`的最大好处是，您不会受到需要在哪里呈现的限制，逻辑仍然可以工作，也就是说，只要您是提供者的后代，那么在理论上，您可以这样做:

```
<DropDown>
  <Modal>
    <Option value={1} label="One"></Option>
    <Option value={2} label="Two"></Option>
    <Option value={3} label="Three"></Option>
  </Modal>
</DropDown> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，这里我假设我有一个`Modal`组件，它在一个模态弹出窗口中呈现孩子，通过组合`DropDown`、`Modal`、`Option`，我们创建了一个新组件，它在一个模态中呈现选项！根本不需要太多额外的工作。

现在想象一下在第一个实现中做上面的事情:-)，这会增加复杂性，并且可能只在少数需要这种行为的情况下。

所有这一切的本质是，当创建任何组件时，我们无法预测它可以使用的方式的数量，优化组合有助于尽可能不用担心这一点，因为我们提供了组件，消费者可以根据需要使用它们，而不必担心它的核心机制，在这种情况下，选择一个项目，并留给消费者他们想要在哪里以及如何呈现它，正如我们刚才演示的那样。

这就是为什么组合是一个如此有用的命题，无论哪个框架/库设计了他们的 API，我都相信这一点会长久地留在我的脑海中(很明显，同时给出了一个像样的性能！).

API 只是这个方向上的另一个步骤，它给了我们更多的原语来组合，我的思维模型还没有发展到创建如此有效的组合的程度，可能在我使用它一段时间后，我会想到一些东西或者社区会想到(可能是后者！)

所有这些都不是我自己的想法，而是来自于社区里那些了不起的人分享的谈话/博客和其他材料。这里有一些参考资料:

[Ryan 关于复合成分的演讲](https://www.youtube.com/watch?v=hEGg-3pIHlE)

[Kent C Dodd 的演讲题为“简单反应”](https://www.youtube.com/watch?v=AiJ8tRRH0f8)

[与反应挂钩的乐趣](https://www.youtube.com/watch?v=1jWS7cCuUXw&t=1426s)

DropDown 组件的完整工作实现的 Codesandbox:

[https://codesandbox.io/embed/4z4l8r8z97](https://codesandbox.io/embed/4z4l8r8z97)

我希望这是有帮助的，感谢阅读！