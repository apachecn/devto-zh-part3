# 破坏它拉尔夫 2 病毒弹出

> 原文：<https://dev.to/annlin/wreck-it-ralph-2-virus-popup-58gm>

## 我刚刚看了拉尔夫断网😍

我非常喜欢拉尔夫在屏幕上到处蹦蹦跳跳的场景。

## CSS 中的拉尔夫

因为已经很晚了，我们需要 Ralph 在 CSS 中，所以我用 box-shadow 偷了 Jason Delia 的令人惊叹的 CSS 像素艺术。我们需要再玩一次盒影。

[https://codepen.io/toomuchome/embed/RERXgw?height=600&default-tab=result&embed-version=2](https://codepen.io/toomuchome/embed/RERXgw?height=600&default-tab=result&embed-version=2)

[破坏王拉尔夫演示](https://linxea.github.io/wreck-it-ralph)

## 简单弹出动画

隐藏最初的拉尔夫，这样第一个拉尔夫总是处于随机位置。

```
<div id="ralph"></div> 
```

使用`cloneNode`复制 Ralph，并将其放入`body`。

```
const body = document.getElementsByTagName("body")[0];
const ralph = document.getElementById("ralph");

function duplicateRalphAtRandomPosition() {
    const ralphClone = ralph.cloneNode(true);
    ralphClone.style.display = "block";
    ralphClone.style.top = getRandomPercentage();
    ralphClone.style.left = getRandomPercentage();
    ralphClone.style.transform = `scale(${getRandomSize()})`;
    body.appendChild(ralphClone);
} 
```

将克隆的 Ralph 放在随机大小的随机位置。

```
// Return 0% to 100%
function getRandomPercentage() {
    return Math.random() * 101 + "%";
}

// Return 0.3 - 1
function getRandomSize() {
    return Math.random() * 0.8 + 0.3;
} 
```

使用`setInterval`
保持每秒一次，直到网络中断

```
// Duplicate Ralph every second yo
setInterval(function() {
    duplicateRalphAtRandomPosition();
}, 1000); 
```