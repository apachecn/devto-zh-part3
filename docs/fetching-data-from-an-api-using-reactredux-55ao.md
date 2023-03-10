# 使用 React/Redux 从 api 获取数据

> 原文：<https://dev.to/markusclaus/fetching-data-from-an-api-using-reactredux-55ao>

# 首发简单

这是我在这里的第一个帖子。我决定分享一些我从犯你可能犯的每一个错误中学到的知识。-我在这里写的一切都是我通过阅读博客帖子、试图理解所做的事情以及试错而学到的。如果有错误或者你想到了更好的方法，请在评论中告诉我。我总是感谢有用的提示！

现在，首先要做的是。你需要安装 React 和 Redux。我想你知道怎么做。设置好 React 应用程序后，您需要使用命令`npm install redux-thunk`安装一个名为 redux-thunk 的工具

安装完所有这些组件后，我们现在可以看看实现这一奇迹所需的组件了！

# 这 thunk 是什么东西？

基本上，thunk 是一个被另一个函数调用的函数。等待...什么？是啊，我第一次听到这种说法的时候就是这个反应。让我给你看一个例子:

```
function some_function() {
    // do something
    return function thunk() {
        // do something thunky later
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，`some_function`被调用，它执行一些操作，然后返回一个新函数，其中包含命令和可能的数据，供以后执行。

# 现在 Redux 呢？

我不想深入 redux 的最深处(反正我很可能做不到)，所以简单解释一下:它是 javascript 应用程序的状态容器。它将应用程序所需的所有数据保存在一个地方。应用程序中的每个组件在它寻找数据的状态容器中都有自己的空间。当数据改变时，组件也会改变。

## 动作

其思想是您将动作分派到 redux 上，并基于这些动作修改状态。

可笑的是:一个动作什么都不做。听起来好像有事情发生，但事实并非如此。动作只是一个带有`type`键的普通对象。比如这个:

```
// this is an action
{
    type: "SOME_ACTION",
    payload: {}
} 
```

Enter fullscreen mode Exit fullscreen mode

很多时候你不想一遍又一遍的写同一个对象，所以有个概念叫动作创建者。

## 动作创建者

动作创建者做的和他们听起来的一样，他们为你创建动作对象。

```
const SOME_ACTION = "SOME_ACTION";

