# 鼠标连接

> 原文：<https://dev.to/codeguppy/mouse-connections-4aon>

*鼠标连接*是这个程序的正确名称吗？…或者也许应该命名为*蜘蛛腿*？

该程序通过显示一系列从画布边缘反弹的球来工作。从鼠标光标到光标特定范围内的每个球的连接是动态创建的。

[https://www.youtube.com/embed/qtMG1XbadLY](https://www.youtube.com/embed/qtMG1XbadLY)

# 源代码

这个很酷的效果其实很容易实现。我们只需要使用 p5.js 这样的 2D 图形库在画布上工作

在这种情况下，我们将使用 p5.js 附带的[codeguppy.com](https://codeguppy.com)环境。

尝试在[代码编辑器](https://codeguppy.com/code.html)
中复制以下源代码

```
var noBalls = 100;
var speed = 1;
var distBalls = 100;

var balls = [];

function enter()
{
    createBalls();    
}

function loop()
{
    clear();

    updateBalls();
    displayBalls();
    displayConnections(mouseX, mouseY);

    displayInstructions();
}

function createBalls()
{
    for(var i = 0; i < noBalls; i++)
    {
        var ball = {
            x : random(width), 
            y : random(height), 
            dx : random([-1, 1]), 
            dy : random([-1, 1]),
            speed : speed
        };

        balls.push(ball);
    }
}

function updateBalls()
{
    for(var i = 0; i < balls.length; i++)
    {
        var ball = balls[i];

        ball.x += ball.dx * ball.speed;
        ball.y += ball.dy * ball.speed;

        if (ball.x > width || ball.x < 0)
            ball.dx *= -1;

        if (ball.y > height || ball.y < 0)
            ball.dy *= -1;
    }
}

function displayBalls()
{
    for(var i = 0; i < balls.length; i++)
    {
        var ball = balls[i];

        fill(255);
        stroke(150);
        circle(ball.x, ball.y, 3);
    }
}

function displayConnections(fromX, fromY)
{
    for(var i = 0; i < balls.length; i++)
    {
        var ball = balls[i];

        var d = dist(fromX, fromY, ball.x, ball.y);
        if (d < distBalls)
        {
            var color = map(d, 0, distBalls, 0, 150);
            stroke(color);
            fill(color);

            circle(fromX, fromY, 3);
            circle(ball.x, ball.y, 3);
            line(fromX, fromY, ball.x, ball.y);
        }
    }
}

function displayInstructions()
{
    push();
    fill(0);
    noStroke();
    text("Move your mouse inside the canvas area", 10, 590);
    pop();
} 
```

Enter fullscreen mode Exit fullscreen mode

# 草稿

这篇文章是草稿。如果你对上面的程序如何工作感兴趣，请留下评论。