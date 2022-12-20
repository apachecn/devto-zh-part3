# rgba2rgb

> 原文：<https://dev.to/mouseannoying/rgba2rgb-1ac4>

几天前我遇到了一个可爱的小挑战，加上我被邀请参加代码帝国的第一次 Alpha 测试，这让我很兴奋。这让我产生了下面的代码:

```
const rgba2rgb = rgba => "#" + rgba.match(/.{1,2}/g)
  .map((c, i, a) => (i !== a.length -1)
    ? parseInt(c, 16)
    : parseInt(c, 16) / 255)
  .map((c, i, a) => (i !== a.length -1)
    ? ~~((1 - a[3]) * 255 + a[3] * c)
    : null)
  .reverse()
  .slice(1)
  .reverse()
  .map(c => c.toString(16))
  .join(""); 
```

我们需要将一种 [RGBA](https://en.wikipedia.org/wiki/RGBA_color_space) 颜色转换为 [RGB](https://en.wikipedia.org/wiki/RGB_color_space) ，假设原始颜色在白色背景之上。数学和色彩理论有点混乱，我经历了的任意[数字](https://stackoverflow.com/questions/11614940/convert-rgba-to-rgb-taking-background-into-consideration) [。这个结果让我有点沾沾自喜，因为它很小，我想它还可以改进。](https://stackoverflow.com/questions/2049230/convert-rgba-color-to-rgb)

开始吧！

**编辑**

所以我注意到上面的内容并没有我想象的那么精彩，所以我在简化后更新了它:

```
const rgba2rgb = function(rgba){
  const coloursAsBase16 = rgba.match(/.{1,2}/g); 
  console.log("coloursAsBase16", coloursAsBase16);
  const coloursAsBase10 = coloursAsBase16.map(function(c, i, a){
    if(i !== a.length - 1) {
      return parseInt(c, 16) // Value between 1 - 255
    } else {
      return parseInt(c, 16) / 255 // Value between 0 - 1}
    }); 
  console.log("coloursAsBase10", coloursAsBase10);
  const coloursWithAlpha = coloursAsBase10.reduce(function(t, c, i, a) {
    if(i !== a.length - 1) {
      return t.concat([parseInt(((1 - a[3]) * 255 + a[3] * c), 10)]);
    } else {
      return t;
    }
  }, []) 
  console.log("coloursWithAlpha", coloursWithAlpha);
  const coloursChangedToBase16 = coloursWithAlpha.map(function(c){
    return c.toString(16)
  }); 
  console.log("coloursChangedToBase16", coloursChangedToBase16);
  return "#" + coloursChangedToBase16.join("");
} 
```

这导致了这个一行程序:

```
const rgba2rgb = rgba => "#" + rgba.match(/.{1,2}/g)
  .map((c, i, a) => (i !== a.length - 1)
    ? parseInt(c, 16)
    : parseInt(c, 16) / 255)
  .reduce((t, c, i, a) => (i !== a.length - 1)
    ? t.concat([parseInt(((1 - a[3]) * 255 + a[3] * c), 10)])
    : t, [])
  .map(c => c.toString(16))
  .join(""); 
```