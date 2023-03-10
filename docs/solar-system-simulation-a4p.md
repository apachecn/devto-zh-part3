# 太阳系模拟

> 原文：<https://dev.to/codeguppy/solar-system-simulation-a4p>

# [T1】简介](#intro)

了解如何创建一个模拟我们的太阳系与现实的行星运动。这个教育应用程序不仅教你科学概念，还教你重要的编码概念，例如:

*   如何使用对象存储复杂的数据结构
*   如何使用三角函数
*   如何使用循环事件创建动画

# 需要什么？

要运行本文中的代码，您不需要在本地计算机上安装任何东西。

只需从[codeguppy.com/code.html](https://codeguppy.com/code.html)打开在线 JavaScript playground，在集成编辑器中复制并粘贴以下代码。

准备就绪后，按“播放”按钮运行代码。

[![](img/3d4b56e16b91998941111113c155f860.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ydr6Ag4y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8n3bshdnikfqluut6une.png)

# 源代码

```
var sunRadius = 30;
var systemAge = 0;
var dpf = 1; // days per frame

var planets = [
        { name : 'Mercury', r : 6, rev : 87.97 },
        { name : 'Venus', r : 7, rev : 224.7 },
        { name : 'Earth', r : 10, rev : 365.26, color: 'blue' },
        { name : 'Mars', r : 8, rev : 1.88 * 365 },
        { name : 'Jupiter', r : 20, rev : 11.86 * 365 },
        { name : 'Saturn', r : 15, rev : 29.46 * 365 },
        { name : 'Uranus', r : 12, rev : 84.01 * 365 },
        { name : 'Neptune', r : 7, rev : 164.79 * 365 },
        { name : 'Pluto', r : 5, rev : 248.59 * 365 }
    ];

function enter()
{
    background('Stars');
    calculateDistanceFromSun();
}

function loop()
{
    clear();

    updateAge();
    updatePlanets();
    drawPlanets();

    displayStats();
}

function drawPlanets()
{
    var sunX = width / 2;
    var sunY = height / 2;

    fill('yellow');
    circle(sunX, sunY, sunRadius);

    for(var i = 0; i < planets.length; i++)
    {
        var p = planets[i];

        // Draw orbit
        noFill();
        stroke(200);
        circle(sunX, sunY, p.sunDist);

        var x = sunX + p.sunDist * cos(p.a);
        var y = sunY - p.sunDist * sin(p.a);

        // Draw planet
        stroke(255);
        fill(p.color || "white");
        circle(x, y, p.r);

        push();
        noStroke();
        fill(255);
        textAlign(LEFT, CENTER);
        text(p.name, x + p.r + 5, y);
        pop();
    }
}

function updateAge()
{
    systemAge += dpf;
}

function updatePlanets()
{
    for (var o of planets)
    {
        o.a = (systemAge / o.rev) * 360;
    }
}

function calculateDistanceFromSun()
{
    function getPlanetSize()
    {
        var d = 0;
        for(var o of planets)
        {
            d += o.r * 2;
        }
        return d;
    }

    function getInterPlanet()
    {
        var planetsSize = getPlanetSize();
        var dp = height / 2 - sunRadius - planetsSize - 10;
        dp  = dp / planets.length;

        return dp;
    }

    var dp = getInterPlanet();
    planets[0].sunDist = sunRadius + dp + planets[0].r;

    for(var i = 1; i < planets.length; i++)
    {
        var p = planets[i-1];
        planets[i].sunDist = p.sunDist + p.r + dp + planets[i].r;
    }
}

function displayStats()
{
    push();
    fill(255);
    noStroke();

    text("System age: " + systemAge + " days", 10, 10);

    for(var i = 0; i < planets.length; i++)
    {
        var p = planets[i];
        var pYears = systemAge / p.rev;
        text(p.name + ": " + pYears.toFixed(2) + " years", 10, (i+2)*14);
    }

    pop();
} 
```

# 反馈

如果你喜欢这篇文章，请在 Twitter 上关注 [@codeguppy](https://twitter.com/codeguppy) 和/或访问 codeguppy.com[获得更多教程和项目。](https://codeguppy.com)

另外，如果你想用详细的说明来扩展这篇文章，解释如何一步一步地构建程序，请在评论中留下反馈。