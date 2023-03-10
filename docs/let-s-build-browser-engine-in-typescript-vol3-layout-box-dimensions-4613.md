# 让我们建立浏览器引擎！在 typescript vol3 布局框中，尺寸

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol3-layout-box-dimensions-4613>

布局树的布局框和尺寸。

*   布局发件箱。创建，新布局的快捷方式发件箱()
*   Rect#expandedBy
*   尺寸#paddingBox
*   尺寸#边框
*   尺寸#边距框

[![](img/a457f265b749be951675146c680468e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FoXBJKvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/az9bggtd8kqugk7s59qk.png)

布局发件箱。Create，新建 LayoutBox()的快捷方式

```
test("layout box create", () => {
  expect(() => LayoutBox.Create(new BoxType.AnonymousBlock())).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class LayoutBox {
  (snip)

  static Create(boxType: BoxType) {
    return new LayoutBox(
      new Dimensions(
        new Rect(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0)
      ),
      boxType,
      []
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Rect#expandedBy

```
test("expandedBy", () => {
  expect(new Rect(12, 13, 4, 5).expandedBy(new EdgeSizes(1, 2, 3, 4))).toEqual(
    new Rect(11, 10, 7, 12)
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class Rect {
  (snip)

  expandedBy(edge: EdgeSizes): Rect {
    return new Rect(
      this.x - edge.left,
      this.y - edge.top,
      this.width + edge.left + edge.right,
      this.height + edge.top + edge.bottom
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

尺寸#填充框
尺寸#边框
尺寸#边距框

[![](img/a457f265b749be951675146c680468e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FoXBJKvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/az9bggtd8kqugk7s59qk.png)T3】

```
const exampleDimensions = new Dimensions(
  new Rect(20, 30, 5, 5),
  new EdgeSizes(2, 3, 4, 5),
  new EdgeSizes(2, 3, 4, 5),
  new EdgeSizes(2, 3, 4, 5)
);

test("Dimensions#paddingBox", () => {
  expect(exampleDimensions.paddingBox()).toEqual(new Rect(18, 26, 10, 14));
});

test("Dimensions#borderBox", () => {
  expect(exampleDimensions.borderBox()).toEqual(new Rect(16, 22, 15, 23));
});

test("Dimensions#marginBox", () => {
  expect(exampleDimensions.marginBox()).toEqual(new Rect(14, 18, 20, 32));
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class Dimensions {
  (nits)

  // The area covered by the content area plus its padding.
  paddingBox(): Rect {
    return this.content.expandedBy(this.padding);
  }

  // The area covered by the content area plus padding and borders.
  borderBox(): Rect {
    return this.paddingBox().expandedBy(this.border);
  }

  // The area covered by the content area plus padding, borders, and margin.
  marginBox(): Rect {
    return this.borderBox().expandedBy(this.margin);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。

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