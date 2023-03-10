# [更新 v2]罗宾的填充游戏

> 原文：<https://dev.to/darksmile92/update-v2-filler-game-by-robin-25pc>

距离我上一次更新这个游戏已经有几个星期了，但是今天我成功地更新了最近的改动！

以前的版本具有以下功能:

*   与自己(或同一台机器上的其他人)对战
*   获取关于最佳行动的建议
*   查看完成百分比
*   切换单元格上的所有者图标
*   切换建议的显示
*   用新游戏重新开始

#### 那么有什么新鲜事？

*   街区的新设计
*   CPU 敌人可用(移动延迟 1.2 秒)
*   CPU 敌人可以打开/关闭

#### 一些有趣的东西

我想给有方块的板子一个更好的外观，所以用了 CSS 渐变。渐变应该使用十六进制代码给出的纯色，并且应该从给定颜色上的黑色到较亮的版本，以达到期望的效果。

**如何在没有 CSS-前置处理器的情况下增亮一个彩色十六进制代码？**
我的纯色 hexcode 处于状态变量:
`this.state.cell.color.hex`

css 部分看起来是这样的:

```
linear-gradient(141deg, #2c3340 0%, #solidColorHex 51%, #lighterColorHex 75%) 
```

Enter fullscreen mode Exit fullscreen mode

因为我在 react 中，所以可以使用 javascript 来操作变量，所以我首先将颜色 hexcode 转换成一个 rgba 值:

```
export const hexToRgbA = hex => {
  var c;
  if (/^#([A-Fa-f0-9]{3}){1,2}$/.test(hex)) {
    c = hex.substring(1).split('');
    if (c.length == 3) {
      c = [c[0], c[0], c[1], c[1], c[2], c[2]];
    }
    c = '0x' + c.join('');
    return {r: (c >> 16) & 255, g: (c >> 8) & 255, b: c & 255, a: 1};
  }
  throw new Error('Bad Hex');
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后，我只需改变阿尔法通道来操纵颜色，并在 react 端使用变量作为我的样式对象来插值整个东西:

```
import {hexToRgbA} from '../helpers';
// ...

const rgbaCol = hexToRgbA(this.state.cell.color.hex);

return (
  <div
    className="cell"
      style={{
        background: `linear-gradient(141deg, #2c3340 0%, rgba(${rgbaCol.r}, ${rgbaCol.g}, ${rgbaCol.b}, 0.6) 51%, ${this.state.cell.color.hex} 75%)`,
        ...
        }}>
        ...
  </div> ); 
```

Enter fullscreen mode Exit fullscreen mode

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [暗黑微笑 92 ](https://github.com/DarkSmile92) / [填充-游戏-反应](https://github.com/DarkSmile92/filler-game-react)

### 作为 react app 实现的经典填充游戏！

<article class="markdown-body entry-content container-lg" itemprop="text">

## 作为 react 应用的填充游戏！

### 开始

在您选择的终端中运行以下命令:

```
git clone https://github.com/DarkSmile92/filler-game-react.git filler-game-react
cd filler-game-react
yarn
yarn start 
```

### 演示

参观[https://darksmile92.github.io/filler-game-react/](https://darksmile92.github.io/filler-game-react/)观看演示。

### 扭

#### 建议

现在，游戏只需计算每种颜色可能获得的最大细胞数，并使用最大值作为建议，就可以找到最佳的下一步。具有该颜色的按钮将带有边框。如果有多个相同的可能性，则只考虑第一个。

### 求解算法

目前还没有自动采取步骤的算法。接受挑战，在`./src/solver/`路径下创建解决方案并分享它们！

### 使用 Visual Studio 代码调试

首先将扩展 [Chrome 调试器](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)安装到 VSCode 实例中。

在终端中运行`yarn start`，在 VSCode 中设置断点，然后按`F5`开始调试。

</article>

[View on GitHub](https://github.com/DarkSmile92/filler-game-react)