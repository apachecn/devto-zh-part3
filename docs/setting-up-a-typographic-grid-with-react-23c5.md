# 使用 React 设置印刷网格

> 原文：<https://dev.to/metamn/setting-up-a-typographic-grid-with-react-23c5>

拥有一个所有元素都垂直和水平对齐的理想网格是可能的。而且有点难。

## 古典版式

经典的排版会建立一个排版网格，并将文本(最重要的)和其他对象(图像、装饰)放置在该网格上。

排印网格以这种方式设置，以帮助读者阅读长格式文本。字体被设置为高度可读的大小；文本的行数设置为 60-80 个字符一行；文本行之间的垂直距离被设置为使长格式文本令人愉快。

经典的印刷术起源于纸张尺寸固定的印刷机。这有助于建立一个明确的网格。在网络上，由于许多不同的屏幕尺寸，建立一个网格更加困难。

[Iain Lamb 与排版](http://lamb.cc/typograph/)给出了一个启发性的研究和一个工具来实现这一点。

它使用浏览器的默认设置，并逐渐建立一个网格，在这个网格中，内容可以完美地垂直和水平对齐。

[![A typographic grid](img/29361ad5878a5144b8cbc7ed633bf8fc.png)](///react/static/c0c88720a7ede1cf8281b597bbcdd0b1/27903/typographic-grid.png)

## 字体大小和行高

> 在这里，与谷物一起工作是值得的。与其从头开始设计一切，不如先仔细考虑浏览器默认提供的功能。从这里开始编写 CSS，作为与默认样式差异的最小表达。这种方法使用最少的代码完成工作，对页面下载速度和将来进行更改的容易程度产生了积极的影响(假设代码越少，意味着需要理解、维护和编辑的内容就越少)。

浏览器默认设置字体大小为`16px`，行高[大致为](https://developer.mozilla.org/en-US/docs/Web/CSS/line-height)到`1.2`。

Iain 建议以特定的方式使用这些值。

将字体大小设置为`100%`(即`16px`)，而不是其他任何单位。这是一种不引人注目的设置字体大小的方式。它不会覆盖浏览器首选项中设置的字体大小，并且允许用户通过缩放来增大或减小字体大小。

行高应设置为无单位。这样,`font-size * line-height`组合在网格上定义了一个全局单位——单元格。即使后来子元素的字体大小发生了变化，网格单元的大小在整个页面上保持不变。

使用`font-size: 100%; line-height: 1.25`时，网格单元大小将为`16px * 1.25 = 20px`。

## 仅用`em`调整子元素大小

到目前为止，即使我们使用了`%`和一个无单位的度量来设置网格，它也是以像素来度量的。

在有像素的网格上调整子元素的大小是一种不好的做法。

[像素不缩放](https://stackoverflow.com/questions/609517/why-em-instead-of-px)，或者更准确地说，它们缩放，但不是成比例的。当缩放以像素为单位设置的页面时，元素会溢出。当设置了相对弹性单位时，如`em`或`rem`，页面将会很好地按比例缩放。

当使用弹性度量而不是绝对度量时，页面在响应式 web 上缩放良好。有很多弹性度量——em、rem、%,其中[只有](https://css-tricks.com/confused-rem-em/) `em`考虑了它的母字体大小。

而 Iain 的网格是建立在字体大小上的。

> em 单元的机制提供了一种相对于父元素来调整子元素大小的极好方法。事实上，如果每个子元素都在 em 中定义其大小值，就会引发连锁反应。每个子元素按比例绑定到其父元素，而父元素又绑定到其父元素，一直到根元素，即 all 的祖先。这样，整个文档的比例最终被定义为一个单一的共享值:的字体大小
> 
> 。
> 
> 以这种方式调整大小的文档享有一个黄金属性，这是大多数网页都应该提供的:比例缩放。如果用户或设计者更改了基本字体大小，页面上的所有其他元素都会相应地调整大小，保持它们与
> 
> 。看起来好像视图刚刚被放大或缩小。

将网格大小从`px`转换为`em`很容易。`1em`等于正文字号。在`16px * 1.25`公式中`16px`可以替换为`1em`得到`1.25em`作为网格大小。

## `<body>`

如果可能，在`<body>`元素中设置网格。这样，整个页面将有一个通用的网格布局，所有的孩子都可以继承和建立。

```
/**
* Set up the global typographic grid with styled-components
*/
const GlobalStyle = createGlobalStyle`
    body {
        font-size: 100%;
        line-height: 1.25;
        --lem: 1.25em;
    }
` 
```

在这个设置中最重要的是`--lem`也就是`line in em`。该变量定义了水平和垂直网格线的大小，使网格单元的宽度和高度为`1.25em`。

任何元素都可以用`--lem`指标定位在网格上。

## 反应灵敏

默认字体大小为 16px，这使得文本在 1K 或 2K 这样的大屏幕上无法阅读。文本大小应该随着视口的增长而增长。

当一个页面必须被放大才能变得可读时，这很烦人。相反，网站应该呈现一个已经优化的体验。

有了 Iain 提出的系统，建立一个快速响应的电网变得轻而易举。`<body>`的第一个子容器应该随着视窗的增长而增加字体大小。

```
/**
 * The responsive typography settings
 */
const TypographicGrid = {
    mobile: {
        fontSize: '100%',
    },
    tablet: {
        fontSize: '110%',
    },
    tabletL: {
        fontSize: '140%',
    },
    laptop: {
        fontSize: '160%',
    },
    desktop: {
        fontSize: '200%',
    }
}

/**
 * The responsive container
 *
 * Note: breakpoints don't follow best practices, just avoid them for now ...
 */
const ResponsiveContainer = styled.div`
    @media (max-width: 767px) {
        font-size: ${props => props.typographicGrid.mobile.fontSize};
    }
    @media (min-width: 768px) and (max-width: 1023px) {
        font-size: ${props => props.typographicGrid.tablet.fontSize};
    }
    @media (min-width: 1024px) and (max-width: 1365px) {
        font-size: ${props => props.typographicGrid.tabletL.fontSize};
    }
    @media (min-width: 1366px) and (max-width: 1559px) {
        font-size: ${props => props.typographicGrid.laptop.fontSize};
    }
    @media (min-width: 1600px) {
        font-size: ${props => props.typographicGrid.desktop.fontSize};
    }
`

/**
* The main container
*/
const Container = styled(ResponsiveContainer)`
    padding: var(--lem);

    // From Iain's Typograph
    h1 { font-size:1.5em; padding:1.45833em 0; }
    h2 { font-size:1.33333em; padding:1.25em 0; }
    h3 { font-size:1.16667em; padding:1.51785em 0; }

    // --lem in action
    // Note: this kind of padding is just for demo purposes ...
    p {
        + p {
            margin-top: calc(var(--lem) * 2);
            margin-left: calc(var(--lem) * 2);
        }
    }
`

/**
* The main class
*/
class TypographyClassic extends React.Component {
    render() {
        return (
            <Container> ... </Container>
        )
    } 
```

## 什么打破了网格？

拥有一个所有元素都垂直和水平对齐的理想网格是可能的，但是很困难。人们应该考虑努力的价值。

如果你有一个设置了`overflow-x`的代码块(`<pre><code>...`)，水平滚动条将会打破网格。

如果你有一个多行标题…它会打破网格。伊恩的计算器适用于单行标题。

随着向网格中添加新元素，这个列表将会增长。

## 告诫

经典排版在某些情况下是有用的，即当所有的东西都只使用`em`来调整大小时，并且每一项都与网格单元格的顶部/左侧对齐。

结合像 Flexbox 的`justify-content: center`这样的现代技术，网格被打破了。或者用像`vw`或`vh`这样的单位来确定元素的大小和位置。

## 什么时候用？

坚持完美的网格——这变得非常脆弱——只有当长格式文本像文章内容一样显示时才被推荐。

对于其他所有东西——布局、导航等——有更自由的解决方案。

然而，如果一个人遵循弗兰克·奇梅罗的方法，并且想要按照大卫·霍克尼的风格来组合布局，那么这种技术会非常强大。

## 现场演示及源代码

[带有源代码的 MR-UI 故事书](https://tinyurl.com/y85a7dh5)在【Github([https://Github . com/meta Mn/MR-UI/tree/master/src/basics/typical classic](https://github.com/metamn/mr-ui/tree/master/src/basics/TypographyClassic)

## 资源

*   [伊恩·兰姆的排版](http://lamb.cc/typograph/)
*   [EM 对 REM](https://css-tricks.com/confused-rem-em/)
*   [像素不缩放](https://stackoverflow.com/questions/609517/why-em-instead-of-px)