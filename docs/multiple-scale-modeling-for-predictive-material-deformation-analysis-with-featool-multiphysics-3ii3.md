# 用 FEATool Multiphysics 预测材料变形分析的多尺度建模

> 原文：<https://dev.to/featool/multiple-scale-modeling-for-predictive-material-deformation-analysis-with-featool-multiphysics-3ii3>

下面介绍了使用 FEATool Multiphysics 研究单轴拉伸下韧带的拉伸和变形的多尺度建模和模拟研究。该研究展示了学生和非专家如何使用 FEATool 模拟软件进行模拟，并以最少的努力和时间快速获得相关结果。本研究是在马萨诸塞大学阿默斯特分校教授钱泳·陈的指导下，由学生雷切尔·阿罗诺、亚伦·达·席尔瓦、罗斯·丹尼斯、阿卜杜勒·卡德尔·热拉尔多、迪安·卡萨洛斯、梅丽莎·西奇和理查德·图雷特合作完成的[1]。

[![Multiple Scale Modeling for Predictive Material Deformation Analysis](img/930a14262e5347c735c74b0d12922b42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jY3Kbmf8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.featool.cimg/featool-multiphysics-multiple-scale-modeling-for-predictive-material-deformation-analysis.jpg)

## 总结

材料变形和应力应变是与工业和研究导向的
材料科学相关的
数学建模的活跃领域。在这些模型中考虑材料
属性的变化是很重要的。研究了
包含不均匀性的多尺度模型，并创建和测试了
解决这一需求的建模框架。在微观尺度上结合
材料属性的变化导致在类似
载荷下材料变形的
显著不同的预测。通过
对代表性体积元素(RVE)中的应力进行平均，说明了材料特性的变化。

## 引用

[1] Aronow R. et al.
[*预测材料变形分析的多尺度建模*](https://www.math.umass.edu/sites/www.math.umass.edu/files/newsletters/2018_umass_math_newsletter_210.pdf) ，
数学与统计通讯，麻省大学
阿姆赫斯特分校，第 33 卷，第 4-6 页，2017-2018。