# 挂在 React 挂钩上

> 原文：<https://dev.to/markusclaus/hooked-on-react-hooks-36kh>

每当我读到 React 中的一个新特性时，我都非常喜欢。我试图理解正在发生的事情，以及如何将这个新特性应用到我的项目中。

`Hooks`是 React 的最新特性之一。它是在 React 16.8 中引入的，它允许您在非类的组件中使用状态和生命周期特性。

要使用 React 挂钩，您需要将 React 升级到版本 16.8 或更高版本。不要忘记更新`react-dom`和`react-hot-loader`，如果你使用它的话。如果你不更新热加载程序，你会遇到一些奇怪的错误。

## 国家挂钩

正如你可能知道的，在 16.8 天之前，如果你想使用状态，你需要这样做:

```
 import React, {Component} from 'react';

class Something extends Component {

    constructor(props){
        super(props);

        this.state = {
            foo: 'bar'
        }

        this.setFoo = this.setFoo.bind(this);
    }

    setFoo(newFoo) {
        this.setState({...this.state, foo: newFoo});
    }

    render() {
        const {foo} = this.props;

        return (
            <div>
                <span>{foo}</span>
                <button onClick={this.setFoo('theRealFoo')}>Set Foo</button>
            </div>
        );
    }

}

export default Something; 
```

这个例子包含了使 JavaScript 中的类工作所需的大量代码。为了在正确的上下文中使用`this`,您需要给这个函数一些特殊的关注。同样的例子用钩子看起来应该是这样的:

```
 import React, {useState} from 'react';

const Something = () => {

    const [foo, setFoo] = useState("bar");

    return (
        <div>
            <span>{foo}</span>
            <button onClick={setFoo('theRealFoo')}>Set Foo</button>
        </div>
    );
}

export default Something; 
```

如您所见，代码量显著减少。在本例中,`useState`是 React 提供的一个钩子。它是一个返回状态值的函数，也是一个用来更新状态值的函数。

所以再也不需要`setState`了。React 会处理好一切。

`useState`接受一个参数，该参数是状态字段的初始值。

有时候状态会变得复杂，你可能需要不止一个变量。要做到这一点，您可以使用多个`useState`调用来创建多个状态变量，或者您可以像以前处理类一样使用一个对象。

我尝试了两种方法，我认为单个变量更容易处理，因为你不需要像以前那样做所有的嵌套对象合并。

React 还有另一个名为`userReducer`的钩子，在处理复杂状态时会派上用场。像在 Redux 中一样，您使用一个接受状态的函数和一个更新状态的动作。

```
 import React, {useReducer} from 'react';

const csReducer = (state, action) => {
    switch(action.type) {
        case 'foo': {
            return 'foo';
        }
        case 'bar': {
            return 'bar';
        }
        default: {
            return state;
        }
    }
}

const ComplexSomething = (props) => {
    const [someState, dispatch] = useReducer(csReducer, props.someState);

    return (
        <div>
            <span>{someState}</span>
            <button onClick={dispatch({type: 'foo'})}>Say Foo!</button>
            <button onClick={dispatch({type: 'bar'})}>Say Bar!</button>
        </div>
    )
} 

export default ComplexSomething; 
```

可以看到整个状态处理都在`csReducer`中。它执行操作，并根据类型返回另一个状态值。你也可以发送一个有效载荷`{type: 'someAction', payload: 'foobar!'}`来把一个获取的信息放入状态。如果返回给定的状态，React 不会更改状态，也不会触发任何重新呈现器。

如您所见，状态处理非常棒。要么你用`useState`做一些简单的事情，要么你开始用`useReducer`加大赌注。

## 带挂钩的生命周期

现在我们可以处理我们的状态了，你可能记得我也提到过你可以用钩子做生命周期的事情。我们来谈谈这个。

有一个钩子叫`useEffect`。之所以这样叫，是因为你在生命周期方法中做的大多数事情，获取数据，向 DOM 添加事件或类似的事情，所有这些都被称为“副作用”，因此称为`useEffect`。

我们来举个例子:

```
 import React, {useState, useEffect} from 'react';
import Spinner from './Spinner';

const EffectComponent = (props) => {

    const [pending, setPending] = useState(true);
    const [product, setProduct] = useState({});

    useEffect(() => {
        setPending(true);
        fetch(`https://myapi.api/product/${props.id}`).then((productData) => {
            setProduct(productData);

            setPending(false);
        })
    }, [props.id]);

    if(pending === true) return <Spinner />

    return (
        <div>{product.name}</div>
    )

}

