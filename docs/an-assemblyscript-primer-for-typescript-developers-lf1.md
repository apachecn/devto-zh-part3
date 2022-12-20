# 汇编脚本入门(面向类型脚本开发人员)

> 原文：<https://dev.to/jtenner/an-assemblyscript-primer-for-typescript-developers-lf1>

编辑:本指南直到主题是关于将二进制数据推送到 wasm 的结尾都是准确的。这个很快会更新！请明天再来检查！

利用 web 组装是一个相当大的挑战，但是有许多好的方法可以做到这一点。使用 emscripten 工具链，或者 rust wasm 编译目标，对于那些坐在金属附近的开发人员来说是一个很好的方法，可以让他们的手变脏，让 web 应用程序由 web assembly 驱动。

但是，普通开发人员可能会发现，汇编脚本(它将类型脚本的一种方言编译成 Web 程序集)可能更容易使用！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [组装脚本](https://github.com/AssemblyScript) / [组装脚本](https://github.com/AssemblyScript/assemblyscript)

### 肯定不是 WebAssembly 编译器的类型脚本🚀

<article class="markdown-body entry-content container-lg" itemprop="text">

[![AssemblyScript logo](img/991b4653baee4f5000ba823d7265b53e.png)](https://assemblyscript.org)

[![Test status](img/76340ce00a0a10c745566947f91c0a1c.png)](https://github.com/AssemblyScript/assemblyscript/actions?query=workflow%3ATest)[![Publish status](img/699ea13a9ee0376aca5fde6fce52fcaf.png)](https://github.com/AssemblyScript/assemblyscript/actions?query=workflow%3APublish)[![npm compiler version](img/8145eb2c775dde2e06500dca701c0ad9.png)](https://www.npmjs.com/package/assemblyscript)[![npm loader version](img/9cea788c57f063697d742cfe1721459d.png)](https://www.npmjs.com/package/@assemblyscript/loader)[![Discord online](img/f55d42249a841f29fffb440efb3aa83e.png)](https://discord.gg/assemblyscript)

**AssemblyScript** 使用 [Binaryen](https://github.com/WebAssembly/binaryen) 将 [TypeScript](http://www.typescriptlang.org) (基本上是带类型的 JavaScript)的严格变体编译成 [WebAssembly](http://webassembly.org) 。它生成精益和平均的 WebAssembly 模块，而仅仅是一个`npm install`之遥。

### [关于](https://assemblyscript.org) [简介](https://assemblyscript.org/introduction.html) [快速入门](https://assemblyscript.org/quick-start.html) [开发说明](https://assemblyscript.org/development.html)

## 贡献者

[![Contributor logos](img/078a49b07397ff575b3d1e7b61be51a0.png)](https://assemblyscript.org/#contributors)

## 感谢我们的赞助商！

大多数核心团队成员和大多数贡献者都是在空闲时间做这项开源工作的。如果你使用 AssemblyScript 完成一项重要的任务或者打算这样做，并且你希望我们在它上面投入更多的时间，[请将](https://opencollective.com/assemblyscript/donate)捐赠给我们的[open collection](https://opencollective.com/assemblyscript)。通过赞助这个项目，你的标志将出现在下面。非常感谢你的支持！

[![Sponsor logos](img/229ffec963204f876b62fb4ed1efdd53.png)](https://assemblyscript.org/#sponsors)

</article>

[View on GitHub](https://github.com/AssemblyScript/assemblyscript)

AssemblyScript 目前在 github 上以最新和最自豪的形式发布。在 dev 分支上是一个垃圾收集版本，它使用引用计数来帮助内存管理。我们将使用 dev 分支作为例子。

AssemblyScript 绝对是我用过的最有趣的语言之一。使用和编写 TypeScript 来获得本机性能就像将火箭船附在蒸汽机车上一样。如果听起来很疯狂，你是对的。

我想把一些事情说清楚。每一个编程解决方案都只是另一套需要解决和学习的问题。所以不要烦恼！启动一个测试项目，尽可能地让你的手脏起来。

## 如何使用汇编脚本

所以让我们开始吧！我们需要安装一个新项目，所以让我们运行一些命令:

```
npm init
npm install assemblyscript/assemblyscript
npx asinit . 
```

它会问你几个问题，创建一个文件夹结构，接触几个文件，并修改你的`package.json`文件以包含几个方便的 npm 脚本来帮助你开始。以下是它将创建和修改的重要文件的列表:

```
 Modified: ./package.json
Directory: ./assembly/
  Created: ./assembly/index.js
  Created: ./assembly/tsconfig.json 
```

请注意，AssemblyScript 创建了一个特殊的 typescript 配置。这是因为 AssemblyScript 附带了一组类型和规则。维护这些类型以匹配 AssemblyScript 标准库中的函数。

首先，我们来看一下`./assembly/index.ts`文件。

```
// ./assembly/index.ts
/**
 * Exporting a function from the index.ts file will cause AssemblyScript to
 * generate a corresponding function that will be exported to JavaScript.
 **/
export function add(a: i32, b: i32): i32 {
  return a + b;
} 
```

AssemblyScript 有一些特殊的数字类型，它们将在本文档的后面解释。相反，现在，让我们实际构建这个 AssemblyScript 模块。我们通过运行为我们创建的`asbuild` npm 脚本来做到这一点。

```
npm run asbuild 
```

这就创建了一堆新的 web 组件模块和一个新创建的`./build/`文件夹。

```
Created: ./build/optimized.wasm
Created: ./build/optimized.wasm.map
Created: ./build/optimized.wat
Created: ./build/untouched.wasm
Created: ./build/untouched.wasm.map
Created: ./build/untouched.wat 
```

调试你的模块时，最好使用`untouched`版本。当您准备好与您的模块捆绑在一起时，请发布`optimized`版本。AssemblyScript 也生成一个 sourcemap 和一个`.wat`文件，它是生成的`.wasm`文件的文本表示。如果你刚刚开始，你一定要检查一下`.wat`文件，看看它生成了什么。

## 消耗 AssemblyScript 模块

AssemblyScript 自带加载器。它提供了一种实例化模块的标准方法。

```
// ./src/index.ts

/**
 * Import the AssemblyScript loader here. If this code runs in the browser,
 * call the `instantiateStreaming` function, otherwise the `instantiateBuffer`
 * method is used in node.js.
 */
import { instantiateStreaming, ASUtil } from "assemblyscript/lib/loader";

/**
 * Defining an interface like this allows you to define the shape of the exported
 * Web Assembly module. Each parameter is a number. Later, when we want to push
 * a string into our module, we will have to generate a pointer to a string.
 * The add function takes two integer parameters and will assume the value is `0`
 * if the parameter is not provided.
 */
interface MyApi {
  add(a: number, b: number): number;
}

/**
 * Imports are used to specify functions that need to be linked. This will be
 * discussed in greater detail later. For now, leave the imports object empty.
 **/
const imports: any = {};

/**
 * Now, let's instantiate our module. Using `fetch()` allows the browser to 
 * download and parse the module at exactly the same time.
 */
async function main(): void {
  var interop: ASUtil & MyApi =
    await instantiateStreaming<MyApi>(fetch("../build/untouched.wasm"), imports);

  // Finally, call the add function we exported
  console.log("The result is:", interop.add(1, 2));
} 
```

控制台应该输出预期的结果，即...

```
The result is: 3 
```

接下来，我们来讨论一下语言本身。

## 汇编脚本语言

### 汇编脚本编号

AssemblyScript 编程语言是 TypeScript 的一个稍加修改的子集。它附带了一个 JavaScript 开发人员应该熟悉的标准库，因为他们的目标是尽可能接近 ECMAScript 规范。事实上，您需要的大多数功能都已经实现了，或者以一种与从 web assembly 中运行相关的特殊方式进行了限制。

AssemblyScript 直接利用了 Web 程序集规范中实现的专用数字类型。因此，当使用现有的 TypeScript 工具时，Web Assembly 使用的 number 类型都有一个解析为`number`的类型别名。

```
var int8: i8 = <i8>0; // 8-bit signed integer [-128 to 127]
var uint8: u8 = <u8>0; // 8-bit unsigned integer [0 to 255]
var int16: i16 = <i16>0; // 16-bit signed integer [-32,768 to 32,767]
var uint16: u16 = <u16>0; // 16-bit unsigned integer [0 to 65,535]
var int32: i32 = <i32>0; // 32-bit signed integer [-2,147,483,648 to 2,147,483,647]
var uint32: u32 = <u32>0; // 32-bit unsigned integer [0 to 4,294,967,295]
var int64: i64 = <i64>; // 64-bit signed integer [-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807]
var uint64: i64 = <u64>0; // 64-bit unsigned integer [0 to 18,446,744,073,709,551,615]
var float32: f32 = <f32>0.0; // 32-bit float [32 bit float range]
var float64: f64 = <f64>0.0; // 64-bit float [64 bit float range]
var pointer: usize = <usize>0; // a 32/64-bit pointer to a location in memory 
```

所有编号类型都存储在`i32`、`i64`、`f32`和`f64`值中，就像常规的 web 组件编号一样。

一些数学运算需要显式转换为不同的数字类型。如果你搞砸了，不小心把一个浮点数和一个整数加在一起，编译器会提醒你进行适当的数字强制转换。

### 汇编脚本函数

大多数函数必须用返回类型进行注释。

```
function add(a: i32, b: i32): i32 {
  return a + b;
}

var myVerboseSumFunction: (a: i32, b: i32) => i32 =
  (a: i32, b: i32): i32 => a + b; 
```

这变得非常冗长，尤其是当你只想把两个数字加在一起的时候。然而，在编写函数时明确地注释它们是一个很好的习惯。当执行数组映射和归约时，你的箭头函数可能看起来有点难看。因为还没有支持的闭包。可能有必要将这些函数提升到模块的全局范围。

```
/**
 * Use every function parameter here. It's quite verbose.
 **/
function add(left: i32, right: i32, index: i32, self: Int32Array): i32 {
  return left + right;
}

var myArray: Int32Array = new Int32Array(100);

var sum: i32 = myArray.reduce(add); 
```

现在 AssemblyScript 有了引用计数，闭包最终会得到支持。

### 汇编脚本类

在 AssemblyScript 中，类很容易使用。举个例子，我们来看一个向量类:

```
/**
 * Exporting a class in an AssemblyScript module only exports its functions.
 * Emscripten generates glue code for classes, and AssemblyScript does not come
 * with this feature. Instead, exporting a class to JavaScript will add all the
 * prototype functions, property gets/sets, and constructor functions to the
 * exports object.
 */
export class Vec_3 {

  /**
   * Constructors work exactly like TypeScript.
   */ 
  constructor(
    public x: f64 = 0.0,
    public y: f64 = 0.0,
    public z: f64 = 0.0,
  ) {}

  /**
   * Operator overloading is supported in AssemblyScript using the `@operator`
   * decorator. We can even ask this computation to happen inline with the 
   * `@inline` function decorator to cause this computation to happen in an 
   * "inline" fashion. It's limited to operations that can be performed on the 
   * *same* object type. Calling a "+" operator on a `Matrix` with a `Vector` is 
   * not valid in AssemblyScript.
   */
  @inline @operator("+")
  protected add_vector(value: Vec_3): Vec_3 {
    return new Vec_3(this.x + value.x, this.y + value.y, this.z + value.z);
  }

  /**
   * To make a computed property, follow the ECMAScript syntax for computed
   * properties.
   **/
  public get length(): f64 {
    return Math.sqrt(this.x * this.x + this.y * this.y + this.z + this.z);
  }
} 
```

如果一个类型需要可空，那么我们使用类型联合语法。

```
/**
 * Nullable types are only valid on *reference* types, or strings.
 **/
var my_vector: Vec_3 | null = null;

/**
 * Nullable numbers, when set to null will equal `0`. Currently, this is a
 * limitation of the Web Assembly specification. AssemblyScript cannot discern
 * between the number `0` and `null`. Therefore, all the number types must be
 * represented as valid numeric values.
 **/
var my_number: i32 | null = null;
assert(my_number == 0); // true 
```

如果使用可为空的数字类型，AssemblyScript 编译器将显示警告。当支持多值返回类型时，这种情况有望改变。多值返回类型将允许返回堆栈上的多个数字，而不仅仅是一个值，从而允许 AssemblyScript 区分`null`和`0`。

您可以在此查看关于多值回报的概述:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ WebAssembly ](https://github.com/WebAssembly) / [多值](https://github.com/WebAssembly/multi-value)

### 向 WebAssembly 添加多值的建议

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Build Status](img/d71bd569a88c31ff0f38271c123c1274.png)](https://travis-ci.org/WebAssembly/multi-value)

# WebAssembly 的多价值提案

**注:本建议书已于 2020/04/09 并入 WebAssembly 标准。**

这个库是 github.com/WebAssembly/spec/的克隆。它是为了讨论，原型规范和一个提案的实现，以增加对 WebAssembly 返回多个值的支持。

*   请参见[概述](https://raw.githubusercontent.com/WebAssembly/multi-value/master/proposals/multi-value/Overview.md)了解提案摘要。

*   详见[修改规格](https://webassembly.github.io/multi-value/)。

来自上游储存库的原件`README`如下...

# 投机

这个存储库包含 WebAssembly 的原型参考实现，它目前是官方规范。最终，我们期望产生一个用人类可读的散文或者正式的规范语言编写的规范。

它还拥有 WebAssembly testsuite，用于测试规范一致性的许多方面。

在[webassembly.github.io/spec](https://webassembly.github.io/spec/)查看半成品规格。

此时，该存储库的内容正在开发中，并被认为是“不完整和不正确的”。

欢迎参与。关于新功能、重要功能的讨论…

</article>

[View on GitHub](https://github.com/WebAssembly/multi-value)

AssemblyScript 类的另一个真正受支持的特性是泛型！

```
/**
 * This is a class that pushes `T` values to the protected `data` array.
 **/
class Writer<T> {
  protected data: T[] = new Array<T>(0); 
  constructor() { }

  @inline
  protected write(value: T): void {
    this.data.push(value);
  }
} 
```

类扩展也像预期的那样工作。

```
class MyWriter extends Writer<f64> {
  constructor() {
    super(); // always call super
  }

  /**
   * Access parent class functions on `super`!
   **/
  public write_value(value: f64): void {
    super.write(value);
  }
} 
```

### 链接到 Javascript

AssemblyScript 重用关键字`declare`来指定一个链接函数。通常`declare`用于描述在别处定义的现有环境函数，但是在这里使用这个语法来生成 Web 程序集导入是有意义的。

就拿下面这个导入的函数来说吧。

```
var imports: any = {
  customMath: {
    add(a: number, b: number): number {
      return a + b;
    }
  }
};

var wasm: ASUtil = instantiateStreaming<MyAPI>(fetch("./module.wasm"), imports); 
```

接下来，告诉模块在哪里可以找到模块中的 add 函数。

```
// @ts-ignore: import a custom_add function from the customMath.add namespace
@external("customMath", "add")
export declare function custom_add(a: f64, b: f64): f64; 
```

当 Web Assembly 调用 JavaScript 时，每个参数，不管是什么类型，都将成为 64 位浮点数。这与返回对象引用和字符串是一样的。例如，向 JavaScript 传递一个`Image`或一个`string`可能看起来像这样:

```
class Image {
  width: i32;
  height: i32;
}

@external("image", "load")
export declare function image_load(image: Image, source: string): void; 
```

然后，我们必须要求 AssemblyScript 加载程序解包字符串值，并自己手动访问内存。

```
// ./src/index.ts

var wasm: ASUtil<T>;

var imports = {
  image: {
    // image references and string references are pointers
    load(imgPointer: number, sourcePointer: number): void {
      // Do something with source now
      var source: string = wasm.__getString(sourcePointer);
      fetch(source).then(e => e.blob())
        .then(e => createImageBitmap(e))
        .then(e => {
          var imageIndex: number = imgPointer / 4; // the pointer is properly aligned
          wasm.I32[imageIndex] = e.width; // width property is first i32
          wasm.I32[imageIndex + 1] = e.height; // height is second property
        });
    }
  }
};

wasm = await instantiateStreaming<T>(fetch("my/assemblyscript.wasm"), imports); 
```

从 web 程序集读写内存是非常容易管理的。

# 结论

每种语言都有局限性和陷阱。AssemblyScript 也不例外，但它是一个令人着迷和惊奇的加速代码的工具，你可以很容易地把它放在你的工具带上。请随时对本文提供反馈，或者在评论区提出任何问题。

不要忘记使用测试框架。😉

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [詹纳](https://github.com/jtenner) / [至于](https://github.com/jtenner/as-pect)

### 🔥强烈的🔥用汇编脚本进行快速测试

<article class="markdown-body entry-content container-lg" itemprop="text">

# jtenner/as-pect

这个包是一个 monorepo，包含 cli 和`@as-pect`包的核心。

[![Greenkeeper badge](img/5cdc90a38632362af8046ca689938e38.png)](https://greenkeeper.io/)[![Build Status](img/5ba3bac52136a284cfa710e93562662c.png)](https://travis-ci.org/jtenner/as-pect)[![Coverage Status](img/31f04fb04873034e57430b913897975f.png)](https://coveralls.io/github/jtenner/as-pect?branch=master)[![lerna](img/fd8983decc3fc5bab4a2d10c5def50a9.png)T11】](https://lerna.js.org/)

用 AssemblyScript 编写你的模块，以 WebAssembly 的速度获得极快的引导测试！

## 证明文件

要查看文档，可以在 gitbook 上的[这里](https://tenner-joshua.gitbook.io/as-pect/)找到。如果文件有任何问题，请随时提出问题！

## 贡献者

若要投稿，请参阅 [CONTRIBUTING.md](https://raw.githubusercontent.com/jtenner/as-pect/master/./CONTRIBUTING.md) 。

感谢 [@willemneal](https://github.com/willemneal) 和 [@MaxGraey](https://github.com/maxgraey) 的支持，让`as-pect`成为最好的软件。

其他贡献者:

*   [@trusktr](https://github.com/trusktr) -文档变更
*   [@MaxGraey](https://github.com/maxgraey) -性能 API 建议
*   [@torch2424](https://github.com/torch2424) -文档变更
*   [@dcodeio](https://github.com/dcodeio) -自己做了 AssemblyScript！
*   [@9oelM](https://github.com/9oelM) -其他功能

## 特别感谢

特别感谢 [AssemblyScript](https://github.com/AssemblyScript/assemblyscript) 团队创建了 AssemblyScript 本身。

</article>

[View on GitHub](https://github.com/jtenner/as-pect)

编码快乐！约什