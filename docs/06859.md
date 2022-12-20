# 与 react 挂钩——通过使用 React 及其同类产品构建图书搜索应用程序来学习，第 1 部分

> 原文：<https://dev.to/paramharrison/learn-by-building-a-book-search-app-using-react-and-its-siblings-part-1-1590>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

让我们使用 react 中的 google books API 构建一个简单的图书搜索页面。在开发时，我们将探索 react 钩子、css 模块和 react 应用程序中的测试。

这将是一个多部分系列。

1.  使用 react 和 google books API 的基本图书搜索页面
2.  应用程序的错误处理和加载状态
3.  重构代码以分离组件和状态管理
4.  使用 react 路由器通过路由创建图书详细信息页面
5.  用 CSS 模块设计页面样式
6.  延迟加载组件和页面
7.  使用 jest 和其他孩子测试应用程序

## React app 设置

使用`create-react-app` cli 创建 react 应用程序。

```
npx create-react-app books-search-react-hooks 
```

安装`prettier`进行格式化

```
yarn add --dev prettier pretty-quick husky 
```

让我们在 package.json
中添加预提交钩子配置

```
{  "husky":  {  "hooks":  {  "pre-commit":  "pretty-quick --staged"  }  }  } 
```

## 创建搜索界面

让我们删除`App.js`中的默认内容，并添加用于搜索 google books API 的表单。

```
// App.js
import React from 'react';
import './App.css';

const App = () => {
  return (
    <section>
      <form>
        <label>
          <span>Search for books</span>
          <input
            type="search"
            placeholder="microservice, restful design, etc.,"
          />
          <button type="submit">Search</button>
        </label>
      </form>
    </section>
  );
};

export default App; 
```

## 使用 useState 钩子搜索输入状态

让我们使用`useState`钩子为搜索输入添加本地状态。

```
// App.js
import React, { useState } from 'react';
import './App.css';

const App = () => {
  const [searchTerm, setSearchTerm] = useState('');
  const onInputChange = (e) => {
    setSearchTerm(e.target.value);
  }

  return (
    <section>
      <form onSubmit={onSubmitHandler}>
        <label>
          <span>Search for books</span>
          <input

            type="search"
            placeholder="microservice, restful design, etc.,"
            value={searchTerm}
            onChange={onInputChange}
          />
          <button type="submit">Search</button>
        </label>
      </form>
    </section>
  );
}

... 
```

## Axios 数据调用书籍 API

让我们添加表单提交来调用 google books API。用于谷歌图书查询的 API

```
https://www.googleapis.com/books/v1/volumes?q=<searchTerm> 
```

让我们添加调用 API 的逻辑。首先为 Ajax 请求添加`axios`包。

```
yarn add axios 
```

```
// App.js
...
import axios from 'axios';
...

const App = () => {
    ...

    let API_URL = `https://www.googleapis.com/books/v1/volumes`;

    const fetchBooks = async () => {
        // Ajax call to API using Axios
        const result = await axios.get(`${API_URL}?q=${searchTerm}`);
        // Books result
        console.log(result.data);
    }

    // Submit handler
    const onSubmitHandler = (e) => {
        // Prevent browser refreshing after form submission
        e.preventDefault();
        // Call fetch books async function
        fetchBooks();
    }

    return {
        ...
        <form onSubmit={onSubmitHandler}>
        ...
    }
} 
```

*   首先，我们防止在表单提交后刷新页面的默认浏览器行为
*   然后调用调用 google books API 的函数`fetchBooks`
*   使用 async-await 调用异步图书 API，并将结果记录到控制台

👏恭喜，我们已经用查询获取了 API。让我们在一个状态中填充结果，并用搜索结果更新我们的 UI。

## 更新图书搜索结果到状态

```
// App.js

const [books, setBooks] = useState({ items: [] });

const fetchBooks = async () => {
  const result = await axios.get(`${API_URL}?q=${searchTerm}`);
  setBooks(result.data);
}; 
```

## 图书搜索结果界面

```
// App.js
...

const App = () => {
  ...

  return (
    <section>
      <form onSubmit={onSubmitHandler}>
        ...
      </form>
      <ul>
        {
          books.items.map((book, index) => {
            return (
              <li key={index}>
                <div>
                  <img alt={`${book.volumeInfo.title} book`} src={`http://books.google.com/books/content?id=${book.id}&printsec=frontcover&img=1&zoom=1&source=gbs_api`} />
                  <div>
                    <h3>{book.volumeInfo.title}</h3>
                    <p>{book.volumeInfo.publishedDate}</p>
                  </div>
                </div>
                <hr />
              </li>
            );
          })
        }
      </ul>
    </section>
  );
}

... 
```

*   显示图书的图像、标题和出版日期
*   对于图片，我们使用 google books 中基于图书 ID 的默认图片 url

让我们显示图书作者。每本书都有多个作者，它将作为一个数组出现在结果中。所以我们会用这个逻辑分别串联。

```
let authors = ['Param', 'Vennila', 'Afrin'];
bookAuthors(authors);
// Param, Vennila and Afrin
let authors = ['Param', 'Afrin'];
bookAuthors(authors);
// Param and Afrin 
```

bookAuthors 函数将 Authors 数组作为输入，并基于上述逻辑连接字符串。

```
// App.js

const bookAuthors = authors => {
  if (authors.length <= 2) {
    authors = authors.join(' and ');
  } else if (authors.length > 2) {
    let lastAuthor = ' and ' + authors.slice(-1);
    authors.pop();
    authors = authors.join(', ');
    authors += lastAuthor;
  }
  return authors;
}; 
```

将作者信息添加到列表中。

```
// App.js

const App = () => {
  ...

  return (
    <section>
      ...
      <ul>
        {
          books.items.map((book, index) => {
            return (
              <li key={index}>
                ...
                <div>
                    <h3>{ book.volumeInfo.title }</h3>
                    <p>{ bookAuthors(book.volumeInfo.authors) }</p>
                    <p>{book.volumeInfo.publishedDate}</p>
                </div>
                ...
              </li>
            );
          })
        }
      </ul>
    </section>
  );
}

... 
```

太棒了，我们已经使用 react 钩子用简单的 react 应用程序完成了系列的第一部分。点击查看 codesandbox 示例

[https://codesandbox.io/embed/1qm1m9zpnl](https://codesandbox.io/embed/1qm1m9zpnl)

希望这个系列能帮助你构建下一个大的 react 应用😅。请继续关注本系列的下一部分🤗

在这里查看第 1 部分[的代码库，在这里](https://github.com/learnwithparam/books-series-react-hooks/commit/580ff2b1585bcdb162ad4b5878e0aa0d726aa761)查看整个系列的代码库[。](https://github.com/learnwithparam/books-series-react-hooks)

> 注:本文最初是为[我的博客](https://learnwithparam.com/blog/learn-react-hooks-by-building-books-search/)写的。我在这里为神奇的开发者社区重新发布它。