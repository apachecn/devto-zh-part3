# Sequelize 模型.更新

> 原文：<https://dev.to/abidemit/sequelize-modelupdate-nep>

在使用 sequelize 创建模型时，我需要更新我的数据库，所以我使用了 Model.findByIdAndUpdate，它返回了一个错误，后来我找到了使用
Model.update 更新数据库的正确方法({
title:'更新的标题'
}，
{
where:
{
id:1
}
})
我希望这能帮助到其他人...或者是他们的任何其他方式？我想知道谢谢。
下面举例:
[https://thepractical dev . S3 . amazonaws . com/I/xv3 dnnshqquidt D1 GS 9k 66 . png](https://thepracticaldev.s3.amazonaws.com/i/xv3dnnshqqitd1gs9k66.png)