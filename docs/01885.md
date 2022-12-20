# 让我们建立浏览器引擎！在 typescript vol8 特性中

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol8-specificity-1c4c>

[计算选择器的特异性](https://www.w3.org/TR/selectors/#specificity)

我不确定我不算 C.

```
// Examples:
//
// *               /* a=0 b=0 c=0 */
// LI              /* a=0 b=0 c=1 */
// UL LI           /* a=0 b=0 c=2 */
// UL OL+LI        /* a=0 b=0 c=3 */
// H1 + *[REL=up]  /* a=0 b=1 c=1 */
// UL OL LI.red    /* a=0 b=1 c=3 */
// LI.red.level    /* a=0 b=2 c=1 */
// #x34y           /* a=1 b=0 c=0 */
// #s12:not(FOO)   /* a=1 b=0 c=1 */
// .foo :is(.bar, #baz)
//                 /* a=1 b=1 c=0 */

test("specificity li", () => {
  expect(new SimpleSelector("li", null, []).specificity()).toEqual([0, 0, 1]);
});

test("specificity li.red.level", () => {
  expect(new SimpleSelector("li", null, ["red", "level"]).specificity()).toEqual([0, 2, 1]);
});

test("specificity #x34y", () => {
  expect(new SimpleSelector(null, "x34y", []).specificity()).toEqual([1, 0, 0]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export type Specificity = [number, number, number];

export class SimpleSelector {
  (snip)

  specificity(): Specificity {
    // http://www.w3.org/TR/selectors/#specificity
    const a = this.id === null ? 0 : 1;
    const b = this.classValue.length;
    // NOTE: I don't support chained selectors now.
    const c = this.tagName === null ? 0 : 1;
    return [a, b, c];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

:思考 _ 面对:

## 参考文献

*   [我们来搭建一个浏览器引擎吧！第 1 部分:入门](https://limpet.net/mbrubeck/2014/08/08/toy-layout-engine-1.html)
*   [姆布鲁贝克/罗宾逊](https://github.com/mbrubeck/robinson)
*   [sanemat/js-toy-engine](https://github.com/sanemat/js-toy-engine)
*   [sanemat/ts-toy-engine](https://github.com/sanemat/ts-toy-engine)

## 系列

*   [让我们来打造浏览器引擎吧！在 typescript vol0 玩具浏览器引擎](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol0-toy-browser-engine-egm)
*   [让我们来打造浏览器引擎吧！在 typescript vol1 画布上用颜色](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol1-canvas-with-color-2nd7)
*   [让我们来打造浏览器引擎吧！在 typescript vol2 中显示命令](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol2-display-command-3466)
*   [让我们来打造浏览器引擎吧！在 typescript vol3 布局框中，尺寸](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol3-layout-box-dimensions-4613)
*   [让我们来打造浏览器引擎吧！在 typescript vol4 布局树中显示列表](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol4-layout-tree-to-display-list-c54)
*   [让我们来打造浏览器引擎吧！在 typescript vol5 中 DOM 快捷方式](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol5-dom-shortcut-2l81)
*   [让我们来打造浏览器引擎吧！在 typescript vol6 CSS 快捷方式](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol6-css-shortcut-1958)
*   [让我们来打造浏览器引擎吧！在 typescript vol7 选择器中匹配](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol7-selector-matching-1faf)
*   [让我们来打造浏览器引擎吧！在打字稿 vol8 特异性](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol8-specificity-1c4c)