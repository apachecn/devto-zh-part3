# Node.js 中针对数据库检查大量数据的更好方法

> 原文：<https://dev.to/iamfrntdv/a-better-way-to-check-big-amount-of-data-against-database-in-nodejs-552i>

[![cover](img/a1173b087537bd25bc40449476aad4a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h2kyTrmy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ondikr1hhkcc4tzp7svm.jpg)

在处理一些项目时，可能会有一些明显的性能优化任务，但也不那么明显。有时甚至很难找到一个好的解决方案，尤其是当互联网上的很多资源看起来像是复制粘贴的时候。

不幸的是，我在互联网上找不到解决这个问题的好办法。幸运的是，我从我的同事那里得到了帮助，并愿意与你分享。

那么，如何检查数据库中是否已经存在一些数据呢？解决方案非常简单，我们只需要知道一些唯一的标识符，我们将使用它从数据库中查询数据:

```
const pool = ... // Database connection pool
const product = {
    id: 1,
    title: "Best Product Ever",
    ...
}

pool.product.find({where: {title: product.title}}).then(result => {
    // do something...
}); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，没什么复杂的。但是如果我们需要检查数据库中是否存在多种产品呢？我们可以使用循环:

```
const pool = ... // Database connection pool
const products = [{
    id: 1,
    title: "Best Product Ever",
    ...
}, {
    ...
}]

products.forEach(product =>{ 
    pool.product.find({where: {title: product.title}}).then(result => {
        // Do something...
    });
}) 
```

Enter fullscreen mode Exit fullscreen mode

这看起来也没那么复杂。您可能已经知道，连接到数据库的一个好方法是使用池。这提供了多次连接到数据库的能力，而不需要关闭连接。如果我们仔细看看我们的循环，就不难理解数组中有多少项就有多少连接。当我们必须检查数据库中是否存在 5 或 6 个项目时，这听起来可能不是问题。但是，当我们想要检查成百上千甚至几十万条记录时，这可能是一个很大的性能问题。

## 解

那么我们该如何解决这个问题呢？不是一个接一个地连接到数据库并检查记录是否存在，而是可以基于数据进行查询，我们将查询的数据与我们的收入数据进行比较。

```
const pool = ... // Database connection pool
const products = [
    {
        title: 'Some Product Title'...
        ...
    },
    {...},
    {...}
]

const productTitles = products.map(product => item.title)
const query = {
    where: {
        title: productTitles
    },
    attributes: ['title']
}

pool.product.find(query).then(result => {
    const resultTitles = result.map(item => item.title);

    const newProducts = products.filter(product => resultTitles.indexOf(product.title) === -1);
    // Do something...
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我基于产品列表进行了一次查询，基于查询选择了数据库中的所有产品。如果我的查询中有一些数据库中不存在的标题，它就不会选择它。然后我在。地图和。过滤方法。结果，我在“新产品”变量中获得了新产品。
在这种情况下我们将只有一个连接池。根据项目需求，可能会有更多的连接池，例如一个连接池，以便稍后保存数据。
当您希望只处理数据库中不存在的数据，并且只在几个额外的处理步骤后保存到数据库时，这很有用。

我希望这个小技巧能帮助你让你的项目变得更好。