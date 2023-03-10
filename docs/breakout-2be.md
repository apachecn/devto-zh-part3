# 越狱

> 原文：<https://dev.to/codeguppy/breakout-2be>

# [T1】简介](#intro)

突围是一款经典的街机视频游戏。

最初的突破游戏是由雅达利在 1976 年开发并发布的。这款游戏一炮而红，激发了各代电脑上无数的克隆体。

在本文中，您将学习如何重新创建这个游戏，为您和您的朋友提供数小时的娱乐。

# 需要什么？

要运行本文中的代码，您不需要在本地计算机上安装任何东西。

只需从[codeguppy.com/code.html](https://codeguppy.com/code.html)打开在线 JavaScript playground，在集成编辑器中复制并粘贴以下代码。

准备就绪后，按“播放”按钮运行代码。

[![](img/b015416e8b6fa042a766814cddbfb64c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OM08_ges--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jrfuf4aez5etwnt0jitf.png)

# 源代码

```
var paddleWidth = 60;
var brickWidth = 50;
var brickHeight = 20;
var brickSpace = 20;
var rowSpace = 10;

var xPaddle;
var yPaddle;

var ball;
var ballsLeft;

var bricks = [];

background("Field");
fill("white");

function enter()
{
    initGame();
}

function initGame()
{
    bricks = createBricks();
    ballsLeft = 3;

    initBall();
}

function initBall()
{
    xPaddle = width / 2;
    yPaddle = height - 20;

    ball = {
        radius : 5,
        x : 0,
        y : 0,
        xvel : 5,
        yvel : -5,
        inMotion : false
    };
}

function loop()
{
    clear();

    readKeys();
    displayBricks();
    displayPaddle();

    updateBall();
    displayBall();

    checkForCollision();

    displayStats();
}

function displayStats()
{
    push();
    fill('black');
    noStroke();

    text("Balls: " + ballsLeft, 10, height - 20);
    text("Bricks: " + bricks.length, 10, height - 6);

    pop();
}

function updateBall()
{
    if ( !ball.inMotion )
    {
        ball.x = xPaddle + paddleWidth / 2;
        ball.y = yPaddle - ball.radius;
    }
    else
    {
        updateBallInMotion();
    }
}

function updateBallInMotion()
{
    ball.x += ball.xvel;
    ball.y += ball.yvel;

    if ( ball.x < 0 || ball.x > width )
    {
        ball.xvel *= -1;
    }
    else if ( ball.y < 0 )
    {
        ball.yvel *= -1;
    }
    else if ( collisionCircleRect(ball.x, ball.y, ball.radius, xPaddle, yPaddle, paddleWidth, 10) )
    {
        ball.yvel *= -1;
    }
    else if ( ball.y > height )
    {
        ballsLeft--;

        if (ballsLeft >= 0)
        {
            initBall();
        }
        else
        {
            initGame();
        }
    }
}

function checkForCollision()
{
    var brickIndex = getHitBrick();
    if ( brickIndex == -1 )
    {
        return;
    }

    bricks.splice(brickIndex, 1);
    ball.yvel *= -1;

    if ( bricks.length == 0 )
    {
        initGame();
    }
}

// Iterate through all the bricks and check
// if the ball hits any bricks.
// Returns the index of the brick that is hit ... or -1
function getHitBrick()
{
    for(var i = 0; i < bricks.length; i++)
    {
        var brick = bricks[i];

        if ( collisionCircleRect( ball.x, ball.y, ball.radius, brick.x, brick.y, brickWidth, brickHeight ) )
        {
            return i;
        }
    }

    return -1;
}

function displayBall()
{
    ellipse( ball.x, ball.y, ball.radius * 2 );
}

function readKeys()
{
    if ( keyIsDown( LEFT_ARROW ) && xPaddle > 0  )
    {
        xPaddle -= 5;
    }
    else if ( keyIsDown( RIGHT_ARROW) && xPaddle < width - paddleWidth )
    {
        xPaddle += 5;
    }
    else if ( keyIsDown (32) )  // SPACE
    {
        ball.inMotion = true;
    }
}

function createBricks()
{
    var noBricks = Math.floor((width - brickSpace) / ( brickWidth + brickSpace ));
    var arBricks = [];

    for(var row = 0; row < 3; row++)
    {    
        for(var col = 0; col < noBricks; col++ )
        {
            var x = col * ( brickWidth + brickSpace ) + brickSpace;
            var y = row * (brickHeight + rowSpace) + rowSpace;

            var brick = { x : x, y : y };
            arBricks.push(brick);
        }
    }

    return arBricks;
}

function displayBricks()
{
    for(var i = 0; i < bricks.length; i++)
    {
        var brick = bricks[i];
        rect( brick.x, brick.y, brickWidth, brickHeight );
    }
}

function displayPaddle()
{
    rect( xPaddle, yPaddle, paddleWidth, 10 );
} 
```

# 反馈

如果你喜欢这篇文章，请在 Twitter 上关注 [@codeguppy](https://twitter.com/codeguppy) 和/或访问 codeguppy.com[获得更多教程和项目。](https://codeguppy.com)

另外，如果你想用详细的说明来扩展这篇文章，解释如何一步一步地构建程序，请在评论中留下反馈。