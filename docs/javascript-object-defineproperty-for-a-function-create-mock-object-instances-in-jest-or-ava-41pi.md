# 函数的定义属性:在 Jest 或 AVA 中创建模拟对象实例

> 原文：<https://dev.to/hugo__df/javascript-object-defineproperty-for-a-function-create-mock-object-instances-in-jest-or-ava-41pi>

这篇文章讲述了如何使用`Object.defineProperty`来模仿构造函数如何创建方法。作为函数的不可枚举属性。

`Object.defineProperty`与函数值一起使用的要点可以归结为:

```
const obj = {}

Object.defineProperty(obj, 'yes', { value: () => Math.random() > .5 })

console.log(obj) // {}
console.log(obj.yes()) // false or true depending on the call :D 
```

如您所见，`yes`属性没有被枚举，但是它确实存在。这对于将函数设置为方法模拟非常有用。

这对测试使用 Mongo 的`ObjectId`这样的东西的代码很有用。我们不希望实际的 ObjectIds 散布在我们的代码中。虽然我确实创建了一个允许你生成 ObjectId 兼容值的应用程序([看这里 Mongo ObjectId 生成器](https://observablehq.com/@hugodf/mongodb-objectid-generator))。

GitHub[github.com/HugoDF/mock-mongo-object-id](https://github.com/HugoDF/mock-mongo-object-id)上有所有的测试和对我们正在做的事情和为什么要做的快速解释，在我们光荣地使用`Object.defineProperty`时达到高潮。如果你是粉丝，就把它留在星星上🙂。

## 测试 Mongo ObjectId

您在持久层中使用它们，您通常希望使用`ObjectId()`构造函数将字符串转换成 ObjectId

请看下面的片段:

```
const { ObjectID, MongoClient } = require('mongodb')
const mongoClient = new MongoClient()
async function getUserIdFromSession(sessionId) {
  const session = await (await mongoClient.connect()).collection('sessions').findOne({
    _id: ObjectId(sessionId)
  });

  return session.userId && session.userId.toString();
} 
```

### 天真的嘲弄

一个简单的模拟实现应该是:

```
const mockObjectId = data => data; 
```

我们依赖于这样一个事实，即`.toString`方法存在于字符串上:

```
'myString'.toString() // 'myString' 
```

问题是它不是一个对象，所以它的行为不同

### 更好的嘲弄

这就是我们的三个要求:

*   应该存在并返回传递给构造函数的任何内容
*   它应该是一个对象
*   `ObjectId('a')`应该深度等于`ObjectId('a')`

```
const test = require('ava')
const mockObjectId = data => {
  return {
    name: data,
    toString: () => data
  };
}

test("toString() returns right value", t => {
  t.is(mockObjectId("foo").toString(), "foo");
});
test("it's an object", t => {
  const actual = mockObjectId("foo");
  t.is(typeof actual, "object");
});
test.failing("two objectIds with same value are equal", t => {
  const first = mockObjectId("foo");
  const second = naiveObjectId("foo");
  t.deepEqual(first, second);
}); 
```

失败:

```
Difference:

  {
    name: 'foo',
-   toString: Function toString {},
+   toString: Function toString {},
  } 
```

是每个模拟实例的新函数…这意味着它们并不完全相等。

### 右讽

```
const test = require("ava");

const mockObjectId = data => {
  const oid = {
    name: data
  };
  Object.defineProperty(oid, "toString", {
    value: () => data
  });
  return oid;
};

test("toString() returns right value", t => {
  t.is(mockObjectId("foo").toString(), "foo");
});
test("it's an object", t => {
  const actual = mockObjectId("foo");
  t.is(typeof actual, "object");
});
test("two objectIds with same value are equal", t => {
  const first = mockObjectId("foo");
  const second = mockObjectId("foo");
  t.deepEqual(first, second);
}); 
```

## object . define property 如何拯救了我们的培根

这是关于可枚举性的。我们想要模仿一个有方法的对象，但是当人们枚举它的时候方法不会出现。

`Object.defineProperty`允许您设置属性是否可枚举、可写和可配置，以及一个值或 get/set (getter/setter)对(参见 [MDN Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) )。

需要 2 个描述符(配置)值:`configurable`(如果为真，可以修改或删除属性，默认为假)，`enumerable`(如果为真，在枚举对象属性时显示，默认为假)。

还有一些可选的描述符(配置)值:`value`(与属性关联的值，任何包含函数的 JS 类型)、`writable`(可以使用赋值操作符写入该属性吗，默认为 false)、`get`和`set`(调用它们来获取和设置属性)。

这是一个调用示例:

```
const o = {}
Object.defineProperty(
  o,
  'me',
  {
    value: 'Hugo',
    writable: false, // default: false
  }
)

console.log(o.me) // 'Hugo' 
```

所有的代码都在 github.com/HugoDF/mock-mongo-object-id 上传。如果你是粉丝，就把它留在星星上🙂。

[无刷标志
rawpixel](https://unsplash.com/@rawpixel?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)