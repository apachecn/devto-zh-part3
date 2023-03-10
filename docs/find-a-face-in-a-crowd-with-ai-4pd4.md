# 用 AI 在人群中找到一张脸

> 原文：<https://dev.to/musebe/find-a-face-in-a-crowd-with-ai-4pd4>

[![Alt text of image](img/8ff3e5e8782019c2e18e6a6722d17d54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XjK-taPM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/thf8yekp1xlefar1bdg4.gif) 
随着技术继续在我们的日常生活中占据主导地位，面部识别变得越来越普遍，为医疗保健、支付、犯罪识别和广告等各个领域的系统和设备提供了安全性。作为所谓的生物识别系统的一个突出特征，面部识别检查人的身体特征，以唯一地将一个人与其他人区分开来。具体来说，这种能力接收数据，通常是来自未知人的图像，分析数据，并将图像与已知人脸数据库中的图像进行比较。该过程包括三个步骤:检测、面纹创建和验证或识别。

验证后，系统会存储一个面纹，很像指纹，包含一组特征，如面部特征的相对位置:眼睛、眉毛、鼻子。当它们组合在一起时，它们可以唯一地识别一个人的脸。

这篇文章描述了如何在操作和上传图像到 Cloudinary 存储器后，使用 [Cloudinary](https://twitter.com/smashingmag/status/1107677212218703880) 管理和优化图像。要了解如何创建、读取和删除图像，请参阅文章 [*搭乘媒体快车*](https://dev.to/musebe/get-on-board-with-the-media-express-5h1k) 。

## 流程

遵循本节中的过程。

### 为 Face.js 安装相关软件

首先，在你的机器上安装 Face.js 进行面部识别所依赖的软件。

对于 Linux 和 iOS，安装以下软件:

*   **cmake:** 键入命令`sudo apt-get install cmake`
*   * * libx 11(OSX 的 XQuartz)用于 dlib GUI:**键入命令`sudo apt-get install libpng-dev`
*   **读取图像的 libpng:**输入命令`sudo apt-get install libpng-dev`

### 安装和配置 Cloudinary

为了从其存储中读取图像，Cloudinary 通过 npm 提供了一个出色的 Node.js 集成库。通过键入以下命令安装 Cloudinary:

`npm install cloudinary`

将 Node.js 类添加到代码中。类型:

`const cloudinary = require(‘cloudinary’)`

要使 Cloudinary 与您的帐户配合使用，请使用您的云名称、API 密钥和 API 秘密来配置 Cloudinary，如下所示:

`cloudinary.config({
cloud_name: 'sample',
api_key: '874837483274837',
api_secret: 'a676b67565c6767a6767d6767f676fe1'
});`

### 设置 API

要从 Cloudinary 服务器检索所有经过训练的图像，请利用 Cloudinary 的搜索 API。使用`socket.emit()`将图像发送到前端，如下所示:

`cloudinary.v2.search
.expression('folder=face_recognition')
.execute().then(result=>{
socket.emit("images", result.resources);
});`

接下来，用`socket.on()`设置前端接收图像的过程:

`//Wait to receive images
socket.on("images", function(images) {
for(image of images){
//Add images to HTML DOM
}
//Add event listener
let domImgs = document.querySelectorAll("#imgCont img")
for(var i = 0; i < domImgs.length; i++) {
domImgs[i].addEventListener('click', function(event){
clickedSrc = event.target.src
UploadToNodeServer(event.target.src)
}, false);
}
}
});`

上面的代码指定，在图像点击时，Cloudinary 将图像源保存在一个全局变量中，触发`UploadToNodeServer`函数。见下文。

`function UploadToNodeServer(imgSrc) {
socket.emit("imageUpload", {
image: false,
src: imgSrc,
});
}
$("#upldbtn").addClass("disabled");
}`

`UploadToNodeServer`函数将图像源发送到节点后端，如下所示:

`socket.on("imageUpload", function(info) {
if(!info.image){
let base64Data;
download(info.src, './tmp/uploads/out.png', function(){
if (info.format === "png")
console.log("Download success")
socket.emit("DownloadSuccess", "true");
//return success
});
}
});`

之后，下载图像并将其发送回前端:

`//下载图像的功能

const download = function(uri，文件名，回调){
request.head(uri，function(err，res，body){
console . log(' content-type:'，RES . headers[' content-type '])；
console.log('content-length:'，RES . headers[' content-length '])；
请求(uri)。管道(fs.createWriteStream(文件名))。on(“关闭”，回调)；
})；
} `

单击 Recognize 按钮触发对 Recognize route 的 AJAX 调用，该调用通过`FaceRecognizer`类识别图像。

最后，在新的浏览器窗口中发送响应。参见下面的代码。

` router.get('/recognize '，function(req，res){
const recognizer = fr。face recognizer()；
/*
加载我们之前保存的列车数据
*/
const model state = require('../model . JSON’)；
recognizer . load(model state)；
/*
从图像中检测人脸
*/
const image = fr . loadimage('。/tmp/uploads/out . png ')；
常量检测器= fr。face detector()；
const targetSize = 150；
const face image = detector . detect faces(image，targetSize)；

/*
在面
上画矩形，为每个面写一个预测
*/
const faceRects = detector . locate faces(image)。map(mmodRect =>mmodRect . rect)；
const faces = detector . getfacesfromlocations(image，faceRects，150)；

if(face rects . length){
const win = new fr。ImageWindow()；
win.setImage(图像)；
faceRects.forEach((rect，I)=>{
win . add overlay(rect)；
const predict = recognizer . predict Best(faces[I]，0.69)；
win.addOverlay(rect，`${predict.className} (${predict.distance})`)；
})；
//fr . hitentertocontinue()；
}

/*
将人脸的输出发送到 HTML 页面
*/
if(face image . length){
const predictions = recognizer . predict(face image[0])；
res.send(预测)；
}
否则{
res.status(400)。json({msg:'无法检测人脸，请尝试另一张
图片' })；
}
})；`

## 结论

目前，苹果等主要科技公司对面部识别技术非常感兴趣，并采用了这项技术。人工智能初创公司也正在成为独角兽。毫无疑问，在不久的将来，面部识别将在社会中发挥越来越重要的作用。尽管存在隐私方面的担忧，但面部识别让我们的街道、家庭、银行和商店更安全，也更高效。

关于这篇文章中描述的面部识别项目的细节，请参见 [GitHub 代码](https://github.com/musebe/Face-Recognition)。欢迎投稿和建议。