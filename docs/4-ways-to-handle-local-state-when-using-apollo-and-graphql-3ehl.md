# 使用 Apollo 和 GraphQL 时处理本地状态的 4 种方法

> 原文：<https://dev.to/jkettmann/4-ways-to-handle-local-state-when-using-apollo-and-graphql-3ehl>

*原载于[jkettmann.com](https://jkettmann.com/4-ways-to-handle-local-state-when-using-apollo-and-graphql)T3】*

在反应型生态系统中，状态管理有多种选择。使用 Apollo 和 GraphQL 是处理服务器端数据的一种很好的方式，只需要很少的样板文件。但是这个社区还很年轻，通常还没有建立一个最佳实践。这里的问题是如何处理客户端状态。有哪些解决方案，利弊如何？

## 用 Apollo 处理客户端状态

使用 Apollo-Link-State，可以选择用 Apollo 本身处理本地状态。但对许多人来说，这仍然是一个谜。文档在很多情况下都很冗长，调试也很困难。同时，将所有数据放在同一个位置，而不引入另一种编程范式，这很好。Apollo 作为客户端状态管理系统的最佳用例之一是当您需要用本地状态增强服务器端数据时。

让我们快速看一下[中的一个例子，这篇文章更详细地介绍了如何组合服务器和本地数据](https://jkettmann.com/combining-server-side-data-and-local-state-with-apollo-link-state/)。我们有一个 GraphQL 服务器，它公开了以下类型的书籍列表。

```
type  Book  {  id:  String!  author:  String!  title:  String!  }  type  Query  {  books:  [Book]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在的目标是用一个客户端布尔标志来扩展 Book 类型，以指示它是否已被用户选中。我们可以通过向 Apollo 提供者传递一个`clientState`对象来实现这一点。`selected`标志由`Book`解析器添加，默认为`false`。我们还实现了一个`toggleBook`突变。这将从 Apollo 缓存中获取某个 ID 的现有图书数据，并切换`selected`标志。

```
import React from 'react';
import gql from 'graphql-tag';
import ApolloClient from 'apollo-boost';
import { ApolloProvider } from 'react-apollo';

import BookList from './BookList';

const clientState = {
  resolvers: {
    Book: {
      selected: (book) => book.selected || false,
    },
    Mutation: {
      toggleBook: (_, args, { cache, getCacheKey }) => {
        const id = getCacheKey({ id: args.id, __typename: 'Book' });
        const fragment = gql`
          fragment bookToSelect on Book {
            selected
          }
        `;
        const book = cache.readFragment({ fragment, id });
        const data = { ...book, selected: !book.selected };
        cache.writeFragment({ fragment, id, data });
        return null;
      },
    },
  },
};

const client = new ApolloClient({
  uri: 'http://localhost:4000/graphql',
  clientState,
});

const App = () => (
  <ApolloProvider client={client}>
    <BookList />
  </ApolloProvider> ); 
```

Enter fullscreen mode Exit fullscreen mode

图书列表包括用`@client`指令注释的查询中的`selected`标志。这表明 Apollo 客户机需要在客户机上解析这些数据。

```
import React from 'react';
import { Query } from 'react-apollo';
import gql from 'graphql-tag';
import Book from './Book';

const BOOKS_QUERY = gql`
  query {
    books {
      id
      author
      title
      selected @client
    }
  }
`;

const BookList = () => (
  <Query query={BOOKS_QUERY}>
    {
      ({ data }) => data.books && (
        <React.Fragment>
          {data.books.map(book => (
            <Book key={book.id} {...book} />
          ))}
        </React.Fragment>
      )
    }
  </Query> ); 
```

Enter fullscreen mode Exit fullscreen mode

book 组件调用`toggleBook`突变，并将其 ID 作为变量提供。在突变的定义中，我们再次使用了`@client`指令。

```
import React from 'react';
import gql from 'graphql-tag';
import { Mutation } from 'react-apollo';
import './Book.css';

const SELECT_BOOK_MUTATION = gql`
  mutation {
    toggleBook(id: $id) @client
  }
`;

const Book = ({ id, author, title, selected }) => (
  <Mutation mutation={SELECT_BOOK_MUTATION}>
    {
      toggleBook => (
        <p
          className={selected ? 'selected' : 'not-selected'}
          onClick={() => toggleBook({ variables: { id } })}
        >
          {title} by {author}
        </p>
      )
    }
  </Mutation> ); 
```

Enter fullscreen mode Exit fullscreen mode

像这样将服务器数据和本地数据结合起来，就可以在我们的组件中获得一致的数据。我们可以将本地数据保存在一个单独的存储中，就像 Redux 存储中的一组选定的图书 id 一样。但是我们必须检查每本书，看它是否包含在这个数组中。当然，这本身并不是什么大事。但是，如果您考虑到编写读写逻辑以将数据输入和输出存储的额外开销，那么将 Apollo 用于客户端状态管理是非常值得考虑的。

如果您想更详细地了解这个问题和一个更复杂的例子，请查看这篇关于用 Apollo 结合服务器端数据和本地状态的文章。

## 用 React 上下文处理全局客户端状态

对于拥有与服务器端数据无关的本地状态的情况，上面的例子可能显得有些多余。在很多情况下，内置的 React API 实际上已经足够了。让我们来看看常见的用例:模态窗口。这可能不是实现模态的最佳方式，但却是使用 React 上下文 API 的一个很好的例子。

我们用一个`Modal`组件、它的上下文和一个打开它的按钮来扩展上面的例子。`App`组件使用它的本地状态来存储关于模态是否打开的信息。它还具有允许将`isModalOpen`标志切换到`true`的功能。标志和函数被传递给模式的上下文提供者。

```
import React from 'react';
import ApolloClient from 'apollo-boost';
import { ApolloProvider } from 'react-apollo';
import Modal, { ModalContext } from '../Modal';
import OpenModalButton from '../OpenModalButton';
import BookList from '../BookList';

const client = new ApolloClient({
  uri: 'http://localhost:4000/graphql',
});

class App extends React.Component {
  state = {
    isModalOpen: false,
  }

  openModal = () => {
    this.setState({ isModalOpen: true });
  }

  render() {
    const { isModalOpen } = this.state;
    const openModal = this.openModal;
    return (
      <ApolloProvider client={client}>
        <ModalContext.Provider value={{ isModalOpen, openModal }}>
          <BookList />

          <OpenModalButton />
          <Modal />
        </ModalContext.Provider>
      </ApolloProvider>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

模态本身通过`React.createContext`定义上下文。`Modal`组件使用上下文的消费者来访问在`App`组件中定义的上下文值。仅当`isModalOpen`标志被置位时，它才呈现实际模态。

```
import React from 'react';

const defaultContext = {
  isModalOpen: false,
  openModal: () => ({ isModalOpen: true }),
};

export const ModalContext = React.createContext(defaultContext);

const Modal = () => (
  <ModalContext.Consumer>
    {
      ({ isModalOpen }) => isModalOpen && (
        <div className="modal">
          This is a modal
        </div>
      )
    }
  </ModalContext.Consumer> ); 
```

Enter fullscreen mode Exit fullscreen mode

`OpenModalButton`组件还使用模态上下文的消费者来访问在`App`组件中定义的`openModal`函数。点击按钮后，`App`组件状态中的`isModalOpen`标志被切换，模态窗口变得可见。

```
import React from 'react';
import { ModalContext } from '../Modal';

const OpenModalButton = () => (
  <ModalContext.Consumer>
    {
      ({ openModal }) => (
        <button onClick={openModal}>
          Open Modal
        </button>
      )
    }
  </ModalContext.Consumer> ); 
```

Enter fullscreen mode Exit fullscreen mode

对客户端状态使用 React 的上下文 API 很简单，如果您以前从未使用 Apollo 进行本地状态管理，实现起来可能会容易得多。如果你对如何使用 Apollo 实现这个模态窗口感兴趣，你可以参考这篇文章。

## 组件状态为简单用例

使用 React 的上下文 API、Apollo 或其他管理全局状态的解决方案都是有效的方法。但是在很多情况下使用简单的组件状态就足够了。当状态的范围仅限于单个组件时，为什么要冒险进行全局重新渲染呢？

在下面的例子中，我们只想在组件中显示一个小的信息框。使用全局状态在这里有些夸张，因为它的实现和维护更加复杂。

```
import React from 'react';

class SomeComponent extends React.Component {
  state = {
    isInfoBoxOpen: false,
  }

  openInfoBox = () => {
    this.setState({ isInfoBoxOpen: true });
  }

  render() {
    return (
      <div className="container">
        <button onClick={this.openInfoBox}>
          Open info box
        </button>
        {
          this.state.isInfoBoxOpen && <InfoBox />
        }
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第三方状态管理解决方案

如果你仍然需要不同的状态管理解决方案，你当然可以使用像 [Redux](https://redux.js.org/) 或 [Mobx](https://mobx.js.org/) 这样的软件包。缺点是引入了新的依赖项和额外的编程范例。同时，您添加了另一个数据源，这使得在需要时合并来自两个数据源的数据变得更加复杂。

## 结论

如果您不想完全迁移到 Apollo，使用上下文 API 或组件状态可以涵盖本地状态管理的大多数情况。一开始使用 Apollo 可能有点复杂和冗长，但是当您需要用客户端状态扩展服务器端数据时，它是一个很好的解决方案。在其他情况下，这可能有些过头了，但至少您会受益于能够使用 Apollo 开发工具。