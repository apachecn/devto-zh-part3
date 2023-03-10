# 重构编年史:传播运算符，映射，归约。

> 原文：<https://dev.to/dvddpl/refactoring-chronicles-111n>

上周我回顾了一些旧代码上的一个小的新特性。我没有过多地谈论细节，也没有咆哮从 UX 的角度来看这是否有意义，我的要求大致如下:

> 当我们在 UI 中添加/创建一个作者时，如果该作者以多种语言出版了多本书，我们应该展开 author 对象并在 DB 中插入多行。基本上每本书/每种语言一行。

想象一下这个作者:

```
const author = {
    name: "John",
    surname: "Doe",
    books: ["A novel", "Romance of your life", "Midnight Krimi"],
    languages: ["IT","DE","EN"]
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们有 3 本书和 3 种语言，我们应该复制作者 9 次(其中书和语言只存在于一个项目中，而不是一个数组中。

要检查的代码看起来像这样:

```
const cloneObject = (obj) => {
    return JSON.parse(JSON.stringify(obj));
};

const cloneObjects = (entries, from, to) => {
    const objects = [];
    entries.forEach((obj) => {
        if (obj.hasOwnProperty(from)) {
            let valuesToSplit = obj[from];
            if (typeof valuesToSplit === "string") {
                valuesToSplit = valuesToSplit.split(",");
            }
            valuesToSplit.forEach((value) => {
                const clonedObject = cloneObject(obj);
                delete clonedObject[from];

                if (typeof value === "string") {
                    clonedObject[to] = value;
                }

                if (typeof value === "object") {
                    clonedObject[to] = value[to];
                }

                objects.push(clonedObject);
            });
        } else {
            objects.push(obj);
        }
    });

    return objects;
};

const expandRequest = (request) => {
    let entries = [request];
    entries = cloneObjects(entries, "books", "book");
    entries = cloneObjects(entries, "languages", "language");
    return entries;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的优点在于,**被设计成足够通用的**,这样 cloneObjects 函数就可以在不同的属性上被迭代调用，并且它考虑到了要克隆的对象的深度副本。
另一方面，通用并不是必要条件——由于非常古老的数据库和客户端实现，手头的用例非常特定于这两个属性。
甚至深度克隆也不是必须的(同样，在这种情况下，对象一直是扁平的，使用像`JSON.parse(JSON.stringify(obj))`这样昂贵而晦涩的操作是没有意义的。对这一实现的其他批评是它没有功能性——条目不断变化，不能立即清晰。

因此，让我们看看如何重构这段代码。
首先，如果当前的实现使之成为可能，在接触任何可以工作的代码之前——不管它可能有多难看，多糟糕，多麻烦——**我们应该进行单元测试**,这样我们就能 100%确定我们的重构没有破坏预期的行为。

```
import test from "ava"
test('Author is expanded into multiple objects (num of books x languages) when it has more than one book and more language/trnaslations', t => {
    const author = {
        name: "John",
        surname: "Doe",
        books: ["A novel", "Romance of your life"],
        languages: ["IT","DE"]
    }
    const expected = [
    {
        name: "John",
        surname: "Doe",
        book: "A novel",
        language: "IT"
    },
    {
        name: "John",
        surname: "Doe",
        book: "A novel",
        language: "DE"
    },
    {
        name: "John",
        surname: "Doe",
        book: "Romance of your life",
        language: "IT"
    },
    {
        name: "John",
        surname: "Doe",
        book: "Romance of your life",
        language: "DE"
    }
    ]

    const expanded = expandRequest(author)
    t.is(expanded.length, author.books.length * author.languages.length)
    t.deepEqual(expanded, expected)
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以继续重构了:

因为我们知道我们可以接受一个浅拷贝——反正物体是平的
,我们可以改变

```
JSON.parse(JSON.stringify(obj) 
```

Enter fullscreen mode Exit fullscreen mode

使用扩展运算符

```
const clone = {...obj} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以使用析构:
来提取我们想要用作“乘数”的数组

```
 const {books, languages} = obj; 
```

Enter fullscreen mode Exit fullscreen mode

我们编写了一个方法，遍历第一个数组，并将它映射到一个新的克隆对象，该对象填充了一个新的属性

```
const expandedWithBooks = books.map(b=> ({...clone, book:b}) ) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们使用 reduce 对一本书的所有作者进行迭代，并应用一个类似的函数来克隆他们中的每一个人，添加语言。

```
languages.reduce((acc, curr)=> {
          const addLang = expandedWithBooks.map(o => ({ ...o, language:curr }))
          return [...acc , ...addLang]
          }
    ,[]) 
```

Enter fullscreen mode Exit fullscreen mode

注意连接两个数组的扩展操作符方式:
`[...array , ...anotherArray]`等价于`array.concat(anotherArray)`，因为两种方式都返回一个新数组。

最终方法如下:

```
const expand = (obj) => {
    const {books, languages} = obj;
    const clone = {...obj}
    delete clone["books"];
    delete clone["languages"];
  const expandedWithBooks = books.map(b=> ({...clone, book:b}) )
    return languages.reduce((acc, curr)=> {
          const addLang = expandedWithBooks.map(o => ({ ...o, language:curr }))
          return [...acc , ...addLang]
          }
    ,[])
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢 ES6 的功能。
[![ES6 javascript is awesome](img/437f32dc9744a394b86047b2b1c865c7.png)T3】](https://i.giphy.com/media/26tk0FLJDhnuHkvF6/giphy.gif)

[在 CodeSandbox 上查看](https://codesandbox.io/s/zl939mrq3p)