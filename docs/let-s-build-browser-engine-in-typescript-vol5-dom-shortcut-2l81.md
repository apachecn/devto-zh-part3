# 让我们建立浏览器引擎！在 typescript vol5 DOM 快捷方式中

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol5-dom-shortcut-2l81>

创建 DOM 快捷方式。稍后我将“实际”解析这个。

```
// $ node_modules/.bin/ts-node example/dom-shortcut.ts
import { elem, text } from "../src/dom";

// <html><body>Hello, world!</body></html>
//
// let root = elem("html");
// let body = elem("body");
// root.children.push(body);
// body.children.push(text("Hello, world!"));

const root = elem("html", new Map([]), []);
const body = elem("body", new Map([]), []);
root.children.push(body);
body.children.push(text("Hello, world!"));
console.dir(root, { depth: null });

// DomNode {
//   children: [
//     DomNode {
//       children: [
//         DomNode {
//           children: [],
//           nodeType: Text { format: 1, text: 'Hello, world!' }
//     }
//   ],
//     nodeType: Element {
//     format: 0,
//       element: ElementData { tagName: 'body', attributes: Map {} }
//   }
// }
// ],
//   nodeType: Element {
//     format: 0,
//       element: ElementData { tagName: 'html', attributes: Map {} }
//   }
// }
// 
```

Enter fullscreen mode Exit fullscreen mode

```
test("node type text", () => {
  expect(() => new NodeType.Text("no mean")).not.toThrow();
});

test("node type element", () => {
  expect(() => new NodeType.Element(new ElementData("no mean", new Map([])))).not.toThrow();
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class DomNode {
  children: DomNode[];
  nodeType: NodeType;

  constructor(children: DomNode[], nodeType: NodeType) {
    this.children = children;
    this.nodeType = nodeType;
  }
}

export namespace NodeType {
  export enum Format {
    Element,
    Text
  }

  export class Element {
    readonly format = Format.Element;
    element: ElementData;

    constructor(element: ElementData) {
      this.element = element;
    }
  }

  export class Text {
    readonly format = Format.Text;
    text: string;

    constructor(text: string) {
      this.text = text;
    }
  }
}

export type NodeType = NodeType.Element | NodeType.Text;

type AttrMap = Map<string, string>;

export class ElementData {
  tagName: string;
  attributes: AttrMap;

  constructor(tagName: string, attributes: AttrMap) {
    this.tagName = tagName;
    this.attributes = attributes;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

节点类型文本的别名快捷方式。

```
test("text", () => {
  expect(text("target")).toEqual(new DomNode([], new NodeType.Text("target")));
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export function text(data: string): DomNode {
  return new DomNode([], new NodeType.Text(data));
} 
```

Enter fullscreen mode Exit fullscreen mode

节点类型元素的别名快捷方式。

```
test("elem", () => {
  expect(elem("a", new Map([["b", "c"]]), [])).toEqual(
    new DomNode([], new NodeType.Element(new ElementData("a", new Map([["b", "c"]]))))
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export function elem(name: string, attrs: AttrMap, children: DomNode[]): DomNode {
  return new DomNode(children, new NodeType.Element(new ElementData(name, attrs)));
} 
```

Enter fullscreen mode Exit fullscreen mode

耶！

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