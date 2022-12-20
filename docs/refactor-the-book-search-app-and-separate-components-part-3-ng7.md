# 重构图书搜索应用程序并分离组件，第 3 部分

> 原文：<https://dev.to/paramharrison/refactor-the-book-search-app-and-separate-components-part-3-ng7>

到 [part 2](https://dev.to/paramharrison/hooked-with-react-error-handling-and-loading-state-in-react-hooks-part-2-4f53) 为止，我们已经创建了一个图书搜索 app，并处理了加载和错误状态。现在，我们将看看如何将应用程序结构拆分成组件。

我们已经在`App.js`写好了整个 app。这仍然是一个小例子，所以没有必要把它分成文件夹。这篇文章将展示如何拆分大型应用程序，而不会导致以后的调试混乱。

文件夹结构没有通用的最佳实践，它主要取决于两个因素

*   项目有多大？
*   你的团队有多大？

对于大型项目和大型团队来说，基于特性或域的文件夹结构将会工作得很好。对于小型项目，基于文件类型的文件夹结构将很容易工作。

你可以在 react 文档[这里](https://reactjs.org/docs/faq-structure.html)了解更多信息

我个人的看法是，只在需要的时候，保持简单、扁平、有规模。当每个文件的长度增加时，您应该总是重构复杂的文件夹结构。

让我们继续看代码，

在我们的图书搜索应用程序中，我们可以创建这些组件

*   图书搜索格式
*   装货设备
*   书籍列表
*   书

让我们创建一个组件文件夹，并为我们的组件创建三个 JS 文件。

```
// booksSearchForm.js
import React from 'react';

const BookSearchForm = ({
  onSubmitHandler,
  searchTerm,
  onInputChange,
  error,
}) => {
  return (
    <form onSubmit={onSubmitHandler}>
      <label>
        <span>Search for books</span>
        <input
          type="search"
          placeholder="microservice, restful design, etc.,"
          value={searchTerm}
          onChange={onInputChange}
          required
        />
        <button type="submit">Search</button>
      </label>
      {error && (
        <div style={{ color: `red` }}>
          some error occurred, while fetching api
        </div>
      )}
    </form>
  );
};

export default BookSearchForm; 
```

我们已经将组件拆分到它自己的文件中，并将必要的函数和状态值作为道具传递。

现在在 App.js

```
// App.js
import React, { useState } from 'react';
import axios from 'axios';

import BookSearchForm from './components/bookSearchForm';
import './App.css';
...

const App = () => {
  ...
  return (
    <section>
      <BookSearchForm

        onSubmitHandler={onSubmitHandler}
        onInputChange={onInputChange}
        searchTerm={searchTerm}
        error={error}
      />
      {
        loading && <div style={{color: `green`}}>fetching books for "<strong>{searchTerm}</strong>"</div>
      }
      ...
    </section>
  )
} 
```

让我们以同样的方式分割其他组件。

```
// Loader.js
import React from 'react';

const Loader = ({ loading, searchTerm }) => {
  return (
    <>
      {loading && (
        <div style={{ color: `green` }}>
          fetching books for "<strong>{searchTerm}</strong>"
        </div>
      )}
    </>
  );
};

export default Loader; 
```

至于 BooksList 和 Book component，我没有拆分成文件放在同一个文件里。当功能增加时会拆分它。

```
// booksList.js
import React from 'react';

// Separate the UI specific transforming logic to utils folder
import { bookAuthors } from '../utils';

const Book = ({ book }) => {
  return (
    <li>
      <div>
        <img
          alt={`${book.volumeInfo.title} book`}
          src={`http://books.google.com/books/content?id=${
            book.id
          }&printsec=frontcover&img=1&zoom=1&source=gbs_api`}
        />
        <div>
          <h3>{book.volumeInfo.title}</h3>
          <p>{bookAuthors(book.volumeInfo.authors)}</p>
          <p>{book.volumeInfo.publishedDate}</p>
        </div>
      </div>
      <hr />
    </li>
  );
};

const BooksList = ({ books }) => {
  return (
    <ul>
      {books.items.map((book, index) => {
        return <Book book={book} key={index} />;
      })}
    </ul>
  );
};

export default BooksList; 
```

并在`App.js`
中把这些都加在一起

```
return (
  <>
    <BookSearchForm
      onSubmitHandler={onSubmitHandler}
      onInputChange={onInputChange}
      searchTerm={searchTerm}
      error={error}
    />
    <Loader searchTerm={searchTerm} loading={loading} />
    <BooksList books={books} />
  </>
); 
```

[https://codesandbox.io/embed/rl20opm2l4](https://codesandbox.io/embed/rl20opm2l4)

就这样，伙计们，我们成功地拆分了所有组件。我们可以通过移动状态来进一步优化它。这是下一部分。

我们会看到，

*   如何管理状态和
*   管理状态的不同方法(自定义钩子，useReducer)
*   为什么我们在 App.js 中管理所有状态，而不是更详细地管理组件本身

在这里查看第 3 部分[的代码库，在这里](https://github.com/learnwithparam/books-series-react-hooks/commit/e08570665b6954f30054ca6c4b3a09b15323ff12)查看整个系列的代码库[。](https://github.com/learnwithparam/books-series-react-hooks)

### 保持联系！

如果你喜欢这篇文章，你可以在 [Twitter](https://twitter.com/learnwithparam) 上找到我，了解更新、公告和新闻。🐤