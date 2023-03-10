# FEATool Multiphysics 1.10 - MATLAB 物理仿真工具箱

> 原文：<https://dev.to/featool/featool-multiphysics-1-10-matlab-physics-simulation-toolbox-38cj>

# [T1】FEA tool Multiphysics 1.10](#featool-multiphysics%E2%84%A2-110)

[![FEATool Multiphysics MATLAB Physics Simulation Toolbox](img/8333fbd43234978e7a2d45096d678a21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gp0Gjvyn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.featool.cimg/banner-featool-multiphysics-matlab-fem-toolbox.jpg)

FEATool Multiphysics 版本 1.10 现已发布，并作为专用的 MATLAB 插件和独立工具箱
提供
(直接从 MATLAB 插件
工具栏点击即可获得)。FEATool
Multiphysics 1.10 提供了以下新功能。

## 自动化多物理教程

[![FEATool Multiphysics - Automated Physics Simulation Tutorials](img/a4cad33a0801b076d877dec4637bde5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--srvo__ST--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.featool.cimg/featool-multiphysics-automated-simulation-tutorials.jpg)

FEATool Multiphysics GUI 已经过全面检查和改进，具有
[内置和自动化教程](///doc/tutorials.html)，以便更容易开始使用多物理建模
。通过从**文件>中选择任意示例
模型示例和教程...**菜单启动，
自动回放设置和运行模型所需的步骤。目前大约有 35 个教学模型可用于不同的物理类型，将来会有更多的模型推出。

结合自动化教程，还引入了新的*有限元
脚本*模型文件格式。这种格式允许
整个 GUI 建模会话和工作流被保存，随后
被同样地重放，这对于教学和教程来说是理想的。在
中添加了 MATLAB 脚本*(。m)* 文件格式，*有限元
脚本(。fes)* 也是一种简单的文本文件格式，可以被
检查、编辑和置于版本控制之下。总之，用户现在可以用二进制文件*保存和导出模型。fea)* 、MATLAB m-script 和
有限元 GUI 脚本格式，用于完全控制模拟
模型数据和再现性

## 特征值求解器

除了静态、非线性和依赖于时间的内置
解算器，FEATool 1.10 还引入了一个新的特征值解算器，可以从 GUI 和 MATLAB API 访问和使用该解算器
作为
[solveeig](https://www.featool.com/doc/solveeig_8m.html) 函数。特征值解算器例如用于[房间的共振频率([https://www . FEA tool . com/model-showcase/07 _ Classic _ PDE _ 03 _ resonance 1](https://www.featool.com/model-showcase/07_Classic_PDE_03_resonance1))、[鼓膜的振动](https://www.featool.com/model%20showcase/07_Classic_PDE_02_circular_drum1)、[悬臂梁的振动](https://www.featool.com/model-showcase/03_Structural_Mechanics_01_euler_beam1)和[空心圆柱体的振动](https://www.featool.com/model-showcase/03_Structural_Mechanics_04_hollow_cylinder1)教程模型。

## 边缘约束

除了 Dirichlet 和 Neumann 边界条件和点
约束，现在还可以使用完整的 3D 边缘约束。这个新的
特征例如用于厚板
NAFEMS 基准示例的[应力分析。](https://www.featool.com/model-showcase/03_Structural_Mechanics_05_thick_plate1)

## 新增后处理功能

除了通过社交
网络共享模型和模拟结果之外，FEATool GUI 现在还具有专用的一键式按钮，用于
将模拟数据导出为 Plotly 和 ParaView Glance 网络图。点击
链接观看演示

 [**FEATool ParaView Glance and Plotly Web 情节示例**](%20%20https://www.featool.com/web-plots/%0A) 

模拟数据现在也可以手动导出到常规的 [CSV](https://www.featool.com/doc/export__csv_8m.html) 、 [ParaView(。vtk](https://www.featool.com/doc/impexp__vtk_8m.html) [/。vtp)](///doc/impexp__vtp_8m.html) 和[通用网格查看器 GMV](///doc/impexp__gmv_8m.html) 格式，使用 *Post* 菜单中的*导出*选项或相应的导入/导出 CLI 功能。

[![FEATool Multiphysics - Extrude and Revolve Postprocessing Examples](img/1a83abd5d393458622b516cebe00f4b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8vzp2WpI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.featool.cimg/featool-multiphysics-postextrude-postrevolve-examples.jpg)

对后处理功能的进一步改进包括一个[变形图选项](https://www.featool.com/doc/post.html#post_deformplot),用户可以通过它指定一个域来变形和扭曲结果图，例如，它可以用来查看一个结构是如何被载荷变形的。此外，两个新的命令行功能[后挤压](https://www.featool.com/doc/postextrude_8m.html)和[后旋转](https://www.featool.com/doc/postrevolve_8m.html)可用于挤压和旋转 2D 和轴对称模拟数据，以创建相应的三维可视化。

## 附加新功能

*   在 STL CAD 文件导入期间支持[多个 STL 部分和文件。](https://www.featool.com/doc/geom.html#geom_cad_import_export)
*   制作几何体对象副本的功能(可通过*几何体*模式下的*变换几何体对象*工具获得，或通过[copy _ Geometry _ object](https://www.featool.com/doc/copy__geometry__object_8m.html)MATLAB 函数在命令行获得)。
*   改进和更准确地评估网格和网格点中的一般表达式。
*   外部网格生成器专用的 [GitHub 库](https://github.com/precise-simulation/external-grid-generators),为 64 位 Windows、Linux 和 Mac OSX 系统预建了静态二进制文件。(由于软件许可证不兼容，不能与 FEATool 一起分发，但如果有互联网连接，可以在请求时自动下载。)