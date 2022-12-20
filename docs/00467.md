# 了解 JSON.parse()和 JSON.stringify()

> 原文：<https://dev.to/attacomsian/understanding-json-parse-and-json-stringify-4685>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/json-parse-stringify)。*

* * *

JSON (JavaScript Object Notation)是一种轻量级的、人类可读的数据交换格式。它最初源于 JavaScript，可用于许多编程语言，包括 [Java](https://attacomsian.com/blog/processing-json-spring-boot) ，Python，C#，PHP 和 Ruby。

对于 JavaScript 异步操作，使用它作为数据格式是很自然的选择。`JSON`对象有两个重要的方法来转换和存储 JSON 数据:`parse()`和`stringify()`。`JSON.parse()`方法将一个字符串作为输入，并将其转换成一个对象。类似地，`JSON.stringify()`接受一个 JSON 对象并将其转换成一个字符串。

我们来举个例子:

```
const jsonObj = {
    id: 1,
    name: 'Hamburger',
    icon: '🍔',
    type: 'Food'
};

// convert JSON object to string
const jsonStr = JSON.stringify(jsonObj);

console.log(jsonStr);
// '{"id":1,"name":"Hamburger","icon":"🍔","type":"Food"}'

//convert string back to JSON object
console.log(JSON.parse(jsonStr));
// {id: 1, name: "Hamburger", icon: "🍔", type: "Food"} 
```

Enter fullscreen mode Exit fullscreen mode

这些方法不仅仅局限于 JSON 对象。您还可以使用它们将 JSON 数组转换成字符串，反之亦然:

```
const jsonArr = ['🐶', '🦉', '💀', '🦆', '🍕'];

const jsonStr = JSON.stringify(jsonArr);

console.log(jsonStr);
// '["🐶","🦉","💀","🦆","🍕"]'

console.log(JSON.parse(jsonStr));
// ["🐶", "🦉", "💀", "🦆", "🍕"] 
```

Enter fullscreen mode Exit fullscreen mode

## JSON.stringify()

顾名思义，`JSON.stringify()`函数将 JavaScript 对象转换成 JSON 字符串。从客户机向服务器发送 JSON 数据时，必须将其转换成 JSON 字符串。`JSON.stringify()`还可以多取两个可选参数:

*   `replacer` -操作结果的函数或数组。
*   `space` -字符串或数字。

为对象中的每个属性调用`replacer`函数，该函数可用于从返回的 JSON 字符串中移除特定值:

```
const user = {
    id: 599,
    name: 'John Doe',
    email: 'john.doe@example.com',
    password: '123abc',
    age: 30,
    address: {
        city: 'New York',
        country: 'United States'
    },
    hobbies: ['Fishing', 'Golf', 'Table Tennis']
};

const str = JSON.stringify(user, (key, value) => {
    // filter-out password from final string
    if (key === 'password') {
        return undefined;
    }
    return value;
});

console.log(str); 
```

Enter fullscreen mode Exit fullscreen mode

下面是上面的代码返回的 JSON 字符串，其中不包括 password 属性:

```
'{"id":599,"name":"John Doe","email":"john.doe@example.com","age":30,"address":{"city":"New York","country":"United States"},"hobbies":["Fishing","Golf","Table Tennis"]}' 
```

Enter fullscreen mode Exit fullscreen mode

如果一个数组作为`replacer`被传递，那么只有那些存在于数组中的对象属性会被包含在结果 JSON 字符串中:

```
const str = JSON.stringify(user, ['name', 'email', 'age']);

console.log(str);
// '{"name":"John Doe","email":"john.doe@example.com","age":30}' 
```

Enter fullscreen mode Exit fullscreen mode

> **注意:**`replacer`函数不能用来从数组中移除值。如果你返回`undefined`或者一个函数，那么就用`null`来代替。

`space`可以是最多 10 个字符的字符串，也可以是 0 到 10 之间的数字。如果指定了字符串，它将被用作空白。另一方面，数字指示有多少空格字符用作空白。下面是一个例子:

```
const dog = {
    name: 'Bablu',
    image: '🐶',
    age: '6 months'
};

const str = JSON.stringify(dog, null, '----');

console.log(str);

// "{
// ----"name": "Bablu",
// ----"image": "🐶",
// ----"age": "6 months"
// }" 
```

Enter fullscreen mode Exit fullscreen mode

## JSON.parse()

`JSON.parse()`函数的作用正好相反。它接受一个 JSON 字符串作为输入，并将其转换成一个 JavaScript 对象:

```
const str = '{"name":"Bablu","image":"🐶","age":"6 months"}';

const dog = JSON.parse(str);

console.log(dog.name); // Bablu
console.log(dog.image); // 🐶
console.log(dog.age); // 6 months 
```

Enter fullscreen mode Exit fullscreen mode

还可以传递一个可选的`reviver`函数，在返回对象属性之前对其进行转换:

```
const str = '{"id":99,"name":"Bablu","image":"🐶","age":"6 months"}';

const dog = JSON.parse(str, (key, value) => {
    if(typeof value === 'string') {
        return value.toUpperCase();
    }
    return value;
});

console.log(dog.id); // 99
console.log(dog.name); // BABLU
console.log(dog.image); // 🐶
console.log(dog.age); // 6 MONTHS 
```

Enter fullscreen mode Exit fullscreen mode

JSON 中不允许尾随逗号。因此，如果作为参数传递的字符串后面有逗号:
，那么`JSON.parse()`会抛出异常

```
JSON.parse('[1, 2, 3, 4, ]'); 
// Unexpected token ] in JSON at position 13
JSON.parse('{"name": "John Doe", "age": 29, }'); 
// Unexpected token } in JSON at position 32 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

JSON 是一种轻量级格式，用于在客户机和服务器之间共享数据。它已经成为在 JavaScript 和许多其他编程语言中进行异步请求的自然选择。对于转换和存储数据，`JSON`对象提供了两个有用的方法:

*   `JSON.stringify()`接受一个 JavaScript 对象作为输入，并将其转换成一个 JSON 字符串。它可以取两个可选参数:`replacer`和`space`。
    *   `replacer`可以是一个函数，也可以是一个数组，用来从生成的 JSON 字符串中过滤出值。
    *   `space`参数是一个数字或一个字符串。它用于控制最终字符串中的间距。
*   `JSON.parse()`做相反的事。它接受一个 JSON 字符串，并将其转换回 JavaScript 对象或值。可以传递一个可选的`reviver`函数，在对象返回之前对其执行转换。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。