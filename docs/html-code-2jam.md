# html 代码

> 原文：<https://dev.to/forgetfulsktbro/html-code-2jam>

基础

图标

文本

alert("感谢所有喜欢这段代码的人，这是我最喜欢的代码之一，我很高兴其他人也喜欢它！这也是我拥有的最高投票代码！'")

——————————————————————————————

@导入网址([https://fonts.googleapis.com/css?family=Lato:400,900](https://fonts.googleapis.com/css?family=Lato:400,900))；
@导入网址([https://cdnjs . cloud flare . com/Ajax/libs/font-awesome/4 . 6 . 3/CSS/font-awesome . min . CSS](https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.6.3/css/font-awesome.min.css))；

*   { font-family:inherit；-webkit-font-smoothing:抗锯齿；-WebKit-用户选择:无；-WebKit-tap-highlight-color:rgba(0，0，0，0)；}

html {
字体大小:62.5%；
font-family: 'Lato '，无衬线；
}

正文{
背景:# 243142；
填充:20px 0 0
}

p {
颜色:# b 8 c 7 db；
text-align:居中；
字号:1.5 雷姆；
边距:30px auto 12px
}

。翻转开关{
显示:block
身高:35px
宽度:62px
背景:# 182029；
填充:3px 0 0
边距:0 自动；
视角:50px
-WebKit-视角:50px
-moz-视角:50px
边框-半径:50px
-WebKit-border-radius:50px；
-moz-border-radius:50px；
}
。翻转开关输入{
不透明度:0；
位置:绝对；
top:0；
右:100%；
宽度:1px
身高:1px
}
。翻转开关标签{
显示:block
位置:相对；
身高:32px
宽度:56px
大纲:无；
边距:0 自动；
-WebKit-外观:无；
背景:无；
边框:无；
变换样式:preserve-3d；
-WebKit-transform-style:preserve-3d；
-moz-transform-style:preserve-3d；
border-radius:继承；
-WebKit-border-radius:inherit；
-moz-border-radius:继承；
动画:不勾选 0.6s 渐出；
-webkit-animation:不勾选 0.6s 缓出；
-moz-animation:不勾选 0.6s 缓出；
盒影:无；
-webkit-box-shadow:无；
-moz-box-shadow:无；
}
。翻转开关标签:之前，。翻转开关标签:{
内容之后:' '；
位置:绝对；
top:0；
左:0；
身高:继承；
宽度:继承；
背面-可见性:隐藏；
-WebKit-back face-visibility:隐藏；
-moz-backface-visibility:隐藏；
border-radius:继承；
-WebKit-border-radius:inherit；
-moz-border-radius:继承；
文本对齐:居中；
}
。翻转开关标签:在{
z-index: 2 之前；
变换:rotateY(0 deg)；
-WebKit-transform:rotateY(0 deg)；
-moz-transform:rotateY(0 deg)；
背景:# E65757
}
。翻转开关标签:{
变换后:旋转(180 度)；
-WebKit-transform:rotateY(180 deg)；
-moz-transform:rotateY(180 deg)；
背景:# 77E371
}
。翻转开关输入:checked+label {
transform:rotateY(180 deg)；
-WebKit-transform:rotateY(180 deg)；
-moz-transform:rotateY(180 deg)；
动画:勾选 0.6s 缓出；
-webkit-animation:检查 0.6s 缓出；
-moz-animation:检查 0.6s 缓出；
}
。翻转开关。翻转开关图标标签:之前，。flip-switch . flip-switch-icon label:after {
font-family:' font awesome '；
颜色:白色；
font-size:2 rem；
行高:32px
}
。flip-switch . flip-switch-icon label:before {
内容:' \ f00d '；
}
。flip-switch . flip-switch-icon label:after {
内容:' \ f00c '；
}
。flip-switch . flip-switch-文本标签:之前，。flip-switch . flip-switch-text label:after {
颜色:白色；
行高:32px
font-weight:900；
font-size:1.3 rem；
}
。flip-switch . flip-switch-text label:before {
content:' OFF '；
}
。flip-switch . flip-switch-text label:after {
content:' ON '；
}

@关键帧检查{
0% {
transform:rotateY(0 deg)；
}
50% {
变换:rotateY(195 deg)；
}
75% {
变换:旋转(165 deg)；
}
100% {
变换:旋转(180 deg)；
}
}
@-WebKit-关键帧检查{
0% {
-WebKit-transform:rotateY(0 deg)；
}
50% {
-WebKit-transform:rotateY(195 deg)；
}
75% {
-WebKit-transform:rotateY(165 deg)；
}
100% {
-WebKit-transform:rotateY(180 deg)；
}
}
@-moz-关键帧检查{
0% {
-moz-transform:rotateY(0 deg)；
}
50% {
-moz-transform:rotateY(195 deg)；
}
75% {
-moz-transform:rotateY(165 deg)；
}
100% {
-moz-transform:rotateY(180 deg)；
}
}
@关键帧取消选中{
0% {
变换:rotateY(180 deg)；
}
50% {
变换:rotateY(-15 deg)；
}
75% {
变换:rotateY(15 度)；
}
100% {
变换:rotateY(0 deg)；
}
}
@-WebKit-key frames uncheck {
0% {
-WebKit-transform:rotateY(180 deg)；
}
50% {
-WebKit-transform:rotateY(-15 deg)；
}
75% {
-WebKit-transform:rotateY(15 deg)；
}
100% {
-WebKit-transform:rotateY(0 deg)；
}
}
@-moz-key frames uncheck {
0% {
-moz-transform:rotateY(180 deg)；
}
50% {
-moz-transform:rotateY(-15 deg)；
}
75% {
-moz-transform:rotateY(15 deg)；
}
100% {
-moz-transform:rotateY(0 deg)；
}
}

————————————————————————————————————

代码照片:https://thepractical dev . S3 . Amazon ws . com/I/z5f9j 8u9gp 5 oxd 70 rj . pngT2]T3]https://thepractical dev . S3 . Amazon ws . com/I/51165 lo 7 msoi 6 cy 9 MC . pngt5]