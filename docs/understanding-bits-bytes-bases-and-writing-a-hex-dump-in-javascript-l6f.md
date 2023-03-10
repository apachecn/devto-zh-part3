# 理解位、字节、基数，并用 JavaScript 编写十六进制转储

> 原文：<https://dev.to/taniarascia/understanding-bits-bytes-bases-and-writing-a-hex-dump-in-javascript-l6f>

我最近的任务是创建一个简单的命令行程序，它接受一个未知内容的文件的输入，并打印一个十六进制转储作为输出。然而，我真的不知道如何开始访问文件的数据，也不知道什么是十六进制转储。所以我要和大家分享我学到的东西，以及我为了完成这个任务而写的东西。

因为我最熟悉 JavaScript，所以我决定在 Node 中这样做。目的是写一个这样的命令:

```
node hex.js data 
```

它将在一个文件(`data`)上运行一个`hex.js`程序，并输出十六进制转储。

文件可以是任何东西——图像、二进制文件、常规文本文件或包含其他编码数据的文件。在我的特殊情况下，它是一个 ROM。

如果你曾经尝试过用文本编辑器打开一个非基于文本的文件，你会记得看到一堆杂乱无章的随机字符。如果您曾经想知道程序如何访问原始数据并使用它，这篇文章可能会有所启发。

本文将由两部分组成:第一部分，解释什么是十六进制转储、什么是位和字节、如何计算基数 2、基数 10 和基数 16 中的值的背景信息，以及对可打印 ASCII 字符的解释。第二部分将在 Node 中编写十六进制转储函数。

## 什么是十六进制转储？

为了理解什么是十六进制转储，我们可以创建一个文件并查看它。我将创建一个简单的文本文件，其中包含一段鲍勃·罗斯语录。(`-en`这里是防止尾随换行符，并允许解释反斜杠转义字符，这将很快派上用场。)

```
echo -en "Just make a decision and let it go." > data 
```

`data`只是一个文件名，不是任何命令或关键字。

