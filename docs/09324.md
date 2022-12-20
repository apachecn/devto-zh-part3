# 使用 Powershell 实现 Microsoft Excel 自动化

> 原文：<https://dev.to/mrpowerscripts/microsoft-excel-automation-with-powershell-16l0>

你想让你的 Excel 技能更上一层楼吗？您知道使用 Excel 文件不需要 Excel 吗？有时候，以编程方式做事比指向和点击要简单一些。或者，您可能需要一次又一次地快速处理大量文件。使用 Microsoft Excel 实现 Powershell 自动化是可能的！

在此视频中，您将看到如何使用 Powershell 脚本更新 Excel 工作簿和工作表。

Excel 有一个 COM 接口，这是微软开发的技术(当然)。我从未读过关于 COM 的令人惊奇的解释，但基本上，它允许编程语言和 Windows 应用程序之间的通信。即使应用程序不是用编程语言构建的。Powershell 更像是一种脚本语言，但是我们仍然可以连接到 Excel COM 接口。

通过这个 COM 接口，Excel 公开了[Excel 对象模型](https://docs.microsoft.com/en-us/office/vba/api/overview/excel/object-model)。我已经把你链接到当前的文档，但是这个链接可能会在六个月后失效——因为微软。此外，为 Excel 对象模型显示的示例使用了一种叫做 Visual Basic for Applications 的东西。这是我 5 分钟前在寻找旧的通用 Excel 对象模型参考时学到的。显然它已经不存在了。又一次，因为微软。我希望我能提供更多的帮助和见解，但是-微软。不过，您可以使用下面的脚本来创建和更新工作簿和工作表。您还可以使用`Get-Member`浏览 excel 对象的属性，以了解更多可用选项。

你可以在 GitHub 上找到这个文件，或者查看下面的源代码。

```
 #None of this will work through an already open excel application
#You must open Excel and workbooks you want to manipulate via the COMobject

####################################################
############### Starting Excel #####################
####################################################

$Excel = New-Object -ComObject Excel.Application

$Excel.Visible = $true
$Excel.DisplayAlerts = $false

####################################################
############## Working with Workbooks ##############
####################################################

#Add a workbook to your current excel file
#You can add multiple workbooks with this method
$Excel.Workbooks.Add()

#Find all the workbooks in your excel file by name
$Excel.Workbooks | Select-Object -ExpandProperty name

#Activate a specifc workbook in your excel file
$Excel.Workbooks.Item(2).activate()
$Excel.Workbooks.Item("book1").activate()

#Acivate Random Workbook in Excel file
$Excel.Workbooks.Item((Get-Random -min 1 -Max ($Excel.Workbooks.Count+1))).activate()

#Open an existing workbook on your hardrive
$Excel.Workbooks.Open("$env:userprofile\desktop\mrpowerscripts.xlsx")

#close workbooks from an excel file
$Excel.Workbooks.Item(1).close()
$Excel.Workbooks.Item("MrPowerScripts.xlsx").close()

#Save workbooks to the hard drive
$Excel.Workbooks.item(1).SaveAs("$env:userprofile\Desktop\asdf.xlsx")

####################################################
########### Working with Worksheets ################
####################################################

#These bits of code will affect the active Workbook.

#Add a worksheet to your active workbook
$Excel.Worksheets.Add()

#Find all worksheets in your active workbook
$Excel.Worksheets | Select-Object -ExpandProperty name

#Change name of worksheet in workbook
$Excel.Worksheets.Item(1).name = "potato"
$Excel.Worksheets.Item("potato").name = "spud"

#Activate specific worksheet in workbook
$Excel.Worksheets.Item(2).activate()
$Excel.Worksheets.Item("sheet3").activate()

#Acivate Random Worksheet in Excel file
$Excel.Worksheets.Item((Get-Random -min 1 -Max ($Excel.Worksheets.Count+1))).activate()

#Delete worksheets from workbook
$Excel.Worksheets.Item(1).delete()
$Excel.Worksheets.Item("Sheet3").delete()

####################################################
########## Cleaning up the environment #############
####################################################

$Excel.Workbooks.Close()
$Excel.Quit()

#Check and you will see an excel process still exists after quitting
#Remove the excel process by piping it to stop-process
Get-Process excel | Stop-Process -Force

#Now we must release the $excel com object to ready it for garbage collection 
```