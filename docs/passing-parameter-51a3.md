# 传递参数

> 原文：<https://dev.to/danielfrugoni/passing-parameter-51a3>

* * *

标题:
描述:以图像形式传递参数，角度 7
标签:
[![](img/44e7f88c8a4599106ecc2d5303054ef8.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ibNjkmeX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/%7B%7BtmpImage%7D%7D) (Opc A)
(Opc B)

选项 A: Works
选项 B: NO
{
@Component({
选择器:' upload-imagen '，
templateUrl:'。/imagen.component.html '，
styleUrls: ['。/imagen.component.scss']，
})

导出类 imageUploadComponent 实现 on init {
@ Input()imagen:any；

constructor(){
console . log(' Foto:'+this . imagen)显示未定义的
}
}
无法找到解决方案

## 非常非常欢迎帮助

额外:tmpImage 不是一个路径，是来自 json 的图像，我可以看到表单中的所有信息。所以图像可能很大，而参数有一个极限大小？