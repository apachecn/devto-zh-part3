# 在构建时创建 spec flow ' feature . cs '

> 原文：<https://dev.to/chrisotto/create-specflow-feature-cs-at-build-time-23ln>

## 在编译时改变特征文件生成`~.feature.cs`

每当在解决方案中创建新的 Specflow 文件时，都会在自定义工具属性中使用 SpecFlowSingleFileGenerator 规范自动创建该文件。这个文件生成器在保存时而不是在构建过程中创建后台文件，按照以下步骤进行更改:

### 先决条件

要进行更改，您需要向 Visual Studio 实例添加一个扩展(如果您还没有的话)。

*   在 Visual Studio 中，转到扩展管理扩展
*   选择在线并搜索“文件嵌套”
*   安装“文件嵌套”扩展并关闭 Visual Studio，一旦关闭，安装将开始
*   当 VS 关闭后出现提示时，从弹出窗口中选择“修改”

### 做出改变

安装了“文件嵌套”扩展后，我们现在可以更改为特征文件创建~.feature.cs 的方式。

*   修改现有属性
    *   在解决方案资源管理器中，右键单击特征文件并选择属性
    *   “属性”窗口应该在解决方案资源管理器下面弹出
    *   在自定义工具属性中，删除“SpecFlowSingleFileGenerator ”,将自定义工具属性留空
*   创建文件依赖关系
    *   移除自定义工具后，构建解决方案
    *   构建完成后，确保您选择了“显示所有文件”,并且您应该在解决方案资源管理器中看到您的功能文件的“~.feature.cs”文件
    *   如果您没有看到它，并且您已经启用了“显示所有文件”，请尝试刷新解决方案资源管理器，它应该会填充
    *   右键点击`~.feature.cs`文件选择‘文件嵌套’
    *   将文件嵌套在各自的特征文件下