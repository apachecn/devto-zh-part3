# 通过绘制 SSH 密钥的标识来提高安全性

> 原文：<https://dev.to/krofdrakula/improving-security-by-drawing-identicons-for-ssh-keys-24mc>

如果您曾经不得不生成一个加密密钥对，或者使用配置了可视主机密钥的 SSH 客户端登录到一台机器上，那么您可能会偶然发现一些类似这样的随机 ASCII 艺术官样文章:

```
The key fingerprint is:
28:b5:b9:9b:15:0d:ac:04:d8:fc:18:fd:af:1b:65:fd you@somewhere.com
+-----------------+
|   +..           |
|  . +...         |
|     +o.o        |
|    .o.=.o .     |
|    . = S.+ .    |
|     . . +.  .   |
|      . o.    E  |
|       +..       |
|      o ..       |
+-----------------+ 
```

Enter fullscreen mode Exit fullscreen mode

ASCII 艺术是主机密钥的 16 字节(128 位)指纹，表示为程序生成的图像。一个[标识](https://blog.docuverse.com/2011/07/29/what-is-identicon/)，如果你愿意的话。它是在 [OpenSSH 5.1](https://lists.mindrot.org/pipermail/openssh-unix-dev/2008-July/026693.html) 中引入的，作为一种帮助人类快速可靠地识别随机字符串的方法。如果你错误地用不同的主机密钥连接到一台机器，你更有可能认出(或者说，认不出)密钥的图像并意识到你的错误。

哦，如果你很好奇，你可以在你的`~/.ssh/config`文件中添加`VisualHostKey yes`来在你的 shell 中连接到其他主机时启用它。

## 吸取教士和钱袋中的硬币

在我们深入研究绘制这种 ASCII 艺术的算法之前，让我们围坐一圈，听我讲一个关于*喝醉的主教*的故事。

> 彼得主教发现自己在一个环境中庭的中央。四面都有墙，显然没有出口。地面铺的是方砖，严格的黑白相间。他的头很痛——可能是因为之前喝了太多的酒——他开始四处乱逛。嗯，确切地说，他只走对角线——就像棋盘上的主教一样。当他撞到墙时，他移动到一边，这将他从黑瓷砖带到白瓷砖(反之亦然)。每走一步，他都在地板上放一枚硬币，以此来记住他曾经到过那里。走了 64 步，就在没有硬币剩下的时候，彼得突然醒了。多么奇怪的梦啊！
> 
> *来源:* [《醉酒的主教:OpenSSH 指纹可视化算法分析](http://www.dirk-loss.de/sshvis/drunken_bishop.pdf)，D. Loss 等。

这个有趣的故事讲完了，让我们分析一下它和我们的小项目有什么关系。当彼得在房间里随意走动时，他会在他参观过的瓷砖上留下硬币。移动 64 次后，一些牌将没有硬币，而一些牌上将有一个或多个硬币。如果我们将网格表示为每块瓷砖中硬币数量的 2D 图，我们就得到了 SSH 虚拟主机密钥！

## 网格

我们从确定房间的大小开始。根据算法，房间大小是一个 17 块宽 9 块长的矩形。

```
const WIDTH = 17;
const HEIGHT = 9; 
```

Enter fullscreen mode Exit fullscreen mode

我们将原点定义在左上角，按列(`x`)和行(`y`)对瓦片进行编号，从`0` :
开始

```
 1111111
  01234567890123456   
 +-----------------+ x
0|                 |
1|                 |
2|                 |
3|                 |
4|        S        |
5|                 |
6|                 |
7|                 |
8|                 |
 +-----------------+
 y 
```

Enter fullscreen mode Exit fullscreen mode

我们用`S = [8, 4]`标记起始位置。

我们将硬币计数的网格表示为一维数组，从左到右、从上到下排列数值。这样，如果我们想要查找特定位置的值，我们可以使用`x`和`y`来计算索引:

```
const world = Array(WIDTH * HEIGHT).fill(0);
const coins = world[y * WIDTH + x]; 
```

Enter fullscreen mode Exit fullscreen mode

## 游戏规则

由于我们总是希望给定相同的指纹，为我们的主教生成相同的行走模式，所以我们首先必须决定如何将指纹转换为供主教移动的命令列表。我们首先定义主教可以采取的四种可能的行动:

```
const MOVES = [
  { x: -1, y: -1 }, // ↖
  { x: 1, y: -1 },  // ↗
  { x: -1, y: 1 },  // ↙
  { x: 1, y: 1 }    // ↘
]; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经定义了与整数`0`、`1`、`2`和`3`相关的四个命令。如果我们创建一个这些数字的列表，我们可以按顺序发出这些命令来移动主教。要做到这一点，我们需要把指纹分成成对的比特。

让我们从一个字节开始。一个字节由 8 位组成:

```
a9 = 10 10 10 01 => [01, 10, 10, 10]
     ^  ^  ^  ^      ^   ^   ^   ^
#    4  3  2  1      1   2   3   4 
```

Enter fullscreen mode Exit fullscreen mode

出于该算法的目的，我们将成对的位转换成一个整数数组，从最低到最高有效(在图中用#编号)。为此，我们使用了一点位数学。

如果你对我为什么选择`3`作为掩码不熟悉的话:二进制形式的`3 === 0b11`。

```
const splitByteIntoCommand = byte => ([
  byte & 3,           //  11   11   11  [11]
  (byte >>> 2) & 3,   //  11   11  [11]  11
  (byte >>> 4) & 3,   //  11  [11]  11   11
  (byte >>> 6) & 3    // [11]  11   11   11
]); 
```

Enter fullscreen mode Exit fullscreen mode

一个字节由两个十六进制字符表示，因此为了从给定的主机密钥生成命令列表，我们需要将字符串分成对，以将它们转换成一个字节:

```
const parseCommands = hexString => {
  const commands = [];

  // loop over all the characters in the hex string in steps of 2
  for (let i = 0; i < hexString.length; i += 2) {

    // take a pair of hex characters
    const value = parseInt(hexString.slice(i, i + 2), 16);

    // split the byte into 4 commands and append them to the list
    commands.push(...splitByteIntoCommand(value));
  }

  return commands;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有了一个函数，它可以将主机密钥指纹作为 32 个字符的十六进制字符串，并将其转换为一个命令数组。

## 使事物移动

我们的主教现在有了一个可以进入的世界和一系列我们希望他执行的命令。让我们创建一个函数，它将接受世界的状态、主教的位置和一个命令来计算下一个状态。

```
// ensures the returned value is always min <= x <= max
const clamp = (min, max, x) =>
  Math.max(min, Math.min(max, x));

const nextPosition = (position, move) => {
  // look up direction to move in the rules lookup
  const delta = MOVES[move];

  // return a new position while ensuring the bishop doesn't stray
  // outside of the room
  return {
    x: clamp(0, WIDTH - 1, position.x + delta.x),
    y: clamp(0, HEIGHT - 1, position.y + delta.y)
  };
};

const step = (world, position, command) => {

  // create a copy of the world state
  const newWorld = Array.from(world);

  // drop a coin in the current position
  newWorld[position.y * WIDTH + position.x] += 1;

  // return the new world state and the next position
  return [newWorld, nextPosition(position, command)];
} 
```

Enter fullscreen mode Exit fullscreen mode

为了遍历命令列表，我们将创建另一个函数来遍历命令，从一个空房间开始。这个函数将在给定的步数后返回世界的状态。

> 注意:开始和结束位置被赋值为 15 和 16，因为我们希望能够看到主教开始和结束步行的位置。

```
const simulate = (commands, steps = commands.length) => {

  // start in the middle of the grid
  const start = { x: 8, y: 4 };

  // set the inital position to the starting position
  let position = start;

  // make the initial world empty
  let world = Array(WIDTH * HEIGHT).fill(0);

  // loop over the requested number of steps
  for (let i = 0; i < steps; i++)

    // calculate the next world state and position
    [world, position] = step(world, position, commands[i]);

  // remember the last position calculated
  const end = position;

  // set the starting position to 15
  world[start.y * WIDTH + start.x] = 15;

  // set the ending position to 16
  world[end.y * WIDTH + end.x] = 16;

  return world;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 绘制网格

到目前为止，我们只有一个平面数组，显示了每个瓷砖中硬币的数量，但我们仍然需要绘制直方图。该算法规定了代表一张牌中硬币可能值的字符:

```
0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15 16
   .  o  +  =  *  B  O  X  @  %  &  #  /  ^  S  E 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将表格编码为一个单独的字符串:

```
const SYMBOLS = ' .o+=*BOX@%&#/^SE'; 
```

Enter fullscreen mode Exit fullscreen mode

要查找特定数量硬币的符号，我们可以只使用字符串的索引来给出用于该计数的符号(用于`4`硬币的符号是`SYMBOLS[4]`)。

为了绘制这个世界，我们将把整数映射到上面字符串中的字符，然后通过把字符串分割成等长的`WIDTH`来绘制网格。

```
const draw = (world, width, height, status = '') => {
  // turn coin counts into histogram symbols
  const drawing = world
    .map(cell => SYMBOLS[cell % SYMBOLS.length])
    .join('');

  // draw the top border
  const result = ['+' + '-'.repeat(width) + '+'];

  // loop through each row
  for (let i = 0; i < height; i++)
    result.push('|' + drawing.slice(i * width, (i + 1) * width) + '|');

  // draw the bottom border
  result.push('+' + '-'.repeat(width) + '+');

  // return the lines, joined with a newline
  return result.join('\n'); 
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 变得有趣

展示最终结果是很棒的，但是看到主教在房间里跑的时候跌跌撞撞会很有趣。幸运的是，模拟让我们指定希望主教执行的步数，因此我们可以在每个动画帧上绘制每一步的世界状态:

```
const displayDiv = document.getElementById('display');

const run = (commands, steps = 0) => {
  const world = simulate(commands, steps);
  displayDiv.textContent = draw(world, WIDTH, HEIGHT)
    + `\n${steps} steps`;
  if (steps < commands.length)
    requestAnimationFrame(() => run(commands, steps + 1));
}; 
```

Enter fullscreen mode Exit fullscreen mode

把它们放在一起，我们就有一个有趣的玩具可以玩了！

[https://codepen.io/KrofDrakula/embed/EGZBMy?height=600&default-tab=result&embed-version=2](https://codepen.io/KrofDrakula/embed/EGZBMy?height=600&default-tab=result&embed-version=2)