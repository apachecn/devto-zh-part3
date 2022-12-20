# 让我们建立浏览器引擎！在 typescript vol7 选择器匹配中

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol7-selector-matching-1faf>

对于选择器匹配，我创建了一些快捷方式。

```
test("element id found", () => {
  expect(new ElementData("no mean", new Map([["id", "target"]])).id()).toEqual("target");
});

test("element id not found", () => {
  expect(new ElementData("no mean", new Map([["no mean", "no mean"]])).id()).toBeNull();
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class ElementData {
  (snip)

  id(): string | null {
    return this.attributes.get("id") || null;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
test("element class 1 found", () => {
  expect(new ElementData("no mean", new Map([["class", "target1"]])).classes()).toEqual(
    new Set(["target1"])
  );
});

test("element class 2 found", () => {
  expect(new ElementData("no mean", new Map([["class", "target1 target2"]])).classes()).toEqual(
    new Set(["target1", "target2"])
  );
});

test("element class 0 found", () => {
  expect(new ElementData("no mean", new Map([])).classes()).toEqual(new Set([]));
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class ElementData {
  (snip)

  classes(): Set<string> {
    const classes = this.attributes.get("class");
    if (!classes) {
      return new Set([]);
    }
    return new Set(classes.split("  "));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

选择器匹配。使用这个简单的选择器。

```
test("matches simple selector", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("no mean", new Map([])),
      new SimpleSelector(null, null, [])
    )
  ).toBe(true);
});

test("matches simple selector different tag", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("other", new Map([])),
      new SimpleSelector("some", null, [])
    )
  ).toBe(false);
});

test("matches simple selector same tag", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("target", new Map([])),
      new SimpleSelector("target", null, [])
    )
  ).toBe(true);
});

test("matches simple selector different id", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("no mean1", new Map([["id", "other"]])),
      new SimpleSelector(null, "some", [])
    )
  ).toBe(false);
});

test("matches simple selector same id", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("no mean1", new Map([["id", "target"]])),
      new SimpleSelector(null, "target", [])
    )
  ).toBe(true);
});

test("matches simple selector different class", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("no mean1", new Map([["class", "other1 other2"]])),
      new SimpleSelector(null, null, ["some1", "some2"])
    )
  ).toBe(false);
});

test("matches simple selector same class", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("no mean1", new Map([["class", "target1 other2"]])),
      new SimpleSelector(null, null, ["target1", "some2"])
    )
  ).toBe(true);
});

test("matches simple selector same tag, other id", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("same", new Map([["id", "other"]])),
      new SimpleSelector("same", "some", [])
    )
  ).toBe(false);
});

test("matches simple selector same tag, same id, different class", () => {
  expect(
    matchesSimpleSelector(
      new ElementData("same", new Map([["id", "same"], ["class", "other1 other2"]])),
      new SimpleSelector("same", "same", ["some1", "some2"])
    )
  ).toBe(false);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
// returns true if there are no none-match
export function matchesSimpleSelector(elem: ElementData, selector: SimpleSelector): boolean {
  const tagName = selector.tagName;
  if (tagName && tagName !== elem.tagName) {
    return false;
  }

  const id = selector.id;
  if (id && id !== elem.id()) {
    return false;
  }

  const classes = selector.classValue;
  if (classes.length !== 0) {
    let included = false;
    for (let className of classes) {
      if (elem.classes().has(className)) {
        included = true;
        break;
      }
    }
    if (!included) {
      return false;
    }
  }

  return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
test("matches no none-match", () => {
  expect(
    matches(
      new ElementData("no mean", new Map([])),
      new Selector.Simple(new SimpleSelector(null, null, []))
    )
  ).toBe(true);
});

test("matches none-match", () => {
  expect(
    matches(
      new ElementData("no mean", new Map([])),
      new Selector.Simple(new SimpleSelector(null, "some", []))
    )
  ).toBe(false);
});

test("matches match", () => {
  expect(
    matches(
      new ElementData("no mean", new Map([["id", "target"]])),
      new Selector.Simple(new SimpleSelector(null, "target", []))
    )
  ).toBe(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export function matches(elem: ElementData, selector: Selector): boolean {
  switch (selector.format) {
    case Selector.Format.Simple:
      return matchesSimpleSelector(elem, selector.selector);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

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