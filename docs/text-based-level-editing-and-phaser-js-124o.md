# 基于文本的级别编辑和 Phaser JS

> 原文：<https://dev.to/mattconn/text-based-level-editing-and-phaser-js-124o>

我最初于 2017 年 4 月 7 日在我的网站上发表了这篇文章。

如果你熟悉 Nethack 这样的 roguelikes(回合制地牢爬虫)，那么你无疑熟悉游戏元素完全以 ASCII 字符显示的想法。

在这里，我将解释如何使用类似的方法来创建我正在开发的游戏中的房间布局，以及如何使用 Phaser (JS 游戏引擎)根据我的布局来渲染房间。

以下字符串数组表示一个房间:

```
 [
    //1         12             25
    'ppppppppppppppppppppppppp',//1
    'p------------------------',//2
    'p------------------------',//3
    'pppppppppp---------------',//4
    'p----------------E-------',//5
    'p--------------pppppppppp',//6
    'p------------------------',//7
    'p------E-E---------------',//8
    'pppppppppp---------------',//9
    'p------------------------',//10
    'p----------------E-E-E---',//11
    'p--------------pppppppppp',//12
    'p-----------------------p',//13
    'p-----------------------p',//14
    'p-----------ppppppppppppp',//15
    'p---------ppppppppppppppp',//16
    'p------pppppppppppppppppp',//17
    'ppppppppppppppppppppppppp',//18
    'ppppppppppppppppppppppppp' //19
    ] 
```

“E”代表敌人，“p”代表不可移动的块，可以作为平台或墙壁。破折号代表空白，仅仅是因为我发现它比空白更容易阅读，尽管我并不确切地计算破折号来寻找任何一种距离。

顶部和右侧的注释掉的数字分别表示列和行；我将一张 800 像素宽、600 像素高的画布分成 25×19、32 像素的正方形。

### 如何以及何时渲染一个房间

1.  玩家与世界的边缘(画布)发生碰撞

2.  然后将玩家的 x 位置更改为与碰撞相对的边缘的位置(右边缘碰撞，玩家移动到画布的最左侧；左边缘碰撞，玩家移动到画布的最右侧)；这给人一种进入新房间的感觉

3.  然后使用 Phaser 的`sprite.kill()`方法清空房间

4.  可变整数然后根据碰撞的边缘增加或减少 1(右，+1；left，-1)，然后这个整数被用作我们的级别数组中我们房间的索引。

    *   因此，如果我们当前房间的索引是 0(级别数组中的第一个房间)，并且玩家与画布的右边缘冲突，我们的整数增加到 1，并且我们渲染索引是 1 的房间(级别数组中的第二个房间)
5.  通过索引得到我们想要的房间后，我们检查房间数组内的每个字符串；这些字符串中的每一个都可以看作是一行可以在画布上呈现的东西，从画布的顶部到底部。

6.  对于房间数组中的每一行(字符串)，我们使用`String.charAt()`通过索引检查每个字符。

7.  使用 switch 语句，字符可以匹配大小写；如果匹配，我们将调用 Phaser 的`group.create()`方法来渲染匹配的 sprite 或图像。

因此，使用上面的房间布局，`level1[0][8].charAt(4)`将与我们的 switch 语句中的`p`匹配:
- 0 是房间的索引(这是唯一显示的房间，但通常会有多个房间)，8 是该行的索引，第 8 行的第 4 个字符是一个`p`。

### 房间渲染功能(删节)

(碰撞时调用。)

```
function renderRoom(room){
    for(var row in room){
        for(var column=0; column <=  room[row].length; column++){
            switch (room[row].charAt(column)){
                case 'p':
                    platform = platforms.create(column*32, row*32, 'platform-image');
                    break;
            }
        }
    }
} 
```

这个房间渲染功能目前只适用于一个级别，但应该可以适应多个级别，即使不完全相同，也可以类似地工作。

房间系统的工作方式如下面的伪代码所示，每个“列”实际上代表字符串中的一个字符:

```
level1 [
    room1 [ 
        row1 'column1 column2 column3',
        row2 'column1 column2 column3',
        row3 'column1 column2 column3'
    ],
    room2 [ 
        row1 'column1 column2 column3',
        row2 'column1 column2 column3',
        row3 'column1 column2 column3'
    ]
] 
```