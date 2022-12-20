# 如何用 JavaScript 操作 CSS 颜色

> 原文：<https://dev.to/bnevilleoneill/how-to-manipulate-css-colors-with-javascript-2oie>

[![](img/1b380aa8ba9daf57d8874bfe7b693e2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uC3ROgVN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A74prqSnfQ5ppH58DbLUqcA.jpeg)

### 颜色型号 101

我知道你是来学习处理颜色的——我们会实现的。但是在此之前，我们需要对 CSS 如何表示颜色有一个基本的了解。CSS 使用两种不同的颜色模型:RGB 和 HSL。让我们快速浏览一下两者。

#### RGB

“红、绿、蓝、[”RGB](https://codepen.io/AdamGiese/full/5783951de51e0db0f569d5abbd9cb2f7)的首字母由三个数字组成，每个数字表示最终颜色中包含多少相应颜色的光。在 CSS 中，每个数字都在 0-255 的范围内，可以写成 CSS rgb 函数的逗号分隔参数。比如 rgb(50，100，0)。

RGB 是一种“加色”颜色系统，这意味着每个数字越高，最终颜色就越亮。如果所有值相等，则颜色为灰度；如果所有值都为零，结果将是黑色的；如果所有值都是 255，结果将是白色的。

或者，您可以使用十六进制记数法来表示 RGB 颜色，其中每种颜色的整数都从基数 10 转换为基数 16。例如，rgb(50，100，0)将是#326400。

虽然我通常发现自己出于习惯而接触 RGB(尤其是十六进制)，但我经常发现它很难阅读，尤其难以操作。输入 HSL。

#### HSL

“色调、饱和度、亮度”的首字母组合 [HSL](https://codepen.io/AdamGiese/full/989988044f3b8cf6403e3c60f56dd612) 也由三个值组成。色调值对应于色轮上的位置，由 CSS 角度值表示；最常用的是 deg 单位。

饱和度是指颜色的强度，用百分比表示。当饱和度为 100%时，它是全彩色的；饱和度越低，颜色越浅，直到灰度达到 0%。

明度，也用百分比表示，指的是颜色有多亮。“常规”亮度为 50%。100%的明度将是纯白，0%的明度将是纯黑，与色调和饱和度值无关。

我发现 HSL 是一个更直观的模型。颜色之间的关系更加明显，对颜色的处理往往就像调整其中一个数字一样简单。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 颜色模型之间的转换

RGB 和 HSL 颜色模型都将颜色分解成各种属性。为了在语法之间进行转换，我们首先需要计算这些属性。

除了色调之外，我们所讨论的每一个值都可以用百分数来表示。甚至 RGB 值也是字节大小的百分比表示。在下面的公式和函数中，这些百分比将由 0 到 1 之间的小数表示。

我想指出的是，我不会深入讨论这些问题的数学；相反，我将简要回顾一下原始的数学公式，然后将其转换成 JavaScript 公式。

#### 从 RGB 计算亮度

亮度是三个 HSL 值中最容易计算的。数学上，公式显示如下，其中 M 是 RGB 值的最大值，M 是最小值:

[![](img/b89e589b514263ca481d334e2340a3aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VUCzLj1I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/163/0%2AmZxFLQvMNraVQWQS.png) 

<figcaption>亮度的数学公式</figcaption>

下面是与 JavaScript 函数相同的公式:

```
const rgbToLightness = (r,g,b) => 
    1/2 * (Math.max(r,g,b) + Math.min(r,g,b)); 
```

#### 从 RGB 计算饱和度

饱和度只是比明度稍微复杂一点。如果亮度为 0 或 1，则饱和度值将为 0。否则，它遵循下面的数学公式，其中 L 表示亮度:

[![](img/f35e070d724129b490ac55c60dd7a5a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4InPRGiO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/169/0%2AxZf55x3WTTJUIAG3.png) 

<figcaption>饱和度的数学公式</figcaption>

如 JavaScript:

```
const rgbToSaturation = (r,g,b) => {
  const L = rgbToLightness(r,g,b);
  const max = Math.max(r,g,b);
  const min = Math.min(r,g,b);
  return (L === 0 || L === 1)
   ? 0
   : (max - min)/(1 - Math.abs(2 * L - 1));
}; 
```

#### 从 RGB 计算色相

从 RGB 坐标计算色相角的公式有点复杂:

[![](img/e5bb2498b1ea007242a2bb650c19a2a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NUjy4VYm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/497/0%2AoLI0PhBJhkE8BK_e.png) 

<figcaption>色相的数学公式</figcaption>

如 JavaScript:

```
const rgbToHue = (r,g,b) => Math.round(
  Math.atan2(
    Math.sqrt(3) * (g - b),
    2 * r - g - b,
  ) * 180 / Math.PI
); 
```

180 的乘法/数学。最后的 PI 是把结果从弧度转换成度数。

#### 计算 HSL

所有这些函数都可以打包成一个单一的实用函数:

```
const rgbToHsl = (r,g,b) => {
  const lightness = rgbToLightness(r,g,b);
  const saturation = rgbToSaturation(r,g,b);
  const hue = rgbToHue(r,g,b);
  return [hue, saturation, lightness];
} 
```

#### 从 HSL 计算 RGB

在开始计算 RGB 之前，我们需要一些先决条件值。

首先是“色度”值:

[![](img/f5252d0d49d7189171d7983a5f369c86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RrfGG7He--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/331/0%2ANoxj7Gk7KGYqGfvx.png) 

<figcaption>色度的数学公式</figcaption>

我们也有一个临时色调值，我们将使用它的范围来决定我们属于哪个“段”的色调圈:

[![](img/4fc959ae377fa32964e52b5666889f96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IVZz2KJf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/90/0%2ADgjQEdahvhEjn60j.png) 

<figcaption>色相素数的数学公式</figcaption>

接下来，我们有一个“x”值，它将用作中间(第二大)分量值:

[![](img/92da9eba7cfb5519b7406710cfd1f7b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jocWO5DI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/381/0%2ArmrqPF1miT7a-O-O.png) 

<figcaption>临时“x”值的数学公式</figcaption>

我们有一个“m”值，用于调整每个亮度值:

[![](img/1ca8ceff745623d770caece579793ad7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Us-2TpV4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/124/0%2ATwM-2sYH0uEJhu35.png) 

<figcaption>明度匹配的数学公式</figcaption>

根据色调主要值，r、g 和 b 值将映射到 C、X 和 0:

[![](img/95e1307aa5c35049aee0c6c33966b487.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YK9il0Bg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/536/0%2Ak1pxEnWMU-rDQYIG.png) 

<figcaption>不考虑明度的 RGB 值的数学公式</figcaption>

最后，我们需要映射每个值来调整亮度:

[![](img/a8290ae6a763bc3120639311d18a5e14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EzXhAowd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/567/0%2AVlii9C8Cum3apLK-.png) 

<figcaption>用 RGB 计算明度的数学公式</figcaption>

将所有这些放入一个 JavaScript 函数:

```
const hslToRgb = (h,s,l) => {
  const C = (1 - Math.abs(2 * l - 1)) * s;
  const hPrime = h / 60;
  const X = C * (1 - Math.abs(hPrime % 2 - 1));
  const m = l - C/2;
  const withLight = (r,g,b) => [r+m, g+m, b+m];
if (hPrime <= 1) { return withLight(C,X,0); } else
  if (hPrime <= 2) { return withLight(X,C,0); } else
  if (hPrime <= 3) { return withLight(0,C,X); } else
  if (hPrime <= 4) { return withLight(0,X,C); } else
  if (hPrime <= 5) { return withLight(X,0,C); } else
  if (hPrime <= 6) { return withLight(C,0,X); }
} 
```

#### 创建颜色对象

为了在操作它们的属性时易于访问，我们将处理一个 JavaScript 对象。这可以通过包装先前编写的函数来创建:

```
const rgbToObject = (red,green,blue) => {
  const [hue, saturation, lightness] = rgbToHsl(red, green, blue);
  return {red, green, blue, hue, saturation, lightness};
}
const hslToObject = (hue, saturation, lightness) => {
  const [red, green, blue] = hslToRgb(hue, saturation, lightness);
  return {red, green, blue, hue, saturation, lightness};
} 
```

#### 举例

我强烈建议你花一些时间[来玩这个例子](https://codepen.io/AdamGiese/full/86b353c35a8bfe0868a8b48683faf668)。当您调整其他属性时，查看每个属性如何相互作用，可以让您更深入地了解这两种颜色模型是如何配合在一起的。

### 颜色操纵

既然我们已经有了在颜色模型之间转换的能力，让我们看看如何操纵这些颜色！

#### 更新属性

我们介绍的每个颜色属性都可以单独操作，返回一个新的颜色对象。比如我们可以写一个旋转色相角的函数:

```
const rotateHue = rotation => ({hue, ...rest}) => {
  const modulo = (x, n) => (x % n + n) % n;
  const newHue = modulo(hue + rotation, 360);
return { ...rest, hue: newHue };
} 
```

rotateHue 函数接受一个旋转参数并返回一个新函数，该函数接受并返回一个颜色对象。这允许容易地创建新的“旋转”功能:

```
const rotate30 = rotateHue(30);
const getComplementary = rotateHue(180);
const getTriadic = color => {
  const first = rotateHue(120);
  const second = rotateHue(-120);
  return [first(color), second(color)];
} 
```

同样，您可以编写函数来使颜色饱和或变亮，或者相反，使颜色不饱和或变暗。

```
const saturate = x => ({saturation, ...rest}) => ({
  ...rest,
  saturation: Math.min(1, saturation + x),
});
const desaturate = x => ({saturation, ...rest}) => ({
  ...rest,
  saturation: Math.max(0, saturation - x),
});
const lighten = x => ({lightness, ...rest}) => ({
  ...rest,
  lightness: Math.min(1, lightness + x)
});
const darken = x => ({lightness, ...rest}) => ({
  ...rest,
  lightness: Math.max(0, lightness - x)
}); 
```

#### 颜色谓词

除了颜色操作，您还可以编写“谓词”——即返回布尔值的函数。

```
const isGrayscale = ({saturation}) => saturation === 0;
const isDark = ({lightness}) => lightness < .5; 
```

### 处理颜色数组

#### 滤镜

JavaScript []。filter 方法接受一个谓词，并返回一个包含所有“通过”的元素的新数组我们在上一节中写的谓词可以用在这里:

```
const colors = [/* ... an array of color objects ... */];
const isLight = ({lightness}) => lightness > .5;
const lightColors = colors.filter(isLight); 
```

#### 排序

要对颜色数组进行排序，首先需要编写一个“比较器”函数。这个函数接受一个数组的两个元素，并返回一个数字来表示“获胜者”正数表示第一个元素应该先排序，负数表示第二个元素应该先排序。零值表示平局。

比如这里有一个比较两种颜色明度的函数:

```
const compareLightness = (a,b) => a.lightness - b.lightness; 
```

这里有一个比较饱和度的函数:

```
const compareSaturation = (a,b) => a.saturation - b.saturation; 
```

为了防止代码重复，我们可以编写一个高阶函数来返回一个比较函数来比较任何属性:

```
const compareAttribute = attribute =>
  (a,b) => a[attribute] - b[attribute];
const compareLightness = compareAttribute('lightness');
const compareSaturation = compareAttribute('saturation');
const compareHue = compareAttribute('hue'); 
```

#### 平均属性

您可以通过组合各种 JavaScript 数组方法来平均颜色数组的特定属性。首先，您可以通过用 reduce 求和并除以数组长度来计算属性的平均值:

```
const colors = [/* ... an array of color objects ... */];
const toSum = (a,b) => a + b;
const toAttribute = attribute => element => element[attribute];
const averageOfAttribute = attribute => array =>
  array.map(toAttribute(attribute)).reduce(toSum) / array.length; 
```

你可以用它来“正常化”一组颜色:

```
/* ... continuing */ 
```

```
const normalizeAttribute = attribute => array => {
  const averageValue = averageOfAttribute(attribute)(array);
  const normalize = overwriteAttribute(attribute)(averageValue);
  return normalize(array);
}
const normalizeSaturation = normalizeAttribute('saturation');
const normalizeLightness = normalizeAttribute('lightness');
const normalizeHue = normalizeAttribute('hue'); 
```

### 结论

颜色是网络不可或缺的一部分。将颜色分解成不同的属性，可以让你对颜色进行巧妙的处理，并为各种各样的可能性打开大门。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[如何用 JavaScript 操纵 CSS 颜色](https://blog.logrocket.com/how-to-manipulate-css-colors-with-javascript-fb547113a1b8/)最先出现在[博客](https://blog.logrocket.com)上。