# 让我们建立浏览器引擎！在 typescript vol9 DOM 和样式树规则中

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol9-dom-and-rules-to-style-tree-18kp>

将 DOM 节点和规则转换为 StyleTree！

```
 test("matchRule none-match", () => {
  expect(matchRule(new ElementData("no mean", new Map([])), new Rule([], []))).toBeNull();
});

test("matchRule match first", () => {
  const rule = new Rule(
    [
      // specificity a=1, b=0, c=0
      new Selector.Simple(new SimpleSelector(null, "target", [])),
      // specificity a=0, b=0, c=1
      new Selector.Simple(new SimpleSelector("target", null, []))
    ],
    []
  );
  expect(matchRule(new ElementData("target", new Map([["id", "target"]])), rule)).toEqual([
    [1, 0, 0],
    rule
  ]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export type MatchedRule = [Specificity, Rule];

// If `rule` matches `elem`, return a `MatchedRule`. Otherwise return null.
export function matchRule(elem: ElementData, rule: Rule): null | MatchedRule {
  // Find the first (most specific) matching selector.
  // Because our CSS parser stores the selectors from most- to least-specific,
  const found = rule.selectors.find(selector => {
    return matches(elem, selector);
  });
  if (found === undefined) {
    return null;
  }
  return [found.selector.specificity(), rule];
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 test("matchingRules none-match", () => {
  expect(matchingRules(new ElementData("no mean", new Map([])), new Stylesheet([]))).toEqual([]);
});

test("matchingRules matches", () => {
  const rule1 = new Rule(
    [
      // specificity a=1, b=0, c=0
      new Selector.Simple(new SimpleSelector(null, "target", [])),
      // specificity a=0, b=0, c=1
      new Selector.Simple(new SimpleSelector("target", null, []))
    ],
    []
  );
  const rule2 = new Rule(
    [
      // specificity a=0, b=0, c=1
      new Selector.Simple(new SimpleSelector("target", null, []))
    ],
    []
  );
  expect(
    matchingRules(
      new ElementData("target", new Map([["id", "target"]])),
      new Stylesheet([rule1, rule2])
    )
  ).toEqual([[[1, 0, 0], rule1], [[0, 0, 1], rule2]]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export function matchingRules(elem: ElementData, stylesheet: Stylesheet): MatchedRule[] {
  return stylesheet.rules
    .map(rule => {
      return matchRule(elem, rule);
    })
    .filter(
      (matchedOrNull): matchedOrNull is MatchedRule => {
        return matchedOrNull !== null;
      }
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 test("specifiedValues none", () => {
  expect(specifiedValues(new ElementData("no mean", new Map([])), new Stylesheet([]))).toEqual(
    new Map([])
  );
});

test("specifiedValues", () => {
  const rule1 = new Rule(
    [
      // specificity a=1, b=0, c=0
      new Selector.Simple(new SimpleSelector(null, "target", [])),
      // specificity a=0, b=0, c=1
      new Selector.Simple(new SimpleSelector("target", null, []))
    ],
    [
      new Declaration("override", new CssValue.Keyword("current")),
      new Declaration("not-override1", new CssValue.Keyword("value1"))
    ]
  );
  const rule2 = new Rule(
    [
      // specificity a=0, b=0, c=1
      new Selector.Simple(new SimpleSelector("target", null, []))
    ],
    [
      new Declaration("override", new CssValue.Keyword("prev")),
      new Declaration("not-override2", new CssValue.Keyword("value2"))
    ]
  );
  expect(
    specifiedValues(
      new ElementData("target", new Map([["id", "target"]])),
      new Stylesheet([rule1, rule2])
    )
  ).toEqual(
    new Map([
      ["not-override1", new CssValue.Keyword("value1")],
      ["not-override2", new CssValue.Keyword("value2")],
      ["override", new CssValue.Keyword("current")]
    ])
  );
});

test("compare matched rule right a", () => {
  expect(
    compareMatchedRule([[0, 0, 0], new Rule([], [])], [[1, 0, 0], new Rule([], [])])
  ).toBeLessThan(0);
});

test("compare matched rule left a", () => {
  expect(
    compareMatchedRule([[1, 0, 0], new Rule([], [])], [[0, 0, 0], new Rule([], [])])
  ).toBeGreaterThan(0);
});

test("compare matched rule right b", () => {
  expect(
    compareMatchedRule([[0, 0, 0], new Rule([], [])], [[0, 1, 0], new Rule([], [])])
  ).toBeLessThan(0);
});

test("compare matched rule left b", () => {
  expect(
    compareMatchedRule([[0, 1, 0], new Rule([], [])], [[0, 0, 0], new Rule([], [])])
  ).toBeGreaterThan(0);
});

test("compare matched rule right c", () => {
  expect(
    compareMatchedRule([[0, 0, 0], new Rule([], [])], [[0, 0, 1], new Rule([], [])])
  ).toBeLessThan(0);
});

test("compare matched rule left c", () => {
  expect(
    compareMatchedRule([[0, 0, 1], new Rule([], [])], [[0, 0, 0], new Rule([], [])])
  ).toBeGreaterThan(0);
});

test("compare matched rule same", () => {
  expect(compareMatchedRule([[0, 0, 0], new Rule([], [])], [[0, 0, 0], new Rule([], [])])).toBe(0);
});

test("sort compare matched rule", () => {
  const left: MatchedRule = [[0, 0, 0], new Rule([], [])];
  const right: MatchedRule = [[1, 0, 0], new Rule([], [])];
  expect([left, right].sort(compareMatchedRule)).toEqual([left, right]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
 export function compareMatchedRule(left: MatchedRule, right: MatchedRule): number {
  const [[la, lb, lc]] = left;
  const [[ra, rb, rc]] = right;
  if (la !== ra) {
    return la - ra;
  } else if (lb !== rb) {
    return lb - rb;
  } else if (lc !== rc) {
    return lc - rc;
  }
  return 0;
}

// Apply styles to a single element, returning the specified styles.
//
// To do: Allow multiple UA/author/user stylesheets, and implement the cascade.
export function specifiedValues(elem: ElementData, stylesheet: Stylesheet): PropertyMap {
  const values = new Map<string, CssValue>([]);
  const rules = matchingRules(elem, stylesheet);
  rules.sort(compareMatchedRule);
  for (const [, rule] of rules) {
    for (const declaration of rule.declarations) {
      values.set(declaration.name, declaration.value);
    }
  }
  return values;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
test("style node text", () => {
  expect(styleTree(text("hoge"), new Stylesheet([]))).toEqual(
    new StyledNode(text("hoge"), new Map([]), [])
  );
});

test("style node element", () => {
  const rule = new Rule(
    [new Selector.Simple(new SimpleSelector(null, "target", []))],
    [new Declaration("some", new CssValue.Keyword("foo"))]
  );
  const element = elem("no mean", new Map([["id", "target"]]), []);
  expect(styleTree(element, new Stylesheet([rule]))).toEqual(
    new StyledNode(element, new Map([["some", new CssValue.Keyword("foo")]]), [])
  );
});

test("style node children", () => {
  const rule = new Rule(
    [new Selector.Simple(new SimpleSelector(null, "target", []))],
    [new Declaration("some", new CssValue.Keyword("foo"))]
  );
  const element = elem("no mean", new Map([]), [elem("no mean", new Map([["id", "target"]]), [])]);
  expect(styleTree(element, new Stylesheet([rule]))).toEqual(
    new StyledNode(element, new Map([]), [
      new StyledNode(
        elem("no mean", new Map([["id", "target"]]), []),
        new Map([["some", new CssValue.Keyword("foo")]]),
        []
      )
    ])
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
 // Apply a stylesheet to an entire DOM tree, returning a StyledNode tree.
export function styleTree(root: DomNode, stylesheet: Stylesheet): StyledNode {
  switch (root.nodeType.format) {
    case NodeType.Format.Text:
      return new StyledNode(
        root,
        new Map([]),
        // NOTE: text node has children??? I'm not sure
        []
      );
    case NodeType.Format.Element:
      return new StyledNode(
        root,
        specifiedValues(root.nodeType.element, stylesheet),
        root.children.map(child => {
          return styleTree(child, stylesheet);
        })
      );
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
*   [让我们来打造浏览器引擎吧！在打字稿 vol8 特异性](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol8-specificity-1c4c)
*   [让我们来打造浏览器引擎吧！在 typescript vol9 中 DOM 和规则到样式树](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol9-dom-and-rules-to-style-tree-18kp)