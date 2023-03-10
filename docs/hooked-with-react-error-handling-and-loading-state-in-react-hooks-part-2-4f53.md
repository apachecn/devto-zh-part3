# 与 React 挂钩——React 挂钩中的错误处理和加载状态，第 2 部分

> 原文：<https://dev.to/paramharrison/hooked-with-react-error-handling-and-loading-state-in-react-hooks-part-2-4f53>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

在第一部分中，我们创建了图书搜索应用 react。但是在从 API 获取数据之前，我们没有处理任何错误或任何加载状态。

在这一部分，我们将看到

*   错误处理
*   API 调用的加载状态
*   输入的基本验证

## 错误处理

现在，如果您单击输入值为空的 search 按钮，我们可以看到 API 调用错误。在我们的代码沙箱[这里](https://codesandbox.io/s/1qm1m9zpnl)尝试一下

这是 Google books API 返回的内容，

```
{  "error":  {  "errors":  [  {  "domain":  "global",  "reason":  "queryRequired",  "message":  "Missing query.",  "locationType":  "parameter",  "location":  "q"  }  ],  "code":  400,  "message":  "Missing query."  }  } 
```

让我们用代码来处理它，

```
// App.js
...

const [searchTerm, setSearchTerm] = useState('');
const [books, setBooks] = useState({items: []});
const [error, setError] = useState(false);

let API_URL = `https://www.googleapis.com/books/v1/volumes`;

const fetchBooks = async () => {
  setError(false);
  try {
    const result = await axios.get(`${API_URL}?q=${searchTerm}`);
    setBooks(result.data);
  }
  catch(error) {
    setError(true);
  }
}

... 
```

我们使用`useState`创建了一个叫做`error`的新状态。它被设置在 catch 块中。
`try, catch`是处理异步、等待函数错误的常用方法。

我们还在获取 API 之前将错误状态设置为 false，这是为了确保下次 API 返回正确的值时，不会因为之前的尝试而显示错误。因此在再次尝试提取之前清除错误。

> 为了保持实现简单，我们没有使用 API 中的实际错误对象。在实际应用中，我们需要向最终用户显示适当的错误。

让我们向用户显示错误，

```
// App.js
...

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
      {
        error && <div style={{color: `red`}}>some error occurred, while fetching api</div>
      }
    </form>
    ...
  </section>
);

... 
```

如果 API 返回错误，我们会向用户显示一条简单的错误消息。太好了，起作用了。让我们继续设置`loading`状态。这类似于我们处理错误的方式。

## 加载状态

创建加载状态的步骤，

*   使用`useState`创建加载状态
*   在获取 API 前设置加载状态，在 API 调用完成后重置
*   基于`loading`状态值在 JSX 中设置加载器

```
// App.js
...

const [searchTerm, setSearchTerm] = useState('');
const [books, setBooks] = useState({items: []});
const [error, setError] = useState(false);
const [loading, setLoading] = useState(false);

let API_URL = `https://www.googleapis.com/books/v1/volumes`;

const fetchBooks = async () => {
  // set loading Before API operation starts
  setLoading(true);
  setError(false);
  try {
    const result = await axios.get(`${API_URL}?q=${searchTerm}`);
    setBooks(result.data);
  }
  catch(error) {
    setError(true);
  }
  // After API operation end
  setLoading(false);
} 
```

让我们在 JSX 中添加加载程序

```
...

return (
  <section>
    <form onSubmit={onSubmitHandler}>
      ...
    </form>
    {
      loading && <div style={{color: `green`}}>fetching books for "<strong>{searchTerm}</strong>"</div>
    }
    <ul>
      {
        books.items.map((book, index) => {
          return (
            ...
          );
        })
      }
    </ul>
  </section>
);

... 
```

[https://codesandbox.io/embed/48j0pqr8w9](https://codesandbox.io/embed/48j0pqr8w9)

这是可行的，我们已经显示了 API 获取时的加载状态，并在 API 完成获取和加载后显示了搜索到的书籍。

让我们添加一个简单的 HTML 验证，不允许空字符串值，

```
<input
  type="search"
  placeholder="microservice, restful design, etc.,"
  value={searchTerm}
  onChange={onInputChange}
  required
/> 
```

伙计们，我们已经成功实现了图书搜索应用程序的加载和错误状态。

[https://codesandbox.io/embed/n47kmr7m9j](https://codesandbox.io/embed/n47kmr7m9j)

我们系列的下一步，

*   重构代码以分离组件
*   再谈状态管理
*   更多的是造型和测试

希望这个系列能帮助你构建下一个大的 react 应用😅。请继续关注本系列的下一部分🤗

在这里查看第 2 部分[的代码库，在这里](https://github.com/learnwithparam/books-series-react-hooks/commit/e4defbf9d8073be796c680c7445278bbc538d5bd)查看整个系列的代码库[。](https://github.com/learnwithparam/books-series-react-hooks)

> 注:本文最初是为[我的博客](https://learnwithparam.com/blog/error-handling-in-react-hooks/)写的。我在这里为神奇的开发者社区重新发布它。