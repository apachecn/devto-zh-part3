# 为 3D 模型建立正确的前向方向

> 原文：<https://dev.to/webdva/establishing-the-correct-forward-facing-direction-for-a-3d-model-5a91>

*我正在分享第一次制作 3D 游戏时在学到的[课。](https://webdva.github.io/establishing-the-correct-forward-facing-direction-for-a-3d-model/)*

与 2D 环境中的 2D 子画面不同，3D 环境中的 3D 模型具有包括所谓的面向前的方向的方向。

在下面的图片中，网格的面向前的方向是不正确的，因为网格的字符正面没有面向预期的方向。

[![Undesired mesh orientation](img/556eae120099e76d616db13357e0351a.png "Mesh facing the wrong direction")](https://res.cloudinary.com/practicaldev/image/fetch/s--fJahnTky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ouy6yy0kvlo71zfwcw6.PNG)

下一张图显示了网格的正确方向，即 3D 角色的背面朝向相机的前向方向。

[![Desired mesh orientation](img/245620524cd9b885c5f96c6d77cf2b76.png "The correct direction to face in")](https://res.cloudinary.com/practicaldev/image/fetch/s--HmSnRXiA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4mjvl8w42y2ghjg801q1.PNG)

以下指南揭示了为 3D 网格建立正确的前向方向的正确步骤，特别是针对 Babylon.js 游戏开发框架。

# Blender 3D 建模实用程序中的正确程序

3D 模型必须面向的轴是 y 轴，以便在外部应用程序中具有正确的面向前的方向，并且必须在 y 轴的正方向。

[![Correct orientation in Blender](img/9a668d2d5f9443ab03c4128220d0e5a4.png "Mesh facing the front direction")](https://res.cloudinary.com/practicaldev/image/fetch/s--NGMfWo1r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xtn59nl8doj00ojqyxuz.PNG)

位置、旋转和比例变换将应用于导出到 Babylon.js 应用程序。