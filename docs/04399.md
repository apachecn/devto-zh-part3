# 带 p5 的 Op Art

> 原文：<https://dev.to/mouseannoying/op-art-with-p5-3pi2>

我爸爸和我都喜欢 Op art，他最近转发了一封来自 Tumblr 的电子邮件，上面有 T2 的链接。这激起了我的兴趣，因为我在业余时间经常玩 p5，你会看到 [前](http://drmsite.blogspot.com/2019/03/p5-tail.html) [帖子](http://drmsite.blogspot.com/2019/03/p5-explosions.html)中的[。](http://drmsite.blogspot.com/2019/04/p5-invader.html)

因此，我得到了它，但时钟可能有一个问题，在掩盖组成图像的方块。稍微明智地摆弄一下 Gimp T1 意味着我知道这个图形将由多个方块组成。这些方块要么以黑色背景开始，要么以黄色背景开始，经过一段时间后它们会发生变化。为了解决蒙版的问题，我选择使用 [createGraphics()](https://p5js.org/reference/#/p5/createGraphics) ，然后使用 [image()](https://p5js.org/reference/#/p5/image) 函数将图形嵌入到图片的主体中。虽然我最终犯了一个简单的错误，但是[堆栈溢出拯救了我](https://stackoverflow.com/questions/55971705/issues-with-multiple-p5-image)，我对[的结果](https://annoyingmouse.js.org/WireFrameJS/000-MISCELLANEOUS/OPART/)非常满意。

代码也在 GitHub 上，作为一个单独的文件在 [JSFiddle](https://jsfiddle.net/annoyingmouse/Lbs9v8f4/) 上:

[https://jsfiddle.net/annoyingmouse/Lbs9v8f4/embedded/result//dark](https://jsfiddle.net/annoyingmouse/Lbs9v8f4/embedded/result//dark)

```
class Tile {
  constructor(p5, x, y, dimension, row, delay) {
    this.p5 = p5;
    this.x = x;
    this.y = y;
    this.dimension = dimension;
    this.delay = delay;
    this.onFirst = row % 2;
    this.on = p5.color(255, 184, 0);
    this.off = p5.color(26, 17, 16);
    this.diameter = Math.sqrt(Math.pow(dimension, 2) * 2)
    this.pg = p5.createGraphics(dimension, dimension)
    this.pg.noStroke();
  }
  update() {
    if (this.delay === 0) {
      if (this.diameter < 0) {
        this.diameter = Math.sqrt(Math.pow(this.dimension, 2) * 2);
        this.onFirst = !this.onFirst;
        this.delay = 120;
      } else {
        this.diameter -= 1;
      }
    } else {
      this.delay -= 1;
    }
    return this.draw();
  }
  draw() {
    this.pg.fill(this.onFirst ? this.off : this.on);
    this.pg.rect(0, 0, this.dimension, this.dimension);
    this.pg.fill(this.onFirst ? this.on : this.off);
    this.pg.circle(this.dimension / 2, this.dimension / 2, this.diameter);
    return this.pg;
  }
}

new p5(p5 => {
  const rows = 14;
  const columns = 14;
  const dimension = 40;
  const framerate = 20;
  const tiles = [];
  const delay = 30;
  p5.setup = () => {
    p5.createCanvas(columns * dimension, rows * dimension);
    for (let row = 0; row < rows; row++) {
      for (let column = 0; column < columns; column++) {
        tiles.push(new Tile(
          p5,
          column * dimension,
          row * dimension,
          dimension,
          row,
          column % 2 ? ((rows - row) * 5) + 80 : row * 5
        ));
      }
    }
  };
  p5.draw = () => {
    p5.background(200);
    tiles.forEach((tile) => {
      p5.image(tile.update(), tile.x, tile.y);
    });
  };
}); 
```