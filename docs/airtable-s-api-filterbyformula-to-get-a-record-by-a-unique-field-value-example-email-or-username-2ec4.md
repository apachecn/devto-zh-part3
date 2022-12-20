# Airtable 的 API filterByFormula 通过唯一的字段值(例如电子邮件或用户名)获取记录

> 原文：<https://dev.to/cjwd/airtable-s-api-filterbyformula-to-get-a-record-by-a-unique-field-value-example-email-or-username-2ec4>

Airtable 的 API 提供了一个用于检索单个记录的 *find* 方法。但是，该方法只能将记录 ID 作为参数来查找记录。但是，在一些用例中，我们不知道记录 ID。在这些情况下，这种方法没有用。那么，我们如何根据表中的其他字段来检索记录呢？我们使用*选择*方法和 *filterByFormula* 参数。

filterByFormula 参数是不言自明的，它允许我们使用公式过滤 *select* 方法的结果。您可以使用 Airtable 公式字段类型支持的任何公式作为筛选器。Airtable 提供了一个方便的[公式字段引用](https://support.airtable.com/hc/en-us/articles/203255215-Formula-field-reference)。

公式将对每条记录进行求值，如果结果是*而不是* 0，则为 false，""，NaN，[]，或#Error！该记录将包含在响应中。

## 一个例子(通过邮箱地址获取一个用户)

我已经将*选择*方法抽象为一个函数，并放在一个名为 dataController.js 的文件中。注意第二个参数*选项*。*选项*参数将接受一个我们想要传递给选择方法的参数对象。

```
 // dataController.js
exports.getAirtableRecords = (table, options) => {
  let records = [],
      params = {
        view: 'Grid view',
        pageSize: 15
      };

  Object.assign(params, options);

  return new Promise((resolve, reject) => {
    // Cache results if called already
    if (records.length > 0) {
      resolve(records);
    }

    const processPage = (partialRecords, fetchNextPage) => {
      records = [...records, ...partialRecords];
      fetchNextPage();
    };

    const processRecords = (err) => {
      if (err) {
        reject(err);
        return;
      }

      resolve(records);
    };

    table.select(params).eachPage(processPage, processRecords);
  });
}; 
```

现在假设您有一个带有用户登录表单的页面，输入用户名和密码。在我的 userController.js 文件中，我将创建一个通过电子邮件地址获取用户的函数。

```
// userController.js

exports.getUserByEmail = (req, res, next) => {
  const { username, password } = req.body;
  const options = {
    filterByFormula: `OR(email = '${username}', username = '${username}')`
  }

  data.getAirtableRecords(table, options)
    .then( users => {
      users.forEach(function(user) {
      // Call the next middleware (function) e.g validate password
        next();
      });
    })
    .catch(err => {
      console.log( Error(err));
    });
} 
```

在主体请求中，我们现在可以访问电子邮件地址，该地址在 *req.body* 中可用。我现在可以构建一个公式，将我们收到的值与数据库中的值进行比较。

ES6 字符串插值使创建公式字符串变得容易。我使用了 OR 公式，因为我的输入可以接受电子邮件或用户名。从字段引用中，我们可以看到 OR 逻辑运算的作用。

OR(logical1，[logical2，...])
*如果任一参数为 true，则返回 true。*
举例或(已完成，已审核)

我们知道，如果结果不是 0、false，“”、NaN、[]或#Error，filterByFormula 将返回匹配的记录！。

简单地说，这个公式表示如果表中的 email 值与用户名表单输入的值相同，或者如果表中的用户名值与用户名表单输入的值相同，则返回 true，否则返回 false。

```
filterByFormula: `OR(email = '${username}', username = '${username}')` 
```

在另一篇文章中，我将演示如何使用 Airtable 创建一个带有身份验证的用户数据库，并使用上面的会话。