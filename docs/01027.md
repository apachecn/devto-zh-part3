# JavaScript 异步/等待承诺所有数组析构

> 原文：<https://dev.to/daleljefferson/javascript-async-await-promise-all-array-destructuring-31lk>

当我说使用异步代码是现代应用程序开发的一个重要部分时，我想你会同意我的观点。使用新的 JavaScript 语法，您可以极大地提高代码的可读性和性能。在本文中，我将向您展示如何使用`Promise.all()`以及数组析构。

让我们假设您正在构建一个图书应用程序，您想要获取一本书及其作者和评级。

## 天真的实现

```
const getBook = async bookName => {
  const book = await fetchBook(bookName);

  const author = await fetchAuthor(book.authorId);
  const rating = await fetchRating(book.id);

  return {
    ...book,
    author,
    rating
  };
};

getBook("The Wealth of Nations"); 
```

看起来非常干净的代码，但是它是如何执行的呢？

*   提取书:1 秒
*   1 秒钟
*   庆祝:1 秒
*   **总计:3 秒**

你们中聪明的人可能已经注意到 fetchAuthor 和 fetchRating 是独立的，可以同时下载。

## 承诺全部实现

```
const getBook = async bookName => {
  const book = await fetchBook(bookName);

  return Promise.all([
    fetchAuthor(book.authorId),
    fetchRating(book.id)
  ]).then(results => ({
    ...book,
    author: results[0],
    rating: results[1]
  }));
}; 
```

不太干净，但现在我们同时获取作者和评级。

*   提取书:1 秒
*   (fetchAuthor/fetchRating): 1 秒
*   **总计:2 秒**

## 两全其美

```
const getBook = async bookName => {
  const book = await fetchBook(bookName);

  const [author, rating] = await Promise.all([
    fetchAuthor(book.authorId),
    fetchRating(book.id)
  ]);

  return {
    ...book,
    author,
    rating
  };
}; 
```

在这里，我们在 await 中使用数组析构，在我看来，这极大地提高了代码的可读性，并且我们仍然可以从使用`Promise.all`中获得性能提升。