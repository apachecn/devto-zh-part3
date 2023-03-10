# 用类型脚本解码 JSON

> 原文：<https://dev.to/joanllenas/decoding-json-with-typescript-1jjc>

Typescript 非常适合为 JavaScript 程序增加类型安全，但是就其本身而言，它不足以保证它在运行时不会崩溃。

本文展示了 [JSON 解码器](https://github.com/joanllenas/ts.data.json)如何帮助将 TypeScript 编译时保证扩展到运行时环境。

## 运行时 vs 编译时

您正在开发的应用程序与用户打交道，因此您创建了一个`User`类型:

```
interface User {
   firstName: string;
   lastName: string;
   picture: string;
   email: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

您将使用这个类型来注释`/me` API 端点结果，然后您将使用这个`User`做各种各样的事情，但是让我们集中在应用程序的配置文件区域:

*   它将显示`firstName` + `lastName`的连接。
*   在`firstName` + `lastName`下面你也要显示`email`。
*   最后，您希望显示用户的`picture`,或者如果不存在，显示一个默认图像。

什么会出错？首先，`User`类型没有说明事实，它没有表达 API 可以返回的`User` *形状*的所有排列。
我们来看几个例子:

```
// The result has null properties
{ firstName: "John", lastName: null, picture: null, email: "john@example.com" }

// The API returned null
null

// The result has undefined properties
{ firstName: "John", lastName: "Doe", email: "john@example.com" }

// The API contract changed and the UI team wasn't notified
{ fName: "John", lName: "Doe", picture: 'pic.jpg', email: "john@example.com" } 
```

Enter fullscreen mode Exit fullscreen mode

[![alt text](img/978472a9ba7b355010088a5d858fafcb.png "Who cares?")](https://res.cloudinary.com/practicaldev/image/fetch/s--65YoeCWi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/se4dj39r4rjs7qqhs986.jpg)

你可以通过使用防御性编程技术来处理这些问题，也就是检查`if`语句背后的`null` / `undefined`，但是，当其他人想要在其他地方使用`/me`结果时会发生什么呢？也许你的同事信任`User`类型的人，为什么不呢？接下来会发生什么？我们为运行时错误引入了一个新的向量。

## 进入 Json 解码器

您使用 Json 解码器来确保给定的运行时值符合特定的编译时类型，不仅如此，它还为您提供了应用转换、故障转移等等的工具。

多亏了 Elm，Json 解码器最近变得流行起来。
Elm 的 Json 解码器是该语言的核心部分，它们被广泛用于确保 JS 到 Elm 的顺畅通信。

Json 解码器背后的想法是你有一个基本解码器的集合(`string`、`number`、`boolean`、`object`、`array`...)可以组成更复杂的解码器。

## 最先进的 JSON 解码器库

有几个 JSON 解码库，但是当我不久前进行研究时，有一个非常突出。Daniel Van Den Eijkel 创造了一些东西，既保留了 Elm 解码库的原则，又符合打字稿的习惯。

不幸的是，这个库没有被维护和发布，所以我决定分叉它，润色它，并以名称 [ts.data.json](https://github.com/joanllenas/ts.data.json) 作为 npm 包发布。
我对这个库的贡献是文档、更好的错误报告、单元测试、API 改进、一些新的解码器和发布 npm 包。

## 使用 JSON 解码器

安装库:

```
npm install ts.data.json --save 
```

Enter fullscreen mode Exit fullscreen mode

### 解码基础知识

在实现我们的自定义`User`解码器之前，让我们从头到尾尝试解码一个`string`。

```
import { JsonDecoder } from 'ts.data.json';

console.log( JsonDecoder.string.decode('Hi!') ); // Ok({value: 'Hi!'}) 
```

Enter fullscreen mode Exit fullscreen mode

完了！🎉

### 解包解码结果

正如我们在前面的例子中看到的，解码过程有两个步骤。

*   首先，我们用`JsonDecoder.string`声明解码器。
*   其次，我们执行解码器，用`*.decode('Hi!')`传递一个 JavaScript 值，它返回包装在`Ok`实例中的结果。

为什么我们将结果包装在一个`Ok`实例中？因为万一失败，我们会将结果包装在一个`Err`实例中。
让我们看看`decode()`的签名是什么样子的:

```
decode(json: any): Result<a> 
```

Enter fullscreen mode Exit fullscreen mode

`Result<a>`是`Ok`和`Err`的联合类型。

```
type Result<a> = Ok<a> | Err; 
```

Enter fullscreen mode Exit fullscreen mode

所以大多数时候我们不会使用`decode()`，相反我们可能会想要使用`decodePromise()`。
让我们看看`decodePromise()`的签名是什么样子的:

```
decodePromise<b>(json: any): Promise<a> 
```

Enter fullscreen mode Exit fullscreen mode

让我们尝试使用`decodePromise()` :
从头到尾解码一个`string`

```
import { JsonDecoder } from 'ts.data.json';

const json = Math.random() > 0.5 ? 'Hi!' : null;
JsonDecoder.string.decodePromise(json)
  .then(value => {
    console.log(value);
  })
  .catch(error => {
    console.log(error);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

一半时间我们会通过`then()`路线得到`Hi!`，一半时间我们会通过`catch()`路线得到`null is not a valid string`。

既然我们知道了基础知识，让我们认真起来，构建我们的定制`User`解码器。

### `User`解码器

除了原始解码器:

*   `JsonDecoder.string: Decoder<string>`
*   `JsonDecoder.number: Decoder<number>`
*   `JsonDecoder.boolean: Decoder<boolean>`

还有其他更复杂的解码器，对于我们的`User`，我们将使用`JsonDecoder.object`解码器:

*   `JsonDecoder.object<a>(decoders: DecoderObject<a>, decoderName: string): Decoder<a>`

> #### 所有解码器都在退的那个`Decoder<a>`是什么东西？
> 
> 解码器有解码特定值的逻辑，但他们不知道如何执行它，这就是`Decoder`类的用途。
> `Decoder<a>`拥有执行、解包、链接和转换解码器/解码器值的方法。

让我们尝试使用我们到目前为止学到的所有技巧从头到尾解码一个`User`:

```
import { JsonDecoder } from 'ts.data.json';

interface User {
  firstName: string;
  lastName: string;
}

const userDecoder = JsonDecoder.object<User>(
  {
    firstName: JsonDecoder.string,
    lastName: JsonDecoder.string
  },
  'User'
);

const validUser = {
  firstName: 'Nils',
  lastName: 'Frahm'
};

const invalidUser = {
  firstName: null,
  lastName: 'Wagner'
};

const json = Math.random() > 0.5 ? validUser : invalidUser;

userDecoder
  .decodePromise(json)
  .then(value => {
    console.log(value);
  })
  .catch(error => {
    console.log(error);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

一半时间我们会得到`{firstName: "Nils", lastName: "Frahm"}`，一半时间我们会得到`<User> decoder failed at key "firstName" with error: null is not a valid string`。`JsonDecoder`掩护我们。

## 下兔子洞

我们刚刚开始了解这个库的皮毛，你能想到的每种类型都有解码器。您还可以解码:

*   数组
*   字典
*   递归数据结构
*   空
*   不明确的

和其他新奇的东西。

去 [GitHub repo](https://github.com/joanllenas/ts.data.json) 了解一下吧！