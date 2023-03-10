# TIL #0:在 JS 中析构

> 原文：<https://dev.to/georgecoldham/til-0-destructuring-in-js-d8j>

> 今天我学到了(TIL)是一种与社区分享我最近学到的东西的方式，我觉得其他人可能会从中受益。这些对那些已经知道的人来说可能是显而易见的，一直到奇怪和晦涩的。归根结底，我们都在学习，所以我们不妨互相帮助！

# 什么是析构？

析构作为 ES6 的一部分被添加进来，作为一种将 JavaScript 对象和数组的属性提取到变量中的方式。它使得处理大型对象变得简单多了，我希望我能早点发现它！

## 数组

我在野外最常看到的析构类型是数组。

```
const colors = ['Red', 'Green', 'Blue', 'Yellow', 'Purple', 'Pink', 'Cyan']

const [r,g,b, ...otherColors] = colors

console.log(r) // 'Red'
console.log(g) // 'Green'
console.log(b) // 'Blue'
console.log(otherColors) // ['Yellow', 'Purple', 'Pink', 'Cyan] 
```

Enter fullscreen mode Exit fullscreen mode

通过将一个数组赋给一个变量数组，我们可以很容易地将单个变量赋给每个值。使用 spread 操作符允许我们创建一个包含所有剩余元素的新数组。

## 物体

访问对象属性的标准方法是使用点符号。

```
const user = {
    name: 'Bob',
    age: 32,
    city: {
    name: 'London'  
  }
}

const name = user.name
const age = user.age
const locationName = user.city.name

console.log(name) // Bob
console.log(age) // 32
console.log(locationName) // London 
```

Enter fullscreen mode Exit fullscreen mode

这对于简单的对象来说是没问题的，但是如果我们试图访问许多属性的话，输入起来会很麻烦。进入析构。

```
JS Result
EDIT ON
 const user = { // Structure taken from Dev.to users API endpoint
  "type_of":"user",
  "id":12345678,
  "username":"Bob_the_nameless",
  "name":"Bob",
  "summary":"An example user",
  "twitter_username":null,
  "github_username":"UndefinedUser",
  "website_url":"www.example.com",
  "location":"London",
  "joined_at":"Jan 11, 2018",
  "profile_image":"urlToSomeImage.jpg"
}

// Too many parameters to want to write by hand to conveniently use
// Use destructuring

const {username, name, joined_at} = user

console.log(username) // 'Bob_the_nameles'
console.log(name) // 'Bob'
console.log(joined_at) // 'Jan 11, 2018' 
```

Enter fullscreen mode Exit fullscreen mode

通过将变量命名为与对象相同的名称，我们可以轻松地提取它们。但是对于嵌套的对象，我们还需要点符号吗？是的，但是这样更好。

```
const user = {
  "type_of":"user",
  "id":12345678,
  "name":"Bob",
  "summary":"An example user",
    "social_media": {
    "usernames": {
      "twitter_username":null,
      "github_username":"UndefinedUser",
      "devto_username":"Bob_the_nameless"
    }
    },  
  "website_url":"www.example.com",
  "location":"London",
  "joined_at":"Jan 11, 2018",
  "profile_image":"urlToSomeImage.jpg"
}

const {twitter_username, github_username, devto_username} = user.social_media.usernames

console.log(twitter_username) // null
console.log(github_username) // 'UndefinedUser'
console.log(devto_username) // 'Bob_the_nameless' 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们可以省去为每个变量重新输入整个路径的麻烦。

我还没有涵盖所有的用例，只有几个我认为最有用的。完整的示例列表可在 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 上找到。我希望这能帮助你们中的一些人节省一些时间！