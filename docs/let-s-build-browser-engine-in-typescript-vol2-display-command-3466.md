# 让我们建立浏览器引擎！在 typescript vol2 中显示命令

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol2-display-command-3466>

```
// node_modules/.bin/ts-node example/display-command.ts | display

import * as Jimp from "jimp";

import { Rect } from "../src/layout";
import { Canvas, DisplayCommand } from "../src/painting";
import { Color } from "../src/css";
const black = new Color(0, 0, 0, 255);

const canvas = Canvas.Create(200, 100);
canvas.paintItem(new DisplayCommand.SolidColor(black, new Rect(10, 20, 30, 30)));

Jimp.create(canvas.width, canvas.height)
  .then((value: Jimp) => {
    let buffer = value.bitmap.data;
    for (let i = 0; i < canvas.pixels.length; i++) {
      buffer[i * 4] = canvas.pixels[i].r;
      buffer[i * 4 + 1] = canvas.pixels[i].g;
      buffer[i * 4 + 2] = canvas.pixels[i].b;
      buffer[i * 4 + 3] = canvas.pixels[i].a;
    }
    return value.getBufferAsync(Jimp.MIME_PNG);
  })
  .then((value: Buffer) => {
    process.stdout.write(value);
  })
  .catch((error: Error) => {
    console.error(error);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/eae3709093663bd8c802e5d8fbafa25b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KYXmRlmc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qe0x8p5727zl18rvf6fg.png)T3】

```
import { Rect } from "../src/layout";

test("rect", () => {
  expect(new Rect(1, 2, 3, 4).height).toBe(4);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class Rect {
  x: number;
  y: number;
  width: number;
  height: number;

  constructor(x: number, y: number, width: number, height: number) {
    this.x = x;
    this.y = y;
    this.width = width;
    this.height = height;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
import { Canvas, DisplayCommand } from "../src/painting";
import { Color } from "../src/css";
import { Rect } from "../src/layout";

const white = new Color(255, 255, 255, 255);
const black = new Color(0, 0, 0, 255);

test("solid color", () => {
  expect(new DisplayCommand.SolidColor(white, new Rect(0, 0, 0, 0)).format).toEqual(
    DisplayCommand.Format.SolidColor
  );
});

test("canvas paint item", () => {
  const canvas = Canvas.Create(2, 3);
  canvas.paintItem(new DisplayCommand.SolidColor(black, new Rect(0, 0, 1, 1)));
  expect(canvas.pixels).toEqual([black, white, white, white, white, white]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
import { Color } from "./css";
import { Rect } from "./layout";
const mathClamp = require("math-clamp");

export class Canvas {
  // snip

  paintItem(item: DisplayCommand) {
    switch (item.format) {
      case DisplayCommand.Format.SolidColor:
        const rect = item.rect;
        const color = item.color;
        let x0 = mathClamp(rect.x, 0.0, this.width);
        let y0 = mathClamp(rect.y, 0.0, this.height);
        let x1 = mathClamp(rect.x + rect.width, 0.0, this.width);
        let y1 = mathClamp(rect.y + rect.height, 0.0, this.height);
        for (let y = y0; y < y1; y++) {
          for (let x = x0; x < x1; x++) {
            // TODO: alpha compositing with existing pixel
            this.pixels[x + y * this.width] = color;
          }
        }
    }
  }
}

export namespace DisplayCommand {
  export enum Format {
    SolidColor
  }

  export class SolidColor {
    readonly format = Format.SolidColor;
    color: Color;
    rect: Rect;
    constructor(color: Color, rect: Rect) {
      this.color = color;
      this.rect = rect;
    }
  }
}

export type DisplayCommand = DisplayCommand.SolidColor; 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/eae3709093663bd8c802e5d8fbafa25b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KYXmRlmc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qe0x8p5727zl18rvf6fg.png)

有用！

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