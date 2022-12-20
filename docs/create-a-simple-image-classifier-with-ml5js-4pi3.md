# 用 ml5js 创建一个简单的图像分类器

> 原文：<https://dev.to/0xkoji/create-a-simple-image-classifier-with-ml5js-4pi3>

在这种情况下，我将向您展示如何使用 p5js 和 ml5js 创建一个简单的图像分类器应用程序。

一、p5js 和 ml5js 是什么？

我会说是为 js 处理(其实是有 processing.js)。

[https://p5js.org/](https://p5js.org/)

在 ITP 这里，大多数没有任何编程经验的学生都开始使用 p5js 学习编码。

ml5js 是 tensorflowjs 的一个包装器，所以这允许我们很容易地使用 tensorflowjs，但是这意味着我们不能像 tensorflowjs 一样用 ml5js 做所有的事情。
[https://github.com/ml5js/ml5-library](https://github.com/ml5js/ml5-library)

`index.html`
非常简单的 html。只需加载库。
ml5js 方面，最近更新了，但是我还没有更新代码，所以这个 app 需要使用 v0.2.1\.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.7.3/p5.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.7.3/addons/p5.dom.min.js"></script>
    <script src="./p5.speech.js"></script>
    <script src="https://unpkg.com/ml5@0.2.1/dist/ml5.min.js" type="text/javascript"></script>
    img_rec
</head>
<body>
    <script src="./sketch.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

`sketch.js`
这款 app 正在使用 MobileNet 对物体进行分类。

```
let classifier;
let video;
let status = '';
let results = '';
const resultsNum = 5;
const voice = new p5.Speech();

function setup() {
    createCanvas(windowWidth, windowHeight);
    video = createCapture(VIDEO);
    classifier = ml5.imageClassifier('MobileNet',  video, modelReady); status = 'loading...';
}
function draw () {
    image(video, 0, 0, width, height);
    fill(255, 0, 0);
    textSize(24);
    text(status, 20, 30);
}

const modelReady = () => {
    status = 'loaded model!';
    classifier.predict(video, gotResult);
}

const gotResult = (err, results) => {
    if (err) {
        console.error(err);
        status = err;
    }
    // console.log(`results: ${results}`);
    status = `class: ${results[0].className}, accuracy: ${results[0].probability.toFixed(4)} \n`;    
    voice.speak(`${results[0].className}`);
    classifier.predict(video, gotResult);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 演示

实际上，这个应用程序告诉你他通过摄像头检测到了什么。
[https://thepractical dev . S3 . Amazon AWS . com/I/CHM bw 4 svkmdcmdyxsyb 6 . gif](https://thepracticaldev.s3.amazonaws.com/i/chmbw4svkmdcmdyxsyb6.gif)