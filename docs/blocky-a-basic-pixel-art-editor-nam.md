# 块状:一个基本的像素艺术编辑器

> 原文：<https://dev.to/codeguppy/blocky-a-basic-pixel-art-editor-nam>

# 简介

孩子们好！你有心情去上美术课吗？

我们希望你是...因为在本教程中，我们将学习如何创建“块状”——这是一个非常简单的图像编辑器，允许你一个像素一个像素地创建你自己的字符。

不需要放大镜-只需要你的鼠标！

随意个性化和扩展像素编辑器。您可以添加颜色选择和各种绘图工具等功能。

此外，不要忘记检查[色块](https://codeguppy.com/code.html?color_blocky)——这个编辑器的更高级版本，有颜色选择。

# 需要什么？

要运行本文中的代码，您不需要在本地计算机上安装任何东西。

只需从[codeguppy.com/code.html](https://codeguppy.com/code.html)打开在线 JavaScript playground，按照指示复制并粘贴代码。

准备就绪后，按“播放”按钮运行代码。

# 建立网格纸尺寸

我们程序的第一步是画网格纸。

因此，我们需要决定画布上需要多少行和多少列……当然还有每个单元格/方块的大小。

让我们定义几个常数来保存这些信息。

```
const squareSize = 16;
const noRows = 30;
const noCols = 50; 
```

这些常量就像我们程序的设置。我们可以稍后回来修改它们，整个程序将反映新的值。

# 绘制网格纸

要绘制网格纸，我们可以使用以下方法之一:

*   我们可以在定义网格纸的图案中绘制水平线和垂直线，或者
*   我们可以绘制按行和列排列的小矩形来定义网格纸的单元格

在本教程中，我们将选择第二种方法。

唯一的挑战是计算出每个网格纸单元的坐标。在这些坐标上，我们必须显示我们的矩形。因为所有单元格的大小都相同，所以每个单元格的左上角坐标可以通过将当前列/行乘以单元格大小来获得。

我们的代码如下所示...准备接受测试！复制 [CodeGuppy 编辑器](https://codeguppy.com/code.html)中的代码并运行！

```
const squareSize = 16;
const noRows = 30;
const noCols = 50;

for(var row = 0; row < noRows; row++)
{
    for(var col = 0; col < noCols; col++)
    {
        var x = col * squareSize;
        var y = row * squareSize;

        rect(x, y, squareSize, squareSize);
    }
} 
```

> 注意:代码假设网格纸从画布的(0，0)坐标开始。如果您想要将整个网格纸区域放置在不同的位置，您将需要相应地以适当的量移动 x 和 y 坐标。

# 重构代码

让我们稍微重构一下代码，并提取所有这些逻辑，在一个单独的函数中绘制网格纸...马上打电话给我。

效果应该和以前一样...唯一的区别是现在代码封装得更好了。尽管我们可以对函数进行参数化，但现在我们让它读取全局常量。

```
const squareSize = 16;
const noRows = 30;
const noCols = 50;

displayGridPaper();

function displayGridPaper()
{
    for(var row = 0; row < noRows; row++)
    {
        for(var col = 0; col < noCols; col++)
        {
            var x = col * squareSize;
            var y = row * squareSize;

            rect(x, y, squareSize, squareSize);
        }
    }
} 
```

# 添加块

有了网格纸，让我们定义 mousePressed()函数。如果定义了这个函数，CodeGuppy 引擎会在你每次点击鼠标的时候自动调用它。

为了验证 mousePressed()是否被正确调用，让这个函数在鼠标坐标处画一个黑色的小矩形。

> 记住:mouseX 和 mouseY 是包含鼠标光标位置的系统变量。每次鼠标光标移动时，CodeGuppy 运行时都会自动更新这些变量。

假设您仍然打开了 [CodeGuppy 编辑器](https://codeguppy.com/code.html)，在代码中添加以下函数并再次运行代码。

```
function mousePressed()
{
    fill('Black');
    rect(mouseX, mouseY, squareSize, squareSize);
} 
```

你运行代码了吗？该计划已经开始寻找我们想要的！

# 找到击中的方块

我们现在面临的挑战是让`mousePressed()`函数在下方网格纸单元格的精确坐标处画出黑色矩形——不管我们用鼠标点击单元格内的哪个位置。

我们计划这样做:

*   找到网格纸单元格的行/列索引。这可以很容易地用我们在画网格纸时使用的逆运算来确定:我们将使用除法。

```
var col = Math.floor( mouseX / squareSize );
var row = Math.floor( mouseY / squareSize ); 
```

注意:`Math.floor` JavaScript 函数用于将除法的结果向上舍入到最接近的较低整数。

*   一旦我们有了行/列索引，我们就可以重新计算该单元格的(x，y)像素坐标。这些是我们用来画黑色矩形的相同坐标。

```
var x = col * squareSize;
var y = row * squareSize; 
```

就是这样！我们的节目结束了！应该是这样的:

```
const squareSize = 16;
const noRows = 30;
const noCols = 50;

displayGridPaper();

function mousePressed()
{
    var col = Math.floor( mouseX / squareSize );
    var row = Math.floor( mouseY / squareSize );

    var x = col * squareSize;
    var y = row * squareSize;

    fill('Black');
    rect(x, y, squareSize, squareSize);
}

function displayGridPaper()
{
    for(var row = 0; row < noRows; row++)
    {
        for(var col = 0; col < noCols; col++)
        {
            var x = col * squareSize;
            var y = row * squareSize;

            rect(x, y, squareSize, squareSize);
        }
    }
} 
```

如果你愿意，仔细检查我们添加的代码行，并将其复制到打开的 [CodeGuppy 编辑器](https://codeguppy.com/code.html)中。如果您遇到错误，只需用上面的代码替换编辑器中的整个代码。
运行它，看看它的表现如何！

# 避开点击

这个程序做得很好...但是持续的鼠标点击有点讨厌。

我们可以通过将`mousePressed`函数中的所有代码移到`loop`函数中来避免鼠标点击。

> 记住:每次点击鼠标按钮时，运行时都会调用`mouseClicked`。`loop`函数也会被运行时每秒自动调用很多次。

现在运行程序，观察行为差异！

# 添加回点击

`loop`解决方案解决了过多点击的问题...但是现在程序只是不断地绘制，没有选项来指定什么时候绘制，什么时候不绘制。

检查`loop`函数中鼠标按钮的状态并仅在鼠标被按下时运行循环代码将会很好。CodeGuppy 环境将系统变量`mouseIsPressed`交由我们处理。像`mouseX`和`mouseY`变量一样，这也是由运行时自动更新的。

使用右边的代码编辑器，尝试将`loop`函数代码包装在一个`if`条件中:

```
if (mouseIsPressed)
{
    ...
} 
```

编辑完代码后，按“播放”按钮，看看你的修改是否有效。

如果您正确添加了`if`，代码现在应该如预期的那样运行了！

> 有了`loop`函数中的`if (mouseIsPressed)`条件，我们的代码现在只有在按下鼠标按钮时才会绘制。

# 代码重构

是时候进行一次小的代码重构了。我们建议您将此作为练习:

*   在单独的函数`displayCell(x, y)`中提取与单元格着色相关的代码
*   将鼠标位置的读数移出`displayCell`功能。
*   在`displayCell`函数中添加一个保护，以防用户试图给网格纸外的单元格着色。

# 删除单元格

希望您的代码能够工作！无论如何，以下是截至目前的计划，并添加了一些评论作为奖励。

```
const squareSize = 16;
const noRows = 30;
const noCols = 50;

displayGridPaper();

function loop()
{
    // Call to displayCell at mouse coordinates, only if mouse is pressed
    if (mouseIsPressed)
    {
        displayCell(mouseX, mouseY);
    }
}

// Display the cell that contains the point (x, y)
function displayCell(x, y)
{
    var col = Math.floor( x / squareSize );
    var row = Math.floor( y / squareSize );

    if (col >= noCols || row >= noRows)
        return;

    // determine the cell left upper-corner x and y coordinates
    var cellX = col * squareSize;
    var cellY = row * squareSize;

    fill('Black');
    rect(cellX, cellY, squareSize, squareSize);
}

// Display the grid paper using a series of white rectangles
function displayGridPaper()
{
    for(var row = 0; row < noRows; row++)
    {
        for(var col = 0; col < noCols; col++)
        {
            var x = col * squareSize;
            var y = row * squareSize;

            rect(x, y, squareSize, squareSize);
        }
    }
} 
```

我们只需要多一个特性:

> 如果按下鼠标右键，允许操作员删除单元格

如果按下鼠标左键，我们将选择填充颜色为`Black`，如果按下鼠标右键，我们将选择填充颜色为`White`。`White`颜色将模拟删除效果。

让我们继续扩展`displayCell`函数，再接受一个参数:颜色。

在函数内部，我们将使用这个参数，而不是之前使用的`Black`常量。

```
function displayCell(x, y, color)
{
    ...
    fill(color);
    rect(cellX, cellY, squareSize, squareSize);
} 
```

...当然，我们还需要改变使用`displayCell`的地方。在我们的例子中是`loop`函数。
在这个函数中，我们还将检查哪个鼠标按钮被按下(系统`mouseButton`变量给我们这个信息)并据此决定颜色。

```
function loop()
{
    if (mouseIsPressed)
    {
        var color = mouseButton == LEFT ? "Black" : "White";
        displayCell(mouseX, mouseY, color);
    }
} 
```

你是否接受挑战，在右边的代码中自己做这些修改？如果是，那么继续输入代码。

# 结论

我们的块状程序现在完成了。

希望您的代码能够工作！如果没有，请尝试通过比较 [CodeGuppy 编辑器](https://codeguppy.com/code.html)中的代码和下面的代码来调试它。

```
const squareSize = 16;
const noRows = 30;
const noCols = 50;

displayGridPaper();

function loop()
{
    // Call to displayCell at mouse coordinates, only if mouse is pressed
    if (mouseIsPressed)
    {
        var color = mouseButton == LEFT ? "Black" : "White";
        displayCell(mouseX, mouseY, color);
    }
}

// Display the cell that contains the point (x, y)
function displayCell(x, y, color)
{
    var col = floor( x / squareSize );
    var row = floor( y / squareSize );

    if (col >= noCols || row >= noRows)
        return;

    // determine the cell left upper-corner x and y coordinates
    var cellX = col * squareSize;
    var cellY = row * squareSize;

    fill(color);
    rect(cellX, cellY, squareSize, squareSize);
}

// Display the grid paper using a series of white rectangles
function displayGridPaper()
{
    for(var row = 0; row < noRows; row++)
    {
        for(var col = 0; col < noCols; col++)
        {
            var x = col * squareSize;
            var y = row * squareSize;

            rect(x, y, squareSize, squareSize);
        }
    }
} 
```

注意:如果您在调试过程中遇到困难...只需用以上版本替换 [CodeGuppy 编辑器](https://codeguppy.com/code.html)中的整个程序。

[![](img/5bb6db18b95a4f240404bfba70e30d54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TwO3Wi21--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3zje9yqt4ovg9om3hfpu.png)

我们把代码留在这个阶段给你。如果你喜欢复古人物绘图，你可以进一步扩展*块状*到你的梦想复古精灵编辑器！

编码快乐！

# 反馈

如果你喜欢这篇文章，请在 Twitter 上关注 [@codeguppy](https://twitter.com/codeguppy) 和/或访问 codeguppy.com[获得更多教程和项目。](https://codeguppy.com)