# 在 Web 汇编中 C++和 JavaScript 之间传递结构化数据

> 原文：<https://dev.to/azure/passing-structured-data-from-c-to-javascript-in-web-assembly-1i0p>

我最近通过 Web Assembly(简称 WASM)完成了我的[无线 LED 控制系统](https://github.com/nebrius/raver-lights)在 Node.js 中运行的[消息栈](https://github.com/nebrius/RaverLightsMessaging)。我遇到的最后一个主要障碍是如何在 JavaScript 和 C++之间传递大量结构化数据。

# 场景

通过我的消息传递堆栈传递的核心数据是控制 LED 动画所需的一组控制参数。这些信息在 C++中使用以下结构定义:

```
#define NUM_WAVES 4 
struct RVLWaveChannel {
  uint8_t a = 0;
  uint8_t b = 0;
  int8_t w_t = 0;
  int8_t w_x = 0;
  int8_t phi = 0;
};

struct RVLWave {
  RVLWaveChannel h;
  RVLWaveChannel s;
  RVLWaveChannel v;
  RVLWaveChannel a;
};

struct RVLWaveSettings {
  uint8_t timePeriod = 255;
  uint8_t distancePeriod = 32;
  RVLWave waves[NUM_WAVES];
}; 
```

我的动画算法用一堆系数来计算一系列叠加的正弦波。这是如何工作的细节是另一天虽然，只是知道它看起来真的真的很漂亮！对于这篇博文，你需要知道的是有 82 个(！！)需要从 JavaScript 传递到 C++的数值，反之亦然。

正如我在本系列的上一篇文章中提到的，在 WASM，您只能在 C++和 JavaScript 之间传递数字。这个结构化数据*是*数字的，但是它也是结构化的。在传递数据时，我们必须保留这种结构和数值。

我们可以实现一个有 82 个参数的函数...但我是说，拜托，真的吗？我只知道我会把事情搞砸！如果数据发生变化，这种方法也会使更新变得非常困难。所以我们需要别的东西。我考虑过序列化为 JSON 字符串，然后再反序列化，但是这需要大量的工作、处理时间，并且在 C++端代码膨胀。

我需要的是聪明的东西...

# 我的解决方案

我发现聪明的是！我记得 C/C++中的结构是以确定的方式在内存中布局的。我意识到有了这些知识，我可以直接用 JavaScript 从内存数组中封送和解封送数据，就像我处理字符串一样！

为了说明我所说的，让我们来看一个非常简单的结构:

```
struct MyStruct {
  uint8_t a = 0x12;
  uint16_t b = 0x3456;
  uint32_t c = 0x789ABCDE;
};

MyStruct str; 
```

如果我们检查`str`指向的内存(例如 C/C++中的`&str`的数值)，我们会看到以下内容:

| str + 0 | str + 1 | str + 2 | str + 3 | str + 4 | str + 5 | str + 6 |
| --- | --- | --- | --- | --- | --- | --- |
| 0x12 | 0x34 | 0x56 | 0x78 | 0x9A | 0xBC | 0xDE |

通过使用 C++中的`sizeof()`操作符，我们知道这个结构有 7 个字节大，与上面的布局相匹配。我们还可以看到，这些值在内存中是紧挨着堆叠的！我们只需要知道每个值相对于基指针的“内存偏移量”，也就是表中的`+ n`部分。

那么，我们如何确定这个偏移量呢？C/C++总是按照在源代码的结构中声明的顺序在内存中排列这些属性。在这个例子中，`a`排在第一位，接着是`b`，然后是`c`，因为我在代码中是按照这个顺序声明它们的。如果我们交换`b`和`c`的顺序，使得`b`位于源代码的末尾，那么`b`也将位于内存块的末尾。

这意味着我们可以计算每一个偏移量，将它之前的每一个条目的大小相加。

## 自动计算偏移量

但是手工计算这些容易出错，*尤其是像我一样当结构引用其他结构时。如果我改变了结构中的数据，我还必须重新计算这些偏移量。这是一个用构建时脚本自动化过程的*完美的*机会！*

你可以看到我在 GitHub 上写的 [Node.js 脚本(诚然评价很差)。](https://github.com/nebrius/RVL-Node/blob/master/scripts/wave.js)

我做的第一件事是使用 regex 的编写一个 quick-n-dirty C++解析器。这个解析产生的数据结构如下:

```
const structs = {
  RVLWaveChannel: [
    { name: 'a', type: 'uint8_t', initialValue: 0 },
    { name: 'b', type: 'uint8_t', initialValue: 0 },
    { name: 'w_t', type: 'int8_t', initialValue: 0 },
    { name: 'w_x', type: 'int8_t', initialValue: 0 },
    { name: 'phi', type: 'int8_t', initialValue: 0 }
  ],
  RVLWave: [
    { name: 'h', type: 'RVLWaveChannel', initialValue: undefined },
    { name: 's', type: 'RVLWaveChannel', initialValue: undefined },
    { name: 'v', type: 'RVLWaveChannel', initialValue: undefined },
    { name: 'a', type: 'RVLWaveChannel', initialValue: undefined }
  ],
  RVLWaveSettings: [
    { name: 'timePeriod', type: 'uint8_t', initialValue: 255 },
    { name: 'distancePeriod', type: 'uint8_t', initialValue: 32 },
    { name: 'waves', type: 'array', subType: 'RVLWave', arraySize: 4 }
  ]
}; 
```

现在我们有了 JavaScript 中 C++结构的表示。不过，我们还没有准备好开始计算补偿。在我们的两个结构中有对其他结构的引用，我们还有一个数组。当这个结构在 C++中被实例化时，这些不同的结构和数组不会被表示为指向多个内存块的指针。更确切地说，结构和数组是“扁平的”,它们都位于一个 82 字节的内存块中。

为了准确地表示内存中的这种扁平化，我们也必须扁平化我们自己对这些结构和数组的表示。我通过编写一个 while 循环来实现这一点，该循环遍历“根”结构中的每个条目(在本例中为`RVLWaveSettings`)。然后，我们用它的“引用”类型替换任何其`type`值不是来自`stdint.h`的原语的条目(例如，`[u?]int[8|16|32]_t`形式的条目)。我们进行这种替换的方式取决于它是结构还是数组。while 循环会一直运行，直到不再需要替换为止。

当循环遇到一个项目数组时，它会“展开”该数组。换句话说，它替换了:

```
{ name: 'waves', type: 'array', subType: 'RVLWave', arraySize: 4 } 
```

同:

```
{ name: 'waves[0]', type: 'RVLWave', initialValue: undefined }
{ name: 'waves[1]', type: 'RVLWave', initialValue: undefined }
{ name: 'waves[2]', type: 'RVLWave', initialValue: undefined }
{ name: 'waves[3]', type: 'RVLWave', initialValue: undefined } 
```

当迭代的每个循环遇到一个结构类型时，它用该结构的整个条目替换对该结构的引用。换句话说，它替换了:

```
{ name: 'waves[0]', type: 'RVLWave', initialValue: undefined } 
```

同:

```
{ name: 'waves[0].h', type: 'RVLWaveChannel', initialValue: undefined }
{ name: 'waves[0].s', type: 'RVLWaveChannel', initialValue: undefined }
{ name: 'waves[0].v', type: 'RVLWaveChannel', initialValue: undefined }
{ name: 'waves[0].a', type: 'RVLWaveChannel', initialValue: undefined } 
```

如果我们继续运行这个算法，我们最终会得到一组如下所示的条目:

```
{ name: "timePeriod", type: "uint8_t", initialValue: 255, size: 1 }
{ name: "distancePeriod", type: "uint8_t", initialValue: 32, size: 1 }
{ name: "waves[0].h.a", type: "uint8_t", initialValue: 0, size: 1 }
{ name: "waves[0].h.b", type: "uint8_t", initialValue: 0, size: 1 }
{ name: "waves[0].h.w_t", type: "int8_t", initialValue: 0, size: 1 }
{ name: "waves[0].h.w_x", type: "int8_t", initialValue: 0, size: 1 }
{ name: "waves[0].h.phi", type: "int8_t", initialValue: 0, size: 1 }
{ name: "waves[0].s.a", type: "uint8_t", initialValue: 0, size: 1 }
... 
```

有了这个，我们现在可以循环并计算偏移量了！我遍历每个条目并保存一个大小的运行总和，这是每个条目的内存偏移量。然后，我将这些信息写入一个 JSON 文件，如下所示:

```
{  "totalSize":  82,  "entryDictionary":  {  "timePeriod":  {  "name":  "timePeriod",  "type":  "uint8_t",  "initialValue":  255,  "size":  1,  "index":  0  },  "distancePeriod":  {  "name":  "distancePeriod",  "type":  "uint8_t",  "initialValue":  32,  "size":  1,  "index":  1  },  "waves[0].h.a":  {  "name":  "waves[0].h.a",  "type":  "uint8_t",  "initialValue":  0,  "size":  1,  "index":  2  },  ...  }  } 
```

## 在 JavaScript 中使用偏移量从 C++结构中读取

现在我们有了偏移量，我们终于可以开始来回传递数据了！让我们从讨论如何将数据从 C++读入 JavaScript 开始。我们从字符串开始:通过创建一个 Node.js `Buffer`对象来表示包含我们想要读取的结构的内存区域。然后我们遍历偏移量数据中的每个元素，并读取给定偏移量处的值:

```
const view = Buffer.from(memory.buffer, waveSettingsPointer, structData.totalSize);
for (const entryName in structData.entryDictionary) {
  const structEntry = structData.entryDictionary[entryName];
  let value = 0;
  switch (structEntry.type) {
    case 'uint8_t':
      value = view.readUInt8(structEntry.index);
      break;
    case 'int8_t':
      value = view.readInt8(structEntry.index);
      break;
    case 'uint16_t':
      value = view.readUInt16BE(structEntry.index);
      break;
    case 'int16_t':
      value = view.readInt16BE(structEntry.index);
      break;
    case 'uint32_t':
      value = view.readUInt32BE(structEntry.index);
      break;
    case 'int32_t':
      value = view.readInt32BE(structEntry.index);
      break;
    default:
      throw new Error(`Unexpected struct type "${structEntry.type}"`);
  }
  // Assign the value we just read to a JavaScript mirror object
  // using some dense code I'd rather not show here :-P
} 
```

然后，我们以使用以下类型脚本接口定义的 JavaScript 数据结构结束:

```
export interface IWaveChannel {
  a: number; // Default 0
  b: number; // Default 0
  w_t: number; // Default 0
  w_x: number; // Default 0
  phi: number; // Default 0
}

export interface IWave {
  h: IWaveChannel;
  s: IWaveChannel;
  v: IWaveChannel;
  a: IWaveChannel;
}

export interface IWaveParameters {
  timePeriod?: number; // Default 255
  distancePeriod?: number; // Default 32
  waves: IWave[];
} 
```

看起来很眼熟，对吧？

从 JavaScript 写入 C++结构实际上是上述过程的逆过程。要查看所有进行编组和解组的代码，请查看 GitHub 上的 [bridge.ts。](https://github.com/nebrius/RVL-Node/blob/master/src/bridge.ts)

就是这样，我们现在可以将 C++的结构传递给 JavaScript，反之亦然！这听起来像是你认为很简单的事情，但这似乎是 WASM 意料之中的事情。无论如何，这一机制标志着通过 Node.js 将该系统与 [Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/?WT.mc_id=devto-blog-brhugh) 集成的下一大步！

如果我将来有时间，我很想增强我的解析脚本，使用一个合适的 C++ AST 解析器，这样它就可以处理更广泛的代码，并将所有这些作为一个易于使用的模块发布在 npm 上。

你可以在 GitHub 的 [RVL 节点库中查看 Node.js 消息库的完整代码。](https://github.com/nebrius/RVL-Node)