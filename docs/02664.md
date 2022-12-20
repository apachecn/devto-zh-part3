# 避免递归陷阱:MongoDB 嵌套查询子句的添加和移除

> 原文：<https://dev.to/hugo__df/avoiding-recursion-pitfalls-mongodb-nested-query-clause-addition-and-removal-3n83>

一个案例研究，说明递归在什么地方对 enterprise Node.js 应用程序有用，以及如何避免常见的陷阱，如`RangeError: Maximum call stack size exceeded`。

这篇文章的完整资源库在 GitHub 上:[github.com/HugoDF/mongo-query-clause-modification](https://github.com/HugoDF/mongo-query-clause-modification)

我们将针对两个现实问题实施解决方案:

*   向 MongoDB 查询添加一个＄或 query 子句
*   使用$or 和$and 删除 MongoDB 查询中对字段的引用(可能)

## 向 MongoDB 查询添加一个＄或查询子句

参见[处的最终代码。/src/add-or-clause.js](https://github.com/HugoDF/mongo-query-clause-modification/blob/master/src/add-or-clause.js) 。

参数为`query`和`orClause`。

`query`是一个 MongoDB 查询，它可能包含也可能不包含一个`$or`和/或`$and`子句。

`orClause`是一个包含 and `$or`子句的对象(它本身就是一个成熟的 MongoDB 查询)，例如

```
const orClause = {
  $or: [
    {createdAt: {$exists: false}},
    {createdAt: someDate}
  ]
}; 
```

最初只需要注意一件事:1。该查询不包含＄或子句 2。该查询包含一个＄or 子句

### 当查询中没有＄or 子句时

如果没有`$or`子句，我们可以简单地扩展我们的`orClause`查询和`query`参数，即。

```
const newQuery = {
  ...query,
  ...orClause
}; 
```

除非在某个地方有 and `$and`，在这种情况下，我们想把我们的`orClause`加到`$and` :

```
const newQuery = {
  ...query,
  $and: [...query.$and, orClause]
}; 
```

### 当查询中有＄or 子句时

如果有一个`$or`子句，我们不能仅仅覆盖它，我们需要`$and`两个`$or`查询。

我们还应该保留现有的`$and`子句内容，结果是:

```
const newQuery = {
  ...queryWithoutOrRemoved,
  $and: [
    ...(query.$and || []),
    { $or: query.$or },
    orClause
  ]
}; 
```

### 满解

这个在[也有。/src/add-or-clause.js](https://github.com/HugoDF/mongo-query-clause-modification/blob/master/src/add-or-clause.js) 。

```
function addOrClause(query, orClause) {
  const {$or, ...queryRest} = query;
  if ($or) {
    return {
      ...queryRest,
      $and: [...(queryRest.$and || []), {$or}, orClause]
    };
  }

  if (queryRest.$and) {
    return {
      ...queryRest,
      $and: [...queryRest.$and, orClause]
    };
  }

  return {
    ...query,
    ...orClause
  };
}

module.exports = addOrClause; 
```

### 对应的测试套件

我们可以观察到不同的案例是如何非常直接地映射到测试案例的。

```
const addOrClause = require('./add-or-clause');

test('should add the passed or clause if no $or on the current query', () => {
  const orClause = {$or: [{myField: 'value'}, {myField: null}]};
  const query = {foo: 'bar'};
  expect(addOrClause(query, orClause)).toEqual({
    $or: [{myField: 'value'}, {myField: null}],
    foo: 'bar'
  });
});
describe('when the query already has an $or', () => {
  test('should add the passed or clause to and $and that also contains the current query', () => {
    const orClause = {$or: [{myField: 'value'}, {myField: null}]};
    const query = {$or: [{foo: 'bar'}, {foo: {$exists: false}}]};
    expect(addOrClause(query, orClause)).toEqual({
      $and: [
        {$or: [{foo: 'bar'}, {foo: {$exists: false}}]},
        {
          $or: [{myField: 'value'}, {myField: null}]
        }
      ]
    });
  });
  describe('when the query has an $and', () => {
    test('should keep the $and, add the $or and the current query', () => {
      const orClause = {$or: [{myField: 'value'}, {myField: null}]};
      const query = {
        $or: [{hello: 'world'}],
        $and: [{foo: 'bar'}, {bar: 'baz'}]
      };
      expect(addOrClause(query, orClause)).toEqual({
        $and: [
          {foo: 'bar'},
          {bar: 'baz'},
          {$or: [{hello: 'world'}]},
          {$or: [{myField: 'value'}, {myField: null}]}
        ]
      });
    });
  });
});
describe('when the query has an $and query', () => {
  test('should add the new or clause to the $and', () => {
    const orClause = {$or: [{myField: 'value'}, {myField: null}]};
    const query = {$and: [{foo: 'bar'}, {bar: 'baz'}]};
    expect(addOrClause(query, orClause)).toEqual({
      $and: [
        {foo: 'bar'},
        {bar: 'baz'},
        {$or: [{myField: 'value'}, {myField: null}]}
      ]
    });
  });
}); 
```

## 使用$or 和$and 删除对 MongoDB 查询中字段的引用(可能)

参见[处的完整解决方案。/src/remove-field-references . js](https://github.com/HugoDF/mongo-query-clause-modification/blob/master/src/remove-field-references.js)。

在本例中，我们创建了一个带有两个参数的函数:`query`(如上所述的 MongoDB 查询)和`fieldName`(我们想要删除引用的字段的名称)。

### 删除顶级字段

最简单的做法是删除对对象顶层字段的引用。

我们可以使用析构和递归
创建一个简单的`omit`函数

```
const omit = (obj, [field, ...nextFields]) => {
  const {[field]: ignore, ...rest} = obj;
  return nextFields.length > 0 ? omit(rest, nextFields) : rest;
}; 
```

并使用它:

```
const newQuery = omit(query, [fieldName]); 
```

### 删除任何＄or 子句中的字段

要删除$or 子句(这是一个成熟的查询)中的字段，只需获取$or 值(这是一个数组)并对其运行递归函数即可。

这将删除嵌套`$or`字段子查询中`$or`子查询*和*的顶级字段。

我们希望确保删除空的$或子查询，因为`{ $or: [{ }, {}]}`是一个无效的查询。

我们将查询的`$or`默认为一个空数组，并在将其传播回 newQuery 之前检查长度。这是因为`{ $or: [] }`是一个无效的查询。

在扩展`filteredTopLevel`时，我们还小心地移除了顶层`$or`，这样如果新的`$or`是一个空数组，旧的`$or`就会被忽略。

```
function removeFieldReferences (query, fieldName) {
  const filteredTopLevel = omit(query, [fieldName]);

  const newOr = (filteredTopLevel.$or || [])
    .map(q => removeFieldReferences(q, fieldName))
    .filter(q => Object.keys(q).length > 0);

  return {
    ...omit(filteredTopLevel, ['$or']),
    ...(newOr.length > 0 ? {$or: newOr} : {})
  };
} 
```

### 删除任何 and 子句中的字段

`$and`解决方案的基本原理与$or 解决方案相同。

我们递归并检查我们没有通过省略空数组和对象来生成无效的查询:

```
function removeFieldReferences (query, fieldName) {
  const filteredTopLevel = omit(query, [fieldName]);

  const newAnd = (filteredTopLevel.$and || [])
    .map(q => removeFieldReferences(q, fieldName))
    .filter(q => Object.keys(q).length > 0);

  return {
    ...omit(filteredTopLevel, ['$and']),
    ...(newAnd.length > 0 ? {$and: newAnd} : {})
  };
} 
```

### 确认我们不太可能破产

实际实现有一个默认为 5 的第三个参数`maxDepth`。

当`maxDepth`等于`0`时，我们不做任何处理就返回查询(可以说我们应该运行顶级过滤器)。

在对`removeFieldReferences`的递归调用中，我们传递`(q, fieldName, maxDepth - 1)`,这样我们就不会意外地陷入不必要的深度。

这样就避免了`RangeError: Maximum call stack size exceeded`。

### 最终实现

这个在[也有。/src/remove-field-references . js](https://github.com/HugoDF/mongo-query-clause-modification/blob/master/src/remove-field-references.js)。

```
const omit = (obj, [field, ...nextFields]) => {
  const {[field]: ignore, ...rest} = obj;
  return nextFields.length > 0 ? omit(rest, nextFields) : rest;
};

function removeFieldReferences(query, fieldName, maxDepth = 5) {
  if (maxDepth <= 0) {
    return query;
  }

  const filteredTopLevel = omit(query, [fieldName]);

  const newOr = (filteredTopLevel.$or || [])
    .map(q => removeFieldReferences(q, fieldName, maxDepth - 1))
    .filter(q => Object.keys(q).length > 0);

  const newAnd = (filteredTopLevel.$and || [])
    .map(q => removeFieldReferences(q, fieldName, maxDepth - 1))
    .filter(q => Object.keys(q).length > 0);

  return {
    ...omit(filteredTopLevel, ['$or', '$and']),
    ...(newOr.length > 0 ? {$or: newOr} : {}),
    ...(newAnd.length > 0 ? {$and: newAnd} : {})
  };
}

module.exports = removeFieldReferences; 
```

### 对应的测试套件

我们可以观察到不同的案例是如何非常直接地映射到测试案例的。

```
const removeFieldReferences = require('./remove-field-references');

test('should remove top-level fields', () => {
  const query = {
    hello: 'value'
  };
  const fieldName = 'hello';
  expect(removeFieldReferences(query, fieldName).hello).toBeUndefined();
});
test('should return passed query when maxDepth is hit (avoids busting the stack by default)', () => {
  const query = {
    hello: 'value'
  };
  const fieldName = 'hello';
  expect(removeFieldReferences(query, fieldName, 0)).toEqual(query);
});
test('should remove references to the field in top-level $or queries', () => {
  const query = {
    $or: [
      {hello: 'value', otherField: 'not-related'},
      {hello: 'othervalue', otherField: 'even-less-related'}
    ]
  };
  const fieldName = 'hello';
  expect(removeFieldReferences(query, fieldName)).toEqual({
    $or: [{otherField: 'not-related'}, {otherField: 'even-less-related'}]
  });
});
test('should remove $or clauses where the query becomes empty on omission of a field', () => {
  const query = {
    $or: [{hello: 'value'}, {otherField: 'not-related'}]
  };
  const fieldName = 'hello';
  expect(removeFieldReferences(query, fieldName)).toEqual({
    $or: [{otherField: 'not-related'}]
  });
});
test('should remove references to field in top-level queries inside of $and', () => {
  const query = {
    $and: [
      {hello: 'value', otherField: 'value'},
      {hello: 'other-value', otherField: 'value'}
    ]
  };
  const fieldName = 'hello';
  expect(removeFieldReferences(query, fieldName)).toEqual({
    $and: [{otherField: 'value'}, {otherField: 'value'}]
  });
});
test('should remove $and clause if all queries end up filtered out', () => {
  const query = {
    foo: 'bar',
    $and: [{hello: 'value'}, {hello: 'other-value'}]
  };
  const fieldName = 'hello';
  expect(removeFieldReferences(query, fieldName)).toEqual({foo: 'bar'});
});
test('should remove references to field in nested $or inside of $and', () => {
  const query = {
    $and: [
      {
        $or: [{hello: 'value'}, {hello: null}]
      },
      {otherField: 'not-related'}
    ]
  };
  const fieldName = 'hello';
  expect(removeFieldReferences(query, fieldName)).toEqual({
    $and: [{otherField: 'not-related'}]
  });
}); 
```

[un splash-logo
Tine ivani](https://unsplash.com/@tine999?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)