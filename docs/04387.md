# 让我们建立浏览器引擎！在 typescript vol6 CSS 快捷方式中

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol6-css-shortcut-1958>

手动创建 CSS 规则。

```
// $ node_modules/.bin/ts-node example/css-shortcut.ts

import {
  Color,
  CssValue,
  Declaration,
  Rule,
  Selector,
  SimpleSelector,
  Stylesheet,
  Unit
} from "../src/css";

// h1, h2, h3 { margin: auto; color: #cc0000; }
// div.note { margin-bottom: 20px; padding: 10px; }
// #answer { display: none; }

const stylesheet = new Stylesheet([]);
stylesheet.rules.push(
  new Rule(
    [
      new Selector.Simple(new SimpleSelector("h1", null, [])),
      new Selector.Simple(new SimpleSelector("h2", null, [])),
      new Selector.Simple(new SimpleSelector("h3", null, []))
    ],
    [
      new Declaration("margin", new CssValue.Keyword("auto")),
      new Declaration("color", new CssValue.ColorValue(new Color(204, 0, 0, 255)))
    ]
  )
);
stylesheet.rules.push(
  new Rule(
    [new Selector.Simple(new SimpleSelector("div", null, ["note"]))],
    [
      new Declaration("margin-bottom", new CssValue.Length(20, Unit.Px)),
      new Declaration("padding", new CssValue.Length(10, Unit.Px))
    ]
  )
);
stylesheet.rules.push(
  new Rule(
    [new Selector.Simple(new SimpleSelector(null, "answer", []))],
    [new Declaration("display", new CssValue.Keyword("none"))]
  )
);
console.dir(stylesheet, { depth: null });

// Stylesheet {
//   rules: [
//     Rule {
//       selectors: [
//         Simple {
//           format: 0,
//           selector: SimpleSelector { tagName: 'h1', id: null, classValue: [] }
//         },
//         Simple {
//           format: 0,
//           selector: SimpleSelector { tagName: 'h2', id: null, classValue: [] }
//         },
//         Simple {
//           format: 0,
//           selector: SimpleSelector { tagName: 'h3', id: null, classValue: [] }
//         }
//       ],
//       declarations: [
//         Declaration {
//           name: 'margin',
//           value: Keyword { format: 0, keyword: 'auto' }
//         },
//         Declaration {
//           name: 'color',
//           value: ColorValue {
//             format: 2,
//             colorValue: Color { r: 204, g: 0, b: 0, a: 255 }
//           }
//         }
//       ]
//     },
//     Rule {
//       selectors: [
//         Simple {
//           format: 0,
//           selector: SimpleSelector {
//             tagName: 'div',
//             id: null,
//             classValue: [ 'note' ]
//           }
//         }
//       ],
//       declarations: [
//         Declaration {
//           name: 'margin-bottom',
//           value: Length { format: 1, length: 20, unit: 0 }
//         },
//         Declaration {
//           name: 'padding',
//           value: Length { format: 1, length: 10, unit: 0 }
//         }
//       ]
//     },
//     Rule {
//       selectors: [
//         Simple {
//           format: 0,
//           selector: SimpleSelector {
//             tagName: null,
//             id: 'answer',
//             classValue: []
//           }
//         }
//       ],
//       declarations: [
//         Declaration {
//           name: 'display',
//           value: Keyword { format: 0, keyword: 'none' }
//         }
//       ]
//     }
//   ]
// } 
```

Enter fullscreen mode Exit fullscreen mode

```
test("declaration", () => {
  expect(() => new Declaration("name", new CssValue.Keyword("keyword"))).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class Declaration {
  name: string;
  value: CssValue;

  constructor(name: string, value: CssValue) {
    this.name = name;
    this.value = value;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
test("simple selector", () => {
  expect(() => new SimpleSelector(null, null, [])).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class SimpleSelector {
  tagName: string | null;
  id: string | null;
  classValue: string[];

  constructor(tagName: string | null, id: string | null, classValue: string[]) {
    this.tagName = tagName;
    this.id = id;
    this.classValue = classValue;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
test("selector simple", () => {
  expect(() => new Selector.Simple(new SimpleSelector(null, null, []))).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export namespace Selector {
  export enum Format {
    Simple
  }

  export class Simple {
    readonly format = Format.Simple;
    selector: SimpleSelector;

    constructor(selector: SimpleSelector) {
      this.selector = selector;
    }
  }
}

export type Selector = Selector.Simple; 
```

Enter fullscreen mode Exit fullscreen mode

```
test("rule", () => {
  expect(() => new Rule([], [])).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class Rule {
  selectors: Selector[];
  declarations: Declaration[];

  constructor(selectors: Selector[], declarations: Declaration[]) {
    this.selectors = selectors;
    this.declarations = declarations;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
test("stylesheet", () => {
  expect(() => new Stylesheet([])).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class Stylesheet {
  rules: Rule[];

  constructor(rules: Rule[]) {
    this.rules = rules;
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