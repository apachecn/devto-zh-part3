# 为什么反应钩子，我们是怎么到这里的？

> 原文：<https://dev.to/yurkaninryan/why-react-hooks-and-how-did-we-even-gethere-152f>

[React 钩子](https://reactjs.org/docs/hooks-overview.html)在这里，我立刻喜欢上了它们。为了理解为什么钩子是伟大的，我想看看我们是如何解决 React 历史上的一个常见问题会有所帮助。

情况是这样的。我们需要构建一个按钮，允许用户通过它发送和接收消息。它必须显示旧消息，监听新消息，并相应地更改文档标题来提醒它们。

在这个练习中，让我们忽略表象部分。我们将关注围绕数据的逻辑。我们可以从创建一个组件外壳并在挂载时获取数据开始。

```
class Intercom extends Component {
  state = { messages: [] };

  /* Once this component is rendered, it will grab all the old messages */
  componentDidMount() {
    API.getMessages(this.props.user.id, data =>
      this.setState({ messages: data.messages })
    );
  }

  render() {
    return (...);
  }
} 
```

很简单。现在我们需要为新消息设置订阅，并在卸载时进行清理。

我们应该为新消息保留一个单独的状态键，因为我们将来需要区分。

```
class Intercom extends Component {
  state = {
    newMessages: [],
    messages: []
  };

  updateNewMessages = data => {
    this.setState({
      newMessages: [...data.messages, ...this.state.newMessages]
    });
  };

  componentDidMount() {
    API.getMessages(this.props.user.id, data =>
      this.setState({ messages: data.messages })
    );

    API.subscribeToNewMessages(this.updateMessages);
  }

  componentWillUnmount() {
    API.unsubscribeFromNewMessages(this.updateMessages);
  }

  render() {
    return (...);
  }
} 
```

现在我们在两个行为上，组件开始变得有点大，但是还没有*那么*糟糕。但是请注意，高光彼此并不靠近，实际上已经开始断裂。

继续，是时候处理文档标题了。我们可以存储原始的文档标题，并在收到新消息时更新它。

```
class Intercom extends Component {
  state = {
    newMessages: [],
    messages: [],
    originalDocumentTitle: document.title,
  };

  updateNewMessages = data => {
    this.setState({
      newMessages: [...data.messages, ...this.state.newMessages]
    });
  };

  componentDidMount() {
    API.getMessages(this.props.user.id, data =>
      this.setState({ messages: data.messages })
    );

    API.subscribeToNewMessages(this.updateMessages);
  }

  componentDidUpdate() {
    document.title = this.state.newMessages.length ?
      'New Messages' :
      this.state.originalDocumentTitle
  }

  componentWillUnmount() {
    API.unsubscribeFromNewMessages(this.updateMessages);
  }

  render() {
    return (...);
  }
} 
```

好了，现在这个文件开始变得有点大了，并且越来越难看到这三种行为的全貌。

如果您已经在 React 中工作了一段时间，您会知道我们已经有了在类组件中处理这个问题的技术。如果你不知道该怎么做，你可能会继续增加行为，陷入技术债务。

默认情况下，钩子对组件有有趣的影响。**看看上面的代码如果用钩子**编写会是什么样子。我将突出显示与新消息行为相关的代码。

```
function Intercom(props) {
  /* Fetch old messages */
  const [messages, setMessages] = useState([]);
  useEffect(
    () => API.getMessages(props.user.id, setMessages),
    [props.user.id]
  );

  /* Watch for new ones */
  const [newMessages, setNewMessages] = useState([]);
  useEffect(
    () => {
      API.subscribeToNewMessages(props.user.id, setNewMessages);

      return () =>
        API.unsubscribeFromNewMessages(props.user.id, setNewMessages);
    },
    [props.user.id]
  );

  /* Update document title as needed */
  const [originalTitle] = useState(document.title);
  useEffect(
    () => {
      document.title = newMessages.length
        ? "You have new messages!"
        : originalTitle;
      return () => (document.title = originalTitle);
    },
    [newMessages.length]
  );

  return (...);
} 
```

一切都是通过行为而不是生命周期挂钩聚集在一起的。这使得调试和重构的故事变得更短。你不需要从一个生命周期钩子跳到另一个生命周期钩子，取出一些逻辑来移动。

现在我们有了这三个清晰的逻辑束，我们可以利用*定制钩子*，并编写我们自己的钩子，以可重用的方式抽象出行为。

```
function useMessages(id) {
  const [messages, setMessages] = useState([]);
  useEffect(
    () => API.getMessages(props.user.id, setMessages),
    [id]
  );

  return messages;
}

function useNewMessages(id) {
  const [messages, setMessages] = useState([]);
  useEffect(
    () => {
      API.subscribeToNewMessages(id, setMessages);

      return () =>
        API.unsubscribeFromNewMessages(id, setMessages);
    },
    [id]
  );

  return messages;
}

function useTemporaryDocumentTitle(temporary) {
  const [original] = useState(document.title);
  useEffect(
    () => {
      document.title = temporary || original;
      return () => document.title = original;
    },
    [temporary]
  );
} 
```

我们可以将这些自定义挂钩导入到我们的组件中，现在我们之前复杂的内部通信组件看起来像这样:

```
function Intercom(props) {
  const messages = useMessages(props.user.id);
  const newMessages = useNewMessages(props.user.id);
  useTemporaryDocumentTitle(
    newMessages.length ? "You have new messages!" : null
  )

  return (...);
} 
```

所以钩子可以帮助我们把行为放进一个可重用的盒子里，但是你知道我们已经在这么做了吗？随着时间的推移，我们已经慢慢地进化了，因为已经发现的每一种技术都有一些问题。

让我们来看看每一个，检查他们的权衡，并思考可以做些什么来改善。

### Mixins

混血儿受到了很多指责。他们为将生命周期挂钩组合在一起描述一个效果做好了准备。

```
const NewMessagesMixin = {
  getInitialState() {
    return {
      newMessages: []
    }
  },

  updateNewMessages(data) {
    this.setState({
      newMessages: [...data.messages, ...this.state.newMessages]
    });
  };

  componentDidMount() {
    API.subscribeToNewMessages(this.updateMessages);
  }

  componentWillUnmount() {
    API.unsubscribeFromNewMessage(this.updateMessages)
  }
}

const Intercom = React.createClass({
  mixins: [NewMessagesMixin, MessagesMixin, DocumentTitleMixin],

  /* Rest of our component would go here! */
}) 
```

虽然封装逻辑的总体想法很棒，但我们最终还是从 mixins 中学到了一些重要的东西。

不清楚`this.state.newMessages`从何而来。对于 mixin，mixin 也有可能盲目地依赖于组件中存在的属性。

随着人们开始包含和扩展大量的 mixins，这就成了一个大问题。你不能简单地在一个文件中搜索，然后假设你没有在其他地方破坏什么东西。

还有，你不能轻易在另一个 mixin 中使用`this.state.newMessages`。它们不构成，所以你不能把它插到其他封装里。

重构需要简单。**这些混进的行为需要更明显的不属于组件。**他们不应该使用组件的内部构件。🙅‍

Mixins 也很难类型化和测试，这使得它们更难在一个为变化而优化的环境中工作。

### 高阶组件

我们可以实现类似的效果，并通过创建一个传入道具的容器来使它不那么神奇！继承的主要缺点是它使重构变得更加困难，所以让我们试试复合吧！

```
/* Function that takes a component and returns a component! */
function withNewMessages(Component){
  return class withNewMessage extends Component {
    state = { newMessages: [] }

    updateNewMessages = data => {
      this.setState({
        newMessages: [...data.messages, ...this.state.newMessages]
      });

      componentDidMount() {
        API.subscribeToNewMessages(this.updateMessages);
      }

      componentWillUnmount() {
        API.unsubscribeFromNewMessage(this.updateMessages)
      }

      render() {
        return (<Component {...this.props} {...this.state} />)
      }
    };
  }
}

class Intercom extends Component {
  /* Component that uses newMessage, messages props*/
}

const DecoratedIntercom = withNewMessages(
  withMessages(
    withDocumentTitle(
      Intercom
    )
  )
) 
```

虽然这是更多的代码，但我们正朝着正确的方向前进。我们拥有混合蛋白的所有好处。现在我们有了一个不再与订阅行为紧密耦合的`<Intercom />`组件。

测试变得更容易了，但是打字仍然没有它应有的强大。它们可以组合在一起，但是在 props 中仍然可能存在名称空间冲突。

### 渲染道具&儿童作为功能

这是一直摆在我们面前的模式。我们想要的只是一个处理订阅新消息行为的组件，以及呈现我们想要的任何内容的能力。

```
class NewMessages extends Component {
  state = { newMessages: [], }

  updateNewMessages = data => {
    this.setState({
      newMessages: [...data.messages, ...this.state.newMessages]
    });
  }

  componentDidMount() {
    API.subscribeToNewMessages(this.updateMessages);
  }

  componentWillUnmount() {
    API.unsubscribeFromNewMessage(this.updateMessages)
  }

  render() {
    return this.props.children(this.state)
  }
}

class Intercom extends Component {
  render() {
    <NewMessages>
      {({ newMessages, }) => (
        // Write code that uses them here!
      )}
    </NewMessages>
  }
} 
```

**这种细微的差异有一些非常棒的好处**

*   现在非常明显的是什么在提供`newMessages`。您还可以轻松地重命名它们，以防止名称冲突。
*   我们可以灵活控制渲染的内容。我们不需要制作新的组件，如果我们决定这样做，这只是一个简单的复制粘贴。
*   您可以在组件呈现函数中直接看到所有这些。这是显而易见的，新开发人员很容易识别。在这里结帐。

然而，它造成了一种虚假的等级感。仅仅因为一个行为“嵌套”在另一个行为之下，并不意味着它依赖于父行为。

此外，当您必须添加或删除一个行为时，重构是一件痛苦的事情。祝你重构类似这样的东西好运:

```
const MyForm = () => (
  <DataFetcher>
    {({ data, }) => (
      <Actions>
        {({ actions, }) => (
          <Translations>
            {({ translations, }) => {
              <Theme>
                {({ theme, }) => (
                  <form styles={theme.form}>
                    <input type="text" value={data.value} />
                    <button onClick={actions.submit}>
                      {translations.submit}
                    </button>
                  </form>
                )}
              </Theme>
            }}
          </Translations>
        )}
      </Actions>
    )}
  </DataFetcher>
) 
```

### 用钩子记住的东西

当使用钩子时，你必须记住一些规则，这些规则乍一看可能很奇怪:

⚠️你应该在渲染函数的顶层调用钩子。

这意味着没有条件挂钩。我们与 React 的合同是，我们将每次以相同的顺序调用相同数量的钩子。

当你比较 Mixins 和 HOCs 的工作方式时，这个规则开始变得更有意义。您不能有条件地使用它们并在每次渲染时对它们重新排序。

如果你想要条件效果，你应该把你的钩子分割成其他组件，或者考虑不同的模式。

⚠️ **你只能在 React 函数组件和自定义钩子中使用钩子。**

我不确定是否有任何技术上的原因不尝试在常规函数中调用它们。这确保了数据在组件文件中始终可见。

⚠️**component did catch 或 getSnapshotBeforeUpdate 没有钩子原语。**

React 小组说他们已经在路上了！

对于 componentDidCatch 用例，您可以创建一个错误边界组件，getSnapshotBeforeUpdate 有点棘手，但幸运的是非常少见。

### 一些最后的笔记

我毫不怀疑 hooks 将会改变我们看待事物的方式，并动摇一些最佳实践。兴奋和图书馆出来的数量是鼓舞人心的！

然而，我在过去已经看到了对所有这些设计模式的大肆宣传。虽然大多数最终成为我们工具箱中非常有价值的工具，但它们都是有价格的。

我仍然没有完全理解钩子的权衡，这让我害怕。我强烈建议和他们一起玩，通过例子来学习。不过，在对它们进行全面重写之前，您可能需要等待一段时间😉

* * *

如果您有任何问题或正在寻找一对一的 React 导师，请随时发推特给我**[@ yurkaniryan](https://dev.to/yurkaninryan)**！

祝你好运，编码快乐！！😄