function create_action(data) {
    return {
        type: SOME_ACTION,
        payload: data
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以有了这些动作创建器，你现在可以通过调用`create_action(data)`来轻松使用`SOME_ACTION`。那些动作创建者可以通过使用`dispatch(create_action(data))`被派遣到 redux。

## 减速器

在一个动作被分派之后，它将被传递到一个所谓的缩减器上。缩减器是一个给定了状态和动作的函数。根据动作，它将转换状态，然后返回新状态。

```
function someReducer(state, action) {
    switch(action.type) {
        case SOME_ACTION:
            return {
                ...state,
                data: action.payload
            }
        break;

        default:
            // the dispatched action is not in this reducer, return the state unchanged
            return state;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

更复杂的应用程序很可能有多个 reducerss，每个 reducer 负责状态的一个部分。因此，重要的是永远不要忘记默认情况下，减速器返回的状态不变。

重要的是要注意，减速器是纯函数。他们从不调用 API 之类的东西，也不会向 redux 分派另一个动作。

# 你讲了 thunks！？

你还记得。好吧，再想想。我刚才提到了还原剂是纯的。但是，我们经常希望有某种 API 调用，或者根据数据或其他什么来调度一些东西...但是我们不能...还原剂是纯净的...Redux-Thunk 来救援！

Redux-Thunk 非常容易理解。它是 redux 商店的一个所谓的中间件。它查看每一个被调度的动作，如果是一个函数，它就调用这个函数。没什么更多的了。但是这打开了一个全新的世界，一个被发送到 redux 的奇特的“动作”的世界。

你可能会问，我如何让这个小奇迹进入我的商店？

```
import { applyMiddleware, createStore } from 'redux';
import thunk from 'redux-thunk';

import rootReducer from './rootReducer';
import initialState from './initialState';

const middlewares = [thunk];

createStore(rootReducer, initialState, applyMiddleware(...middlewares)); 
```

Enter fullscreen mode Exit fullscreen mode

## 我们去拿些产品

我们想从我们的 API 加载一些产品。为此，我们首先将组件设置为某种挂起状态，我们显示一个加载微调器或类似的东西。然后我们加载数据，决定是否只显示产品列表或显示某种错误信息-

我们从设置动作创建器开始。

```
 // action.js

export const FETCH_PRODUCTS_PENDING = 'FETCH_PRODUCTS_PENDING';
export const FETCH_PRODUCTS_SUCCESS = 'FETCH_PRODUCTS_SUCCESS';
export const FETCH_PRODUCTS_ERROR = 'FETCH_PRODUCTS_ERROR';

function fetchProductsPending() {
    return {
        type: FETCH_PRODUCTS_PENDING
    }
}

function fetchProductsSuccess(products) {
    return {
        type: FETCH_PRODUCTS_SUCCESS
        products: products
    }
}

function fetchProductsError(error) {
    return {
        type: FETCH_PRODUCTS_ERROR
        error: error
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了动作创建者，让我们为整个事情设置一个缩减器。

```
 // reducer.js

import {FETCH_PRODUCTS_PENDING, FETCH_PRODUCTS_SUCCESS, FETCH_PRODUCTS_ERROR} from './actions';

const initialState = {
    pending: false,
    products: [],
    error: null
}

export function productsReducer(state = initialState, action) {
    switch(action.type) {
        case FETCH_PRODUCTS_PENDING: 
            return {
                ...state,
                pending: true
            }
        case FETCH_PRODUCTS_SUCCESS:
            return {
                ...state,
                pending: false,
                products: action.payload
            }
        case FETCH_PRODUCTS_ERROR:
            return {
                ...state,
                pending: false,
                error: action.error
            }
        default: 
            return state;
    }
}

export const getProducts = state => state.products;
export const getProductsPending = state => state.pending;
export const getProductsError = state => state.error; 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们已经完成了大部分工作。

在上面的代码中需要注意的是 reducer 末尾的三个函数。这些被称为选择器。选择器用于获取状态的定义部分。在小型应用中，它们是多余的。但是如果你扩展你的应用程序，它变得越来越复杂，如果你在你的状态中改变一些东西，它会变得非常混乱。有了选择器，你需要改变选择器，一切正常。

我可能会写一篇关于选择器的博文，因为我认为它们对于建立一个可伸缩的 react/redux 应用程序非常重要。

我们说到哪了...啊，是的，大部分工作已经完成。在 redux 方面，我们唯一要做的事情就是编写一个我们喜欢的新动作。

```
 // fetchProducts.js

import {fetchProductsPending, fetchProductsSuccess, fetchProductsError} from 'actions';

function fetchProducts() {
    return dispatch => {
        dispatch(fetchProductsPending());
        fetch('https://exampleapi.com/products')
        .then(res => res.json())
        .then(res => {
            if(res.error) {
                throw(res.error);
            }
            dispatch(fetchProductsSuccess(res.products);
            return res.products;
        })
        .catch(error => {
            dispatch(fetchProductsError(error));
        })
    }
}

export default fetchProducts; 
```

Enter fullscreen mode Exit fullscreen mode

上面的操作非常简单。首先，我们发送我们的未决诉讼。然后我们从 API 中获取数据。我们解码 json 进入一个对象。然后我们检查错误。如果有错误发生，我们抛出它并调用我们的错误函数。如果一切顺利，我们称之为成功行动。减速器处理其余部分。

这都是关于从服务器获取数据...不，开玩笑的，不是的。但是大多数关于从 api 获取数据的帖子都是这样结束的，对吗？但是...

# 我们的应用呢？

哦，你想让你商店里的商品在你的 react 应用上显示出来吗？好吧好吧，我们开始吧。

我假设您知道如何使用提供程序将 react 应用程序连接到 redux 商店。有很多关于这个话题的帖子。完成这些之后，您将需要一些组件。

对我来说，一切都始于风景。对我来说，视图是一个组件，它将用户得到的所有东西包装到一个父组件中。这个父组件拥有到 redux 存储的大部分连接，并与其封装的组件共享数据。

```
 import React, { Component } from 'react';
import PropTypes from 'prop-types';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';

import fetchProductsAction from 'fetchProducts';
import {getProductsError, getProducts, getProductsPending} from 'reducer';

import LoadingSpinner from './SomeLoadingSpinner';
import ProductList from './ProductList';

class ProductView extends Component {
    constructor(props) {
        super(props);

        this.shouldComponentRender = this.shouldComponentRender.bind(this);
    }

    componentWillMount() {
        const {fetchProducts} = this.props;
        fetchProducts();
    }

    shouldComponentRender() {
        const {pending} = this.props;
        if(this.pending === false) return false;
        // more tests
        return true;
    }

    render() {
        const {products, error, pending} = this.props;

        if(!this.shouldComponentRender()) return <LoadingSpinner />

        return (
            <div className='product-list-wrapper'>
                {error && <span className='product-list-error'>{error}</span>}
                <ProductList products={products} />
            </div>
        )
    }
}

const mapStateToProps = state => ({
    error: getProductsError(state),
    products: getProducts(state),
    pending: getProductsPending(state)
})

const mapDispatchToProps = dispatch => bindActionCreators({
    fetchProducts: fetchProductsAction
}, dispatch)

export default connect(
    mapStateToProps,
    mapDispatchToProps
)(ProductView ); 
```

Enter fullscreen mode Exit fullscreen mode

所以，这里发生了很多事情。我们编写一个标准的 React 组件。然后我们用`connect`把它连接到我们的 redux store。`Connect`有两个参数:一个函数`mapStateToProps`将部分状态映射到你的组件道具中，另一个函数`mapDispatchToProps`将函数映射到你的道具中，这些道具在被调用时会被分派给 redux。

就在最后，我们把所有这些东西放在一起，瞧，我们就有了一个到我们商店的连接。

在 mapStateToProps 函数中，我们使用了之前编写的选择器。

我喜欢在我的视图组件和大多数组件中添加一个名为`shouldComponentRender`的函数。我这样命名，是因为它接近 react 的`shouldComponentUpdate`生命周期方法。它检查组件是否应该呈现。如果没有，它将呈现一个 LoadingSpinner 组件。

我发现这样工作非常有益，因为组件总是被重新初始化，并且所有子组件在控制渲染的 pending 标志切换后被再次挂载。因此，您可以在构造函数中将 redux 状态添加到组件的状态中。(我不想说什么进入 redux，什么进入组件状态，这是另一个帖子的话题)。

在我的大多数项目中，我发现这是最烦人的问题之一。想象一个呈现产品的组件。它由数据初始化，然后一些子组件(如价格计算器，它有一个组件状态)在其构造函数中初始化。当新数据进来时，您需要检查计算器是否需要重新初始化。使用`shouldComponentRender`功能，这样做非常简单。每次挂起标志切换时(可能是因为选择了新产品)，所有东西都重新初始化，一切就绪。

当然，有些原因会导致视图中的组件不需要重新渲染。如果是这种情况，只需从视图中移除`shouldComponentRender`函数，并在子组件中使用它。

您可以使用某种淡出/-效果来改善用户体验。

嗯，就是这样。这次是真的。

感谢你阅读我的第一篇博文。我希望你喜欢它，我希望你学到了一些东西，如果你有任何建议或提示给我，以提高我的反应/还原技能，或者只是想说“嗨”，给我留下一些评论，我真的很喜欢。