Unix 系统已经有了一个 [hexdump 命令](http://man7.org/linux/man-pages/man1/hexdump.1.html)，我将使用 canonical ( `-C`)标志来格式化输出。

```
hexdump -C data 
```

这是我得到的。

```
00000000  4a 75 73 74 20 6d 61 6b  65 20 61 20 64 65 63 69  |Just make a deci|
00000010  73 69 6f 6e 20 61 6e 64  20 6c 65 74 20 69 74 20  |sion and let it |
00000020  67 6f 2e                                          |go.|
00000023 
```

好，我有一串数字，在右边我们可以看到我刚才回显的字符串中的文本字符。手册页告诉我们`hexdump`“以十六进制、十进制、八进制或 ascii 显示文件内容”。这里使用的具体格式(规范)将进一步解释:

> `-C, --canonical`
> 
> 标准十六进制+ASCII 显示。以十六进制的
> 格式显示输入偏移，后面是十六个空格分隔的两列
> 十六进制字节，后面是用“ **|** ”字符括起来的 **%_p**
> 格式的相同十六个字节。

现在我们可以看到，每一行都是一个十六进制输入偏移量(地址)，有点像行号，后跟 16 个十六进制字节，再后跟两个管道之间 ASCII 格式的相同字节。

| 地址 | Hexadecimal bytes | 美国信息交换标准代码 |
| --- | --- | --- |
| `00000000` | `4a 75 73 74 20 6d 61 6b 65 20 61 20 64 65 63 69` | `&#124;Just make a deci&#124;` |
| `00000010` | `73 69 6f 6e 20 61 6e 64 20 6c 65 74 20 69 74 20` | `&#124;sion and let it&#124;` |
| `00000020` | `67 6f 2e` | `&#124;go.&#124;` |
| `00000023` |  |  |

这对于查看 ASCII 文本是有意义的，但是不能用 ASCII 表示的数据怎么办呢？那看起来怎么样？在这个例子中，我将回显以 16/十六进制表示的 0-15，这将是`00`到`0f`。

```
echo -en "\x00\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f" > data2 
```

这些数字不对应任何 ASCII 字符，也不能在常规文本编辑器中查看。例如，如果您尝试在 VSCode 中打开它，您会看到“该文件没有显示在编辑器中，因为它是二进制文件或者使用了不支持的文本编码。”。

如果你决定打开它，你可能会看到一个问号。幸运的是，我们可以用 hexdump 查看原始内容。

```
00000000  00 01 02 03 04 05 06 07  08 09 0a 0b 0c 0d 0e 0f  |................|
00000010 
```

可以看到，不可打印的 ASCII 字符用一个`.`表示，字节确认为十六进制。地址的第二行有`10`,因为它从第 16 个字节开始，16 是十六进制的`10`。

## 了解字节和基数

查看`hexdump`表的“十六进制字节”部分，你必须知道“十六进制”是什么意思，以及“字节”是什么。

你可能已经知道一千字节大约是一千字节，或称`1024`字节，一兆字节大约是一千字节，或称`1,024 * 1,024`字节(`1,048,576`字节)，或者甚至知道一张软盘有`1,474,560`字节的存储空间。

但是字节到底是什么呢？

### 位、半字节和字节

位是二进制数字，是计算机上最小的数据形式，可以是`0`或`1`。像布尔值一样，一个位可以表示开/关、真/假等。一个半字节中有四位，一个字节中有八位。

| 单位 | 储存；储备 |
| --- | --- |
| 少量 | 二进制数字(`0`或`1`) |
| 轻咬 | 4 位 |
| 字节 | 8 位 |

计算机处理字节数据。

### [一个字节的值](#value-of-a-byte)

你有没有玩过一款在`255`时就已经达到库存物品数量上限的电子游戏？为什么会停在那个点上？

如果每个库存单位是一个字节，那么可以表示的最高值是多少？

这在二进制(基数为 2)中很容易看出来。对于一个字节，有 8 个 1 比特的槽。一个位的最高值是`1`，所以二进制 8 位的最高值是 8 `1` s。

#### 二进制:11111111 <sub>2</sub>

怎么知道`11111111`代表数字`255`(十进制)？从最不重要的值(一直到最右边的值)开始，将该数字乘以该位置的底数的结果，并将它们相加。

`1 * 2**7`+`1 * 2**6`+`1 * 2**5`+`1 * 2**4`+`1 * 2**3`+`1 * 2**2`+`1 * 2**1`+`1 * 2**0`=`255`

#### Decimal: 255 <sub>10</sub>

如果这没有意义，那就用十进制来考虑。例如，你知道`007`和`070`和`700`都是非常不同的值(前导零对值没有影响)。七是`7 * 10^0`，七十是`7 * 10^1`，七百是`7 * 10^2`。

| 数字 | 十进制表示 | 计算 |
| --- | --- | --- |
| 七 | `007` | `7 * 10^0`或`7 * 1` |
| 七十 | `070` | `7 * 10^1`或`7 * 10` |
| 七百 | `700` | `7 * 10^2`或`7 * 100` |

如我们所见，数字的位置决定了数值，我们可以用同样的计算方法得到十进制的`255`。

`2 * 10**2` + `5 * 10**1` + `5 * 10**0` = `255`

#### 十六进制:FF <sub>16</sub>

这个概念适用于任何基地。十六进制是以 16 为基数，`F`代表最大值，`15` ( `0`是一个值)。

`15 * 16**1` + `15 * 16**0` = `255`

#### 相同的数字

所以`11111111`、`255`、`FF`都代表同一个数，也恰好是一个字节的最大值。十六进制是表示字节值的一种方便、简洁的方式，因为它总是包含在两个字符中。

| 数字 | 基础 | 计算 |
| --- | --- | --- |
| `1111111` | 二进制的 | `1 * 2**7`+`1 * 2**6`+`1 * 2**5`+`1 * 2**4`+`1 * 2**3`+`1 * 2**2`+`1 * 2**1`+`1 * 2**0` |
| `255` | 小数 | `2 * 10**2` + `5 * 10**1` + `5 * 10**0` |
| `FF` | 十六进制的 | `2 * 10**2` + `5 * 10**1` |

### 代表其他碱基

编程语言将使用前缀来表示基数 10 以外的值。二进制是`0b`，十六进制是`0x`，所以你可以比如在一个节点 repl 里写`0b1111`或者`0xff`，它会输出十进制的值。

| 基础 | 前缀 |
| --- | --- |
| 二进制的 | `0b` |
| 十六进制的 | `0x` |

### 不同基数的计数

一个字节的最大值是`255`，一个半字节(4 位)的最大值是`15`。这是一个用二进制、十进制和十六进制计算`15`的图表。

| 二进制数
(基数 2) | 十进制
(基数为 10) | 十六进制
(基数 16) |
| --- | --- | --- |
| `0000` | `0` | `00` |
| `0001` | `1` | `01` |
| `0010` | `2` | `02` |
| `0011` | `3` | `03` |
| `0100` | `4` | `04` |
| `0101` | `5` | `05` |
| `0110` | `6` | `06` |
| `0111` | `7` | `07` |
| `1000` | `8` | `08` |
| `1001` | `9` | `09` |
| `1010` | `10` | `0a` |
| `1011` | `11` | `0b` |
| `1100` | `12` | `0c` |
| `1101` | `13` | `0d` |
| `1110` | `14` | `0e` |
| `1111` | `15` | `0f` |

就像十进制一样，任何基数中的前导零都不会影响值，但是十六进制通常以前导零书写，使得一个字节的表示总是两个字符。

所以现在我们应该对十六进制转储的地址和字节中表示的值有一个很好的了解。

### 可打印的 ASCII 字符

在`0x20`和`0x7e`之间的都是可打印的 ASCII 字符。这张图表显示了它们，以及它们对应的二进制、八进制、十进制和十六进制。在上面的`hexdump`例子中，我打印了`0x00`到`0x0f`，因为它们都没有用 ASCII 表示，所以它们显示为点。

## 用 JavaScript 编写十六进制转储

现在回到在 Node 中编写十六进制转储程序的原始任务。我们知道它应该是什么样子，我们理解原始数据的价值，但是从哪里开始呢？

我们知道我们希望程序如何运行。它应该能够使用文件名作为参数和十六进制转储。

```
node hex.js data 
```

所以很明显，我将制作`hex.js`，我也将制作一些新的数据，既有 ASCII 也有非 ASCII 可表示的数据。

```
echo -en "<blink>Talent is pursued interest</blink>\x00\xff" > data 
```

目标是产生这样的输出:

` ` 0000000000 3c 62 6c 69 6e 6b 3e 54 61 6c 65 6e 74 20 69 73 |人才是|
000000010 20 70 75 72 73 75 65 64 20 69 6e 74 65 72 65 72 65 73 |追求兴趣|
0000000020 74 3c 2f 62 6c 69 6e 6b 3e 00 ff | t..|
0000002b

```
 ### Getting a raw data buffer of a file

The first step is to obtain the data from the file somehow. I'll start by using the [file system module](https://nodejs.org/api/fs.html#fs_file_system).

```js
const fs = require('fs') 
```

为了得到文件名，我们只需要得到第三个命令行参数(`0`是节点二进制、`1`是`hex.js`、`2`是`data`)。

```
const filename = process.argv.slice(2)[0] 
```

我将使用 [`readFile()`](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback) 来获取文件的内容。(`readFileSync()`只是同步版本。)正如 API 所说，“如果没有指定编码，那么就返回原始缓冲区”，所以我们得到了一个缓冲区。(`utf8`是我们用来表示字符串的。)

```
function hexdump(filename) {
  let buffer = fs.readFileSync(filename)

  return buffer
}

console.log(hexdump(filename)) 
```

这将注销一个`<Buffer>`对象(为简洁起见，删除了值)。

```
<Buffer 3c 62 6c 69 6e 6b 3e 54 ... 69 6e 6b 3e 00 ff> 
```

好吧，这看起来很眼熟。多亏了这些背景知识，我们可以看到缓冲区是用十六进制表示的一组字节。你甚至可以看到我在那里回响的最后的`00`和`ff`。

### 用缓冲器工作

您可以将缓冲区视为一个数组。如果用`buffer.length`查长度，会得到`43`，对应的是字节数。因为我们需要 16 字节的行，所以我们可以循环遍历每 16 个字节，并将它们分割成块。

```
function hexdump(filename) {
  let buffer = fs.readFileSync(filename)
  let lines = []

  for (let i = 0; i < buffer.length; i += 16) {
    let block = buffer.slice(i, i + 16) // cut buffer into blocks of 16

    lines.push(block)
  }

  return lines
} 
```

现在我们有了一组更小的缓冲区。

```
 [ <Buffer 3c 62 6c 69 6e 6b 3e 54 61 6c 65 6e 74 20 69 73>,
  <Buffer 20 70 75 72 73 75 65 64 20 69 6e 74 65 72 65 73>,
  <Buffer 74 3c 2f 62 6c 69 6e 6b 3e 00 ff> ] 
```

### 计算地址

我们想用十六进制表示地址，你可以用`toString(16)`把一个数转换成十六进制的字符串。那我就在前面加几个零，这样长度总是一样的。

```
let address = i.toString(16).padStart(8, '0') 
```

那么如果我把地址和块放在一个模板字符串中会发生什么呢？

```
lines.push(`${address}  ${block}`) 
```

```
[ '00000000 <blink>Talent is',
  '00000010  pursued interes',
  '00000020 t</blink>\u0000�' ] 
```

模板试图将缓冲区转换为字符串。但是它不能按照我们想要的方式解释非 ASCII 字符，所以我们不能对 ASCII 输出那样做。不过，我们现在有正确的地址了。

### 创建十六进制和 ASCII 字符串

当您访问缓冲区中的每个值时，它会将其解释为原始数字，无论您选择将其表示为二进制、十六进制、ASCII 还是任何其他数字，都由您决定。我将为十六进制创建一个数组，为 ASCII 创建一个数组，然后将它们连接成字符串。这样，模板文字就已经有了一个可以使用的字符串表示。

为了获得 ASCII 字符，我们可以根据上面可打印的 ASCII 图表测试值- `>= 0x20`和`< 0x7f` -然后获得字符代码或一个点。获取十六进制值与获取地址相同——将其转换为 16 进制字符串并用`0`填充单个值。

我将在这一行添加一些空格，并将这几行转换成换行符分隔的字符串。

```
function hexdump(filename) {
  let buffer = fs.readFileSync(filename)
  let lines = []

  for (let i = 0; i < buffer.length; i += 16) {
    let address = i.toString(16).padStart(8, '0') // address
    let block = buffer.slice(i, i + 16) // cut buffer into blocks of 16
    let hexArray = []
    let asciiArray = []

    for (let value of block) {
      hexArray.push(value.toString(16).padStart(2, '0'))
      asciiArray.push(value >= 0x20 && value < 0x7f ? String.fromCharCode(value) : '.')
    }

    let hexString = hexArray.join('  ')
    let asciiString = asciiArray.join('')

    lines.push(`${address}  ${hexString} |${asciiString}|`)
  }

  return lines.join('\n')
} 
```

现在我们快到了。

```
00000000 3c 62 6c 69 6e 6b 3e 54 61 6c 65 6e 74 20 69 73 |<blink>Talent is|
00000010 20 70 75 72 73 75 65 64 20 69 6e 74 65 72 65 73 | pursued interes|
00000020 74 3c 2f 62 6c 69 6e 6b 3e 00 ff |t</blink>..| 
```

### 全十六进制转储程序

此时唯一剩下的事情是一些最终的格式化——如果少于 16 个字节，则在最后一行添加填充，并将字节分成两个 8 字节的块，这对于我来说解释起来并不重要。

这里是最终版本的要点，或者见下文。

```
const fs = require('fs')
const filename = process.argv.slice(2)[0]

function hexdump(filename) {
  let buffer = fs.readFileSync(filename)
  let lines = []

  for (let i = 0; i < buffer.length; i += 16) {
    let address = i.toString(16).padStart(8, '0') // address
    let block = buffer.slice(i, i + 16) // cut buffer into blocks of 16
    let hexArray = []
    let asciiArray = []
    let padding = ''

    for (let value of block) {
      hexArray.push(value.toString(16).padStart(2, '0'))
      asciiArray.push(value >= 0x20 && value < 0x7f ? String.fromCharCode(value) : '.')
    }

    // if block is less than 16 bytes, calculate remaining space
    if (hexArray.length < 16) {
      let space = 16 - hexArray.length
      padding = '  '.repeat(space * 2 + space + (hexArray.length < 9 ? 1 : 0)) // calculate extra space if 8 or less
    }

    let hexString =
      hexArray.length > 8
        ? hexArray.slice(0, 8).join('  ') + '  ' + hexArray.slice(8).join('  ')
        : hexArray.join('  ')

    let asciiString = asciiArray.join('')
    let line = `${address}  ${hexString}  ${padding}|${asciiString}|`

    lines.push(line)
  }

  return lines.join('\n')
}

console.log(hexdump(filename)) 
```

## 结论

我在这篇文章中涉及了很多概念。

*   位、半字节和字节
*   二进制、十进制和十六进制数字
*   在任何基础系统中计算数字的值
*   可打印的 ASCII 字符
*   访问 Node.js 中的文件数据
*   使用原始数据的缓冲区
*   将数字转换为十六进制和 ASCII

关于这个主题，我还想写更多的内容，比如创建 16 位十六进制转储、按位运算符和字节序，以及使用流来改进这个十六进制转储功能，所以可能会在后续文章中有更多内容。