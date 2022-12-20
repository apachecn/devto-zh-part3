# 用 React 挂钩构建全局存储

> 原文：<https://dev.to/lexswed/building-global-store-with-react-hooks-2plg>

特雷，特雷，特雷！在这篇文章中，我将讲述我开发全局状态管理解决方案的案例。没有什么突破性的，只是为我的情况开发一个合适的 API 的流程。

首先，如果你不熟悉 [React Context](https://reactjs.org/docs/context.html) 和 [React Hooks](https://reactjs.org/docs/hooks-intro.html) ，是时候和他们成为朋友了。我也可以诚实地说，即使在使用钩子一段时间后，我仍然有一个钩子文档的标签(它会更新！).

## 问题陈述

知道所有其他项目都是直接从 Redux 开始的，我决定推迟编写样板文件，创建许多文件，将 reselect 添加到“依赖项”中。我想要一些简单愚蠢的东西。

## 前钩

我一开始没有钩子，因为我担心它们不会在我的产品发布之前发布，所以我做了非常简单的设置:

```
const context = createContext({field: null});

class DataProvider extends Component {
    state = {
        field: null,
        otherField: { default: 'I am default' }
    }

    getField = async () => {
        const field = await api.getField();
        this.setState({ field });
    }

    render() {
        const value = { data: this.state, getField: this.getField };

        return <context.Provider value={value}>{ this.props.children }</context.Provider>
    }
} 
```

这是[常见的做法](https://reactjs.org/docs/context.html#dynamic-context)，这里没什么特别的。

> 我经常指出，我不会过早地进行性能优化。就像这里，我们在每次`field`更新时创造新的价值，所以即使一些消费者不使用`data.field`，它仍然会得到更新。但这是个问题吗？

后来，我有了一些上下文，它们操作更多的数据，有更多的动作，所以我将上下文值分离到`{ data, api }`(顺便说一下，将商店分离到一个简单的小责任区模块是一个很好的挑战！).

所以我的消费者长这样:

```
 const Consumer = ({ title }) => {
    return (
        <BooksConsumer>
            {({ data: { books }, api: { getBooks } }) => (
                <AuthorsConsumer>
                    {({ data: authors }) => <OurComponent title={title} books={books} getBooks={getBooks} authors={authors} />}
                </AuthorsConsumer>
            )}
        </BooksConsumer>
    );
}; 
```

好吧，看起来很丑。此外，对于每一个上下文，我必须重复所有的创作样板。然后我听说 [hooks 很快](https://github.com/facebook/react/commit/200817599a9a4a5ca4ede377e39387105f0b3318)发布，决定全押。至少要去掉难看(我不这么认为)的渲染函数嵌套。

```
const Consumer = ({ title }) => {
    const {
        data: { books },
        api: { getBooks },
    } = useContext(BooksContext);
    const {
        data: { authors },
    } = useContext(AuthorsContext);

    return <OurComponent title={title} books={books} getBooks={getBooks} authors={authors} />;
}; 
```

> 为了更容易测试，我对组件进行了包装。

所以下一个问题是我必须为每个上下文编写的样板文件。正如[一个聪明人所说的](https://twitter.com/lexswed/status/1073600675312009216)，我们希望将我们的动态模式转移到一个函数中。第一个想法是简单地有这样的东西:

```
function createStore(data, api) {
    const context = createContext(state);

    const Provider = ({ children }) => {
        const [state, updateState] = useState(data);
        const value = {
            data: state,
            api: wrapApi(api, updateState, state),
        };
        return <context.Provider value={value}>{children}</context.Provider>;
    };

    return [context, Provider];
}

function wrapApi(api, updateState, state) {
    return Object.keys(api).reduce((res, key) => {
        res[key] = api[key](updateState, state);
        return res;
    }, {});
} 
```

所以它的用法可以是:

```
 const api = {
    getBooks: (updateState, state) => async () => {
        const books = await fetchBooks();

        updateState({ ...state, books });
    },
};

const state = {
    books: [],
    notBooks: []
}

export const [BooksContext, BooksProvider] = createStore(state, api); 
```

现在，每当我们需要一个新的上下文时——我们用一个简单的 API 使用一个函数。请注意，我将`updateState, state`放在了 api 的闭包中，因此我们的组件可以轻松地从`api`中调用函数。

> 有趣的事实:`api` name 被选中是因为它简短，而不是因为它发出 API 请求🤷🏻‍♂️.

同样，你可能很想做出“优化”:

```
const Provider = ({ children }) => {
        const [state, updateState] = useState(data);
        const value = useMemo(
            () => ({
                data: state,
                api: wrapApi(api, updateState, state),
            }),
            [state, updateState],
        ); // this
        return <context.Provider value={value}>{children}</context.Provider>;
    }; 
```

但是当然它不会让事情变得更好，状态会改变(正如我们希望的那样)，所以我们将需要再次创建新的值并再次绑定 API。

好吧，看起来已经足够了。但是我想要一些调试经验。并不是说我想念 redux-dev-tools 或任何东西...我决定现在使用 reducer，并添加一些强大的调试功能(`console.log` ):

```
function createStore(reducer, actions, initialState) {
    const context = createContext(initialState);

    const Provider = ({ children }) => {
        const [data, dispatch] = useReducer(reducer, initialState);

        const actionCreators = bindActionCreators(actions, dispatch, data); // bind same way as before but add console.log 😅

        return <context.Provider value={[data, actionCreators]}>{children}</context.Provider>;
    };

    return [context, Provider];
} 
```

所以我可以把它当作:

```
 function reducer(state, { action, payload }) {
    switch (action) {
        case SET_BOOKS: {
            return { ...state, books: payload };
        }
        default: {
            return state;
        }
    }
}

const api = {
    getBooks: (dispatch, state) => async () => {
        const books = await fetchBooks();

        dispatch({ action: SET_BOOKS, payload: books });
    },
};

const defaultState = {
    books: [],
    notBooks: [],
};
const [BooksContext, BooksProvider] = createStore(reducer, api, defaultState); 
```

> 哦妈妈...貌似 Redux :c

我们简化一点:

```
function createStore(reducer, actions, initialState) {
    const context = createContext(initialState);

    const Provider = ({ children }) => {
        const reducerFn = (state, { type, payload }) => reducer[type](state, payload); // we create reducer here instead

        const [data, dispatch] = useReducer(reducerFn, initialState);

        const actionCreators = bindActionCreators(actions, dispatch, data);

        return <context.Provider value={[data, actionCreators]}>{children}</context.Provider>;
    };

    return [context, Provider];
} 
```

用法是:

```
 const reducer = {
    [SET_BOOKS]: (state, payload) => ({ ...state, books: payload }), // or just `books` as param
};

const actions = {
    getBooks: (dispatch, state) => async () => {
        const books = await fetchBooks();

        dispatch({ action: SET_BOOKS, payload: books });
    },
};
const [BooksContext, BooksProvider] = createStore(reducer, actions);

// component

const Consumer = () => {
    const [{ books  }, { getBooks }] = useContext(BooksContext);

    // ...usage
} 
```

看起来好多了。特别是对于 TypeScript:我有安全的`actions`，这样您就可以只调度`enum Actions`，然后在 reducer 中使用它。但是为什么比用 redux 好呢？不是但是。

通常，你会从状态库中导入一些辅助函数，比如`connect`表单`react-redux`和你的动作创建器(甚至可能还有`redux-thunk`)。然后，您将您的状态和调度员映射到道具。使用当前的 API，我可以从上下文中选择我已经需要的东西，知道它是什么和来自哪里(单一来源)，不需要文件旅行(如果你不使用 redux ducks)。我只需要做些事情...自己写钩子！

## `useStore`

```
function createStore<D, A extends StoreActions>(
    reducer: StoreReducer<D, any>,
    actions: A,
    initialState?: D,
): Store<D, A> {
    const context = createContext<ContextValue<D, A>>([initialState, actions ? ({} as BoundStoreActions<A>) : null]);

    const StoreProvider: FunctionComponent = ({ children }) => {
                // create Reducer, use Reducer, bind actions
        const store = useStoreProvider(reducer, actions, initialState);

        return <context.Provider value={store}>{children}</context.Provider>;
    };

    const useStore: useStore<D, A> = selector => {
        const [data, actions] = useContext(context);
                 // map state and actions to anything you need (object, array, just one field)
        const selected = selector(data, actions);

        return selected;
    };

    return [context, StoreProvider, useStore];
} 
```

然后在我的组件:

```
const BooksList = ({ authorId ) => {
    const { books, setFavorite } = useBooks((store, actions) => ({
        books: store.books.filter(book => book.authorId === authorId), // feels like selector, isn't it? 🙃
        setFavorite: actions.setFavorite,
    }));
        const authorInfo = useAuthors(store => store.authors[authorId]);

    return (
        <List>
            {books.map(book => (
                <Book key={book.id} title={book.title} author={authorInfo} />
            ))}
        </List>
    );
}; 
```

很整洁，不是吗？

我为什么要对商店采取行动？这意味着每次存储改变时，我的所有动作都必须被绑定，为什么不把它们作为普通函数使用，把它们传递给组件呢？像这样:

```
// modules/books
const [BooksContext, BooksProvider, useBooks] = createStore(reducer, initialState);
// components/Books
import {getBooks, getAuthors, useBooks } from 'modules/books'

const Books = () => {
    const {books, getBooks, getAuthors } = useBooks(store => ({ books: store.data.books, getBooks, getAuthors});
     // I wrote getBooks, getAuthors 3 times :/

    // use it 
```

所以可以做的是传递我想绑定到一个存储的动作，并将它们绑定在`useBooks`中。这意味着它可以是任何动作，甚至与商店无关，因此可以分派商店中不存在的动作类型。但同时它可以允许不同商店的可重用操作，这种情况很少见。此外，商店创建现在更容易了，不需要在创建过程中绑定任何东西。但是这里最主要的事情是你必须导入有名字的动作，然后从`useBooks`中取相同的名字(这可能会让人混淆)或者为它们创造一个新的名字(这很难)。

## 接下来是什么？

问题是我已经对当前的解决方案很满意了(顺便说一句，不是类型，似乎我必须学习如何正确地编写它们)。它工作，没有性能问题**有 9 个以上的不同类型的数据和目的的商店。不过我很想听听大家的意见和解决方案，看看[的源代码](https://github.com/LexSwed/use-pipe)，里面有[的第二个提议](https://github.com/LexSwed/use-pipe/blob/master/src/createStore2.tsx)和[的例子](https://github.com/LexSwed/use-pipe/blob/master/example/createStore2.js)。如果这是你想使用和改进的东西，欢迎你！然后我可以继续工作，添加测试，用法的例子，也许性能调整，像选择器，等等(不是说我不会工作，但无论如何...)!**

那么，你将如何创建你的全球商店呢？