export default EffectComponent; 
```

首先，我们定义两个状态变量`pending`和`product`。然后我们使用`useEffect`来获取数据。该函数将`pending`设置为 true，然后从 API 加载数据。数据到达后，它设置我们的`product`状态，然后`pending`为假，所以我们的组件将呈现。

组件每次渲染时都会调用`effect`。如果你以前使用过`componentDidMount`和`componentWillUpdate`，你就会知道管理数据什么时候应该加载，什么时候不加载是一件痛苦的事情。对于这个问题，有一个简单的解决方案。第二个参数是一个包含变量的数组。只有当数组中的变量发生变化时,`effect`才会触发。在上面的例子中我做了`[props.id]`，所以`effect`只会在每次`props.id`改变时触发。

您也可以在`useEffect`中返回一个函数，该函数将在组件被卸载时被调用。你可以在那里做一些清洁工作。

## 共享有状态逻辑的新途径

在 React 的上一个版本中，有两种方法可以在组件之间共享有状态逻辑。渲染道具和高阶组件。两者都很好，它们工作得很好，但是得到概念...Phuuu...我试图向不同的人解释 100 次，也许 50%的人理解了。

使用 React 16.8 时，可以使用钩子在组件之间共享有状态逻辑。这种方式简单多了，因为钩子只是一个函数...每个人都懂函数，对吗？

为了共享逻辑，我们可以构建自己的定制钩子并调用它们，就像我们调用预构建钩子一样。

```
 import React, {useState} from 'react';

// This is the custom hook
function useFetchData(url, setData) {
    const [pending, setPending] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        setPending(true);
        fetch(url).then((productData) => {
            setData(productData);

            setPending(false);
        }).catch(error =>{
            setError(error);
        })
    }, [url]);

    return {pending, error};
}

const EffectComponent = (props) => {
    const [product, setProduct] = useState({});

    // and here we call it.
    const {pending, error} = useFetchData(`https://myapi.api/product/${props.id}`, setProduct);

    if(pending === true) return <Spinner />

    return (
        <div>
            <span>{product.name}</span>
            {error && <span class="error">{error}</span>}
        </div>
    )

}

export default EffectComponent; 
```

看上面的例子。我所做的是去掉获取数据的逻辑，并把它放到一个函数中，这个函数就是自定义钩子。惯例是每个钩子都以`use`开头，然后是你的函数名。我现在可以简单地使用我的`useFetchData`钩子，而不是重写获取逻辑。

我觉得这个概念好把握多了。你只要把所有东西都放在一个函数里，你调用它`hook`就可以了。

## 钩子的规则

现在，在使用钩子之前，你需要知道一些具体的事情。反动派称之为“钩子规则”。

事实上，只有两个:

1.)你只能在函数组件中使用钩子

这条规则有一个例外。你可以在你的定制钩子中使用钩子。

2.)你不能在任何类型的循环、嵌套函数或条件中使用钩子。

最后一个是因为 React 记得你使用钩子的顺序，它使用这个顺序给你正确的数据或者做正确的事情。

例如:

```
 const [varA, setVarA] = useState(1);
const [varB, setVarB] = useState(2);
const [varC, setVarC] = useState(3); 
```

这很好。无论何时调用组件，挂钩的顺序都是相同的。无论何时使用`varC`，都是值 3。

```
 const [varA, setVarA] = useState(1);
if(varA === 2) {
    const [varB, setVarB] = useState(2);
}
const [varC, setVarC] = useState(3); 
```

这个是个问题。当`varA`为 2 时，钩子的顺序会改变，因此事情会出错。

## 还有其他人

是的，还有其他人...事实上，还有一大堆其他的 React 钩子。但我认为它们是更多的边缘情况挂钩，你可能不经常需要，如果有的话。如果你想在组件的渲染过程中在一个函数内做一些繁重的工作，这可能会派上用场。它需要一个函数和一组值。该函数仅在数组中的值发生变化时运行。否则它将返回记忆值。[你可以在这里阅读记忆。](https://en.wikipedia.org/wiki/Memoization)

不过，有一个钩子看起来很有趣。我不得不对此做更多的测试，但我认为它会比现在更容易使用 Reacts 的上下文 API。

好了，这个帖子到此为止。我希望你学到了一些东西，我希望你至少喜欢它一点点。如果你有建议或意见，请随意发表。

感谢您的阅读！