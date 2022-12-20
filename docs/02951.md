# 彩色块状

> 原文：<https://dev.to/codeguppy/color-blocky-48jk>

# [T1】简介](#intro)

像素艺术很牛逼。本教程将向您展示如何容易建立一个简单的像素艺术编辑器。“Color Blocky”程序为 CodeGuppy 上也提供的更基本的像素艺术编辑器“Blocky”添加了颜色选择和其他功能。

孩子和怀旧的成年人都会从玩“彩色积木”中获得无穷的乐趣。

请随意使用更多工具进一步扩展该编辑器，然后与您的家人和朋友分享您的新程序。

# 需要什么？

要运行本文中的代码，您不需要在本地计算机上安装任何东西。

只需从[codeguppy.com/code.html](https://codeguppy.com/code.html)打开在线 JavaScript playground，在集成编辑器中复制并粘贴以下代码。

准备就绪后，按“播放”按钮运行代码。

[![](img/ff653eaaeb1b814d0d567822428d711e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DoNmnXOf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ujm0up06nzjd0kj1s9ny.png)

# 源代码

```
const noRows = 16;
const noCols = 16;
const squareSize = 25;

// pallete from http://eastfarthing.com/blog/2016-05-06-palette/
var pallete = ["#ffffff", "#3f32ae", "#e30ec2", "#baaaff", "#ff949d", "#e80200", "#7a243d", "#000000", "#195648", 
              "#6a8927", "#16ed75", "#32c1c3", "#057fc1", "#6e4e23", "#c98f4c", "#efe305"];

var map;

var buttons = [];
var foreColor = 1;
var backColor = 0;

var editorX = 50;
var editorY = 0;

var showNumbers = false;

function initMap()
{
    var map = new Array(noRows);
    for(var i = 0; i < noRows; i++)
    {
        var row = new Array(noCols);
        for(var j = 0; j < noCols; j++)
        {
            row[j] = 0;
        }
        map[i] = row;
    }

    return map;
}

function enter()
{
    addButtons();
    map = initMap();
}

function loop()
{
    clear();

    drawButtons();
    displayMap();
    displayInstructions();

    handleButtons();
    handleCellClick();
}

function keyPressed()
{
    showNumbers = (key.toUpperCase() === "Z") & !showNumbers;
}

function handleCellClick()
{
    if (!mouseIsPressed)
        return;

    var col = floor( (mouseX - editorX) / squareSize );
    var row = floor( (mouseY - editorY) / squareSize );

    if (col < 0 || col >= noCols 
        || row < 0 || row >= noRows)
        return;

    var color = mouseButton == LEFT ? foreColor : backColor;
    map[row][col] = color;
}

function displayMap()
{
    for(var row = 0; row < noRows; row++)
    {
        for(var col = 0; col < noCols; col++)
        {
            var cellX = editorX + col * squareSize;
            var cellY = editorY + row * squareSize;
            var color = map[row][col];

            fill(pallete[color]);
            stroke(0);
            rect(cellX, cellY, squareSize, squareSize);

            if (showNumbers)
            {
                textAlign(CENTER, CENTER);
                fill(0);
                noStroke();

                text(color, cellX + squareSize / 2, cellY + squareSize / 2);
            }
        }
    }
}

function displayInstructions()
{
    noStroke();
    fill(0);
    textAlign(LEFT, LEFT);

    text("Use LEFT / RIGHT mouse buttons to draw. Press Z to toggle numbers.", editorX, height - 10);
}

function addButtons()
{
    var w = floor(height / pallete.length);

    for(i = 0; i < pallete.length; i++)
    {
        var color = pallete[i];
        addButton(1, i * w, w, w, color);
    }
}

function addButton(x, y, w, h, color)
{
    var btn = { x : x, 
                y : y, 
                w : w,
                h : h,
                color : color
                };

    buttons.push(btn);

    return btn;
}

function handleButtons()
{
    if (!mouseIsPressed)
        return;

    for(var i = 0; i < buttons.length; i++)
    {
        var btn = buttons[i];

        if (collisionPointRect(mouseX, mouseY, btn.x, btn.y, btn.w, btn.h))
        {
            if (mouseButton == LEFT)
                foreColor = i;
            else
                backColor = i;
        }
    }
}

function drawButtons()
{
    for(var btn of buttons)
    {
        drawButton(btn);
    }
}

function drawButton(btn)
{
    stroke(0);
    strokeWeight(1);

    fill(btn.color);
    rect(btn.x, btn.y, btn.w, btn.h);

    writeLabel(btn);
}

function writeLabel(btn)
{
    var t = "";

    if (btn.color == pallete[foreColor] )
    {
        t = "FG";
    }
    if (btn.color == pallete[backColor] )
    {
        t = t == "" ? "BG" : "F/BG";
    }

    fill("white");
    stroke("black")

    textAlign(CENTER, CENTER);
    text(t, btn.x + btn.w / 2, btn.y + btn.h / 2)
} 
```

# 反馈

如果你喜欢这篇文章，请在 Twitter 上关注 [@codeguppy](https://twitter.com/codeguppy) 和/或访问 codeguppy.com[获得更多教程和项目。](https://codeguppy.com)

另外，如果你想用详细的说明来扩展这篇文章，解释如何一步一步地构建程序，请在评论中留下反馈。