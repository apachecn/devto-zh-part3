# CJS 层叠 JavaScript 风格

> 原文：<https://dev.to/joshrkulp/cjs-cascading-javascript-style-1cmc>

# JSX CSS

我一直在做一些新的 React 项目，CSS 在 JavaScript 中的工作方式对我来说既奇怪又简单。我喜欢使用 JSON 对象的想法，但奇怪的是，我看过的大多数演示都没有超出简单的内联 CSS 的范围。

这是他们中大多数人表现出来的。

```
 <div style={{background:'red'}}>Hello World!</div> 
```

基本上，您是将一个 Javascript 对象`{background = 'red'}`作为内联样式。

下一步是将它提取到一个 const 中，然后放入样式中。这开始看起来更像普通 CSS。

```
 render() {
    const style = {
        background: 'red'
    }
        return (
            <div>
                <div style={style}>Hello World! Object</div>
            </div>
        );
    } 
```

这在 CSS 类级别上工作得很好，但是当你想做更多的时候，你就开始卡住了。这就是级联概念的由来。

这是它在普通 CSS 中的样子

```
 <style>
.Item{
 background:"Red";
}

.Item .Large{
    font-size:30px;
}

</style>

<div class="Item">Item 1</div>
<div class="Item Large">Item 2</div> 
```

然后在 JSX

```
render() {
    const style = {}

    style.Item = {
        background: 'red'
    };

    style.Item.Large = {
        ... style.Item,
        fontSize: 30
    }

        return (
            <div>
                <div style={style.Item}>Hello World! Cascading </div>
                <div style={style.Item.Large}>Hello World! Cascading Large</div>
            </div>
        );
    } 
```

在 JSX 中，我开始使用 spread 操作符`...`来复制相关对象中的项目或者层叠它们。

我有这个啊哈🤦‍♂️时刻前几天，并希望这有助于一些人沿着 CSS 在 JavaScript 在 React 的道路。