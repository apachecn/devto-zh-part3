# 转到库以读写 Microsoft Excel(XLSX)文件

> 原文：<https://dev.to/xuri/go-library-for-reading-and-writing-microsoft-excel-xlsx-files-57hi>

[![Excelize logo](img/87625897652fda39abe35859c99fdb8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f_rS1swf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xuri.me/exceliimg/excelize.svg)

[excelise](https://github.com/xuri/excelize)是一个用 pure Go 编写的库，它提供了一组允许你读写 XLAM / XLSM / XLSX / XLTM / XLTX 文件的函数。支持读取和写入由 Microsoft Excel 2007 和更高版本生成的电子表格文档。通过高兼容性支持复杂组件，并提供流式 API，用于从具有大量数据的工作表中生成或读取数据。

GitHub:[github.com/xuri/excelize](https://github.com/xuri/excelize)

## 基本用法

### 安装

```
go get github.com/xuri/excelize/v2 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 XLSX 文件

下面是一个创建 XLSX 文件的简单示例。

```
package main

import "github.com/xuri/excelize/v2"

func main() {
    f := excelize.NewFile()
    // Create a new sheet.
    index := f.NewSheet("Sheet2")
    // Set value of a cell.
    f.SetCellValue("Sheet2", "A2", "Hello world.")
    f.SetCellValue("Sheet1", "B2", 100)
    // Set active sheet of the workbook.
    f.SetActiveSheet(index)
    // Save xlsx file by the given path.
    if err := f.SaveAs("Book1.xlsx"); err != nil {
        println(err.Error())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 读取 XLSX 文件

下面构成了阅读 XLSX 文档的框架。

```
package main

import (
    "fmt"

    "github.com/xuri/excelize/v2"
)

func main() {
    f, err := excelize.OpenFile("Book1.xlsx")
    if err != nil {
        fmt.Println(err)
        return
    }
    defer func() {
        // Close the spreadsheet.
        if err := f.Close(); err != nil {
            fmt.Println(err)
        }
    }()
    // Get value from cell by given worksheet name and axis.
    cell, err := f.GetCellValue("Sheet1", "B2")
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println(cell)
    // Get all the rows in the Sheet1.
    rows, err := f.GetRows("Sheet1")
    if err != nil {
        fmt.Println(err)
        return
    }
    for _, row := range rows {
        for _, colCell := range row {
            fmt.Print(colCell, "\t")
        }
        fmt.Println()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 将图表添加到 XLSX 文件

使用 Excelize，图表的生成和管理就像几行代码一样简单。您可以根据工作表中的数据构建图表，或者生成工作表中没有任何数据的图表。

[![Add chart to Excel document](img/c53d67109146ba52683377f3947b35c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--puEGynHD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://xuri.me/excelize/img/base.png)

```
package main

import (
    "fmt"

    "github.com/xuri/excelize/v2"
)

func main() {
    categories := map[string]string{
        "A2": "Small", "A3": "Normal", "A4": "Large",
        "B1": "Apple", "C1": "Orange", "D1": "Pear"}
    values := map[string]int{
        "B2": 2, "C2": 3, "D2": 3, "B3": 5, "C3": 2, "D3": 4, "B4": 6, "C4": 7, "D4": 8}
    f := excelize.NewFile()
    for k, v := range categories {
        f.SetCellValue("Sheet1", k, v)
    }
    for k, v := range values {
        f.SetCellValue("Sheet1", k, v)
    }
    if err := f.AddChart("Sheet1", "E1", `{
        "type": "col3DClustered",
        "series": [
        {
            "name": "Sheet1!$A$2",
            "categories": "Sheet1!$B$1:$D$1",
            "values": "Sheet1!$B$2:$D$2"
        },
        {
            "name": "Sheet1!$A$3",
            "categories": "Sheet1!$B$1:$D$1",
            "values": "Sheet1!$B$3:$D$3"
        },
        {
            "name": "Sheet1!$A$4",
            "categories": "Sheet1!$B$1:$D$1",
            "values": "Sheet1!$B$4:$D$4"
        }],
        "title":
        {
            "name": "Fruit 3D Clustered Column Chart"
        }
    }`); err != nil {
        fmt.Println(err)
        return
    }
    // Save spreadsheet by the given path.
    if err := f.SaveAs("Book1.xlsx"); err != nil {
        fmt.Println(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 添加图片到 XLSX 文件

```
package main

import (
    "fmt"
    _ "image/gif"
    _ "image/jpeg"
    _ "image/png"

    "github.com/xuri/excelize/v2"
)

func main() {
    f, err := excelize.OpenFile("Book1.xlsx")
    if err != nil {
        fmt.Println(err)
        return
    }
    defer func() {
        // Close the spreadsheet.
        if err := f.Close(); err != nil {
            fmt.Println(err)
        }
    }()
    // Insert a picture.
    if err := f.AddPicture("Sheet1", "A2", "image.png", ""); err != nil {
        fmt.Println(err)
    }
    // Insert a picture to worksheet with scaling.
    if err := f.AddPicture("Sheet1", "D2", "image.jpg",
        `{"x_scale": 0.5, "y_scale": 0.5}`); err != nil {
        fmt.Println(err)
    }
    // Insert a picture offset in the cell with printing support.
    if err := f.AddPicture("Sheet1", "H2", "image.gif", `{
        "x_offset": 15,
        "y_offset": 10,
        "print_obj": true,
        "lock_aspect_ratio": false,
        "locked": false
    }`); err != nil {
        fmt.Println(err)
    }
    // Save the spreadsheet with the origin path.
    if err = f.Save(); err != nil {
        fmt.Println(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode