# 如何根据字段是否存在在 mongo 集合中找到一个文档

> 原文：<https://dev.to/paramharrison/how-to-find-a-document-in-a-mongo-collection-based-on-whether-a-field-exists-or-not-2efd>

MongoDB find 语法非常简单。让我们有一个名为`users`的集合，并找到集合中的所有文档，

```
// Query
db.users.find({});

// Result
[
  {
    email: 'one@email.com',
    name: 'one',
  },
  {
    firstName: 'two',
  },
  {
    email: 'three@email.com',
    firstName: 'three',
  },
]; 
```

如果我们需要按字段过滤，那么，

```
// Query
db.users.find({ email: 'one@email.com' });

// Result
[
  {
    email: 'one@email.com',
    name: 'one',
  },
]; 
```

如果我们需要根据特定的字段过滤所有的文档呢？举个例子，

*   列出所有没有`email`字段的文档
*   列出所有有`firstName`字段的文件

#### 过滤没有电子邮件字段的文档

在现场使用`$exists`操作符可以轻松实现。

```
// Query
db.users.find({ email: { $exists: false } });

// Result
[
  {
    firstName: 'two',
  },
]; 
```

#### 过滤有名字字段的文档

```
// Query
db.users.find({ firstName: { $exists: true } });

// Result
[
  {
    firstName: 'two',
  },
  {
    email: 'three@email.com',
    firstName: 'three',
  },
]; 
```

MongoDB 非常强大，它提供了许多方法来查询您到底想要什么。希望这篇教程对你有所帮助🤗