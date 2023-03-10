# 用 C#将数据导出到 Excel 的 6 种简单方法

> 原文：<https://dev.to/syncfusion/6-easy-ways-to-export-data-to-excel-in-c-pdh>

[**Syncfusion Excel (XlsIO)库**](https://www.syncfusion.com/excel-framework/net) 是一个. NET Excel 库，允许用户以非常简单和容易的方式将数据从各种数据源(如数据表、数组、对象集合、数据库、CSV/TSV 和 Microsoft Grid 控件)导出到 C#和 VB.NET 的 Excel 中。将数据导出到 Excel 有助于以更易于理解的方式可视化数据。此功能有助于生成财务报告、银行对账单和发票，同时还允许过滤大型数据、验证数据、格式化数据等。

Essential XlsIO 提供了以下将数据导出到 Excel 的方法:

*   数据表到 Excel
*   要 Excel 的对象集合
*   数据库到 Excel
*   Microsoft 网格控件到 Excel
*   Excel 数组
*   CSV 到 Excel

在这篇博客中，我们将看看这些方法，以及如何执行它们。

## 1。从数据表导出到 Excel

来自 ADO.NET 对象(如 datatable、datacolumn 和 dataview)的数据可以导出到 Excel 工作表中。通过识别列类型或单元格值类型、超链接和大型数据集，可以在几秒钟内完成作为列标题的导出。

**将数据表导出到 Excel** 工作表可以通过[导入数据表](https://help.syncfusion.com/file-formats/xlsio/working-with-data#import-data-from-datatable)方法实现。下面的代码示例显示了如何将雇员详细信息的数据表导出到 Excel 工作表。

```
using (ExcelEngine excelEngine = new ExcelEngine())
{
    IApplication application = excelEngine.Excel;
    application.DefaultVersion = ExcelVersion.Excel2016;

    //Create a new workbook
    IWorkbook workbook = application.Workbooks.Create(1);
    IWorksheet sheet = workbook.Worksheets[0];

    //Create a dataset from XML file
    DataSet customersDataSet = new DataSet();
    customersDataSet.ReadXml(Path.GetFullPath(@"../../Data/Employees.xml"));

    //Create datatable from the dataset
    DataTable dataTable = new DataTable();
    dataTable = customersDataSet.Tables[0];

    //Import data from the data table with column header, at first row and first column, 
    //and by its column type.
    sheet.ImportDataTable(dataTable, true, 1, 1, true);

    //Creating Excel table or list object and apply style to the table
    IListObject table = sheet.ListObjects.Create("Employee_PersonalDetails", sheet.UsedRange);

    table.BuiltInTableStyle = TableBuiltInStyles.TableStyleMedium14;

    //Autofit the columns
    sheet.UsedRange.AutofitColumns();

    //Save the file in the given path
    Stream excelStream = File.Create(Path.GetFullPath(@"Output.xlsx"));
    workbook.SaveAs(excelStream);
    excelStream.Dispose();
} 
```

[![Export DataTable to Excel in C#](img/6ba8a42868bfbdcde2eafbad4d540f0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fBaC1Ph0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/DataTable-to-Excel_900x177.png)

*将数据表输出到 Excel*

当将大量数据导出到 Excel 时，如果不需要应用数字格式和样式，可以使用 [ImportDataTable](https://help.syncfusion.com/cr/cref_files/windowsforms/Syncfusion.XlsIO.Base~Syncfusion.XlsIO.IWorksheet~ImportDataTable(DataTable,Int32,Int32,Boolean).html) 重载，将 *importOnSave* 参数的真值设为真。这里，导出发生在保存 Excel 文件时。

使用此选项以高性能导出大型数据。

```
value = instance.ImportDataTable(dataTable, firstRow, firstColumn, importOnSave); 
```

如果您有一个命名范围，并且希望从命名范围的特定行和列将数据导出到命名范围，您可以使用下面的 API，其中 rowOffset 和 columnOffset 是从命名范围中的特定单元格导入的参数。

```
value = instance.ImportDataTable(dataTable, namedRange, showColumnName, rowOffset, colOffset); 
```

## 2。从对象集合导出到 Excel

将对象集合中的数据导出到 Excel 工作表是一种常见的情况。但是，如果您需要将数据从模型导出到 Excel 工作表，此选项会很有帮助。

Syncfusion Excel (XlsIO)库支持将数据从对象集合导出到 Excel 工作表。

通过 [ImportData](https://help.syncfusion.com/file-formats/xlsio/working-with-data#importing-data-to-worksheets) 方法可以将数据从对象集合导出到 Excel 工作表。下面的代码示例显示了如何**将数据从集合导出到 Excel** 工作表。

```
using (ExcelEngine excelEngine = new ExcelEngine())
{
    IApplication application = excelEngine.Excel;
    application.DefaultVersion = ExcelVersion.Excel2016;

    //Read the data from XML file
    StreamReader reader = new StreamReader(Path.GetFullPath(@"../../Data/Customers.xml"));

    //Assign the data to the customerObjects collection
    IEnumerable customerObjects = GetData (reader.ReadToEnd());   

    //Create a new workbook
    IWorkbook workbook = application.Workbooks.Create(1);
    IWorksheet sheet = workbook.Worksheets[0];

    //Import data from customerObjects collection
    sheet.ImportData(customerObjects, 5, 1, false);

    #region Define Styles
    IStyle pageHeader = workbook.Styles.Add("PageHeaderStyle");
    IStyle tableHeader = workbook.Styles.Add("TableHeaderStyle");

    pageHeader.Font.RGBColor = Color.FromArgb(0, 83, 141, 213);
    pageHeader.Font.FontName = "Calibri";
    pageHeader.Font.Size = 18;
    pageHeader.Font.Bold = true;
    pageHeader.HorizontalAlignment = ExcelHAlign.HAlignCenter;
    pageHeader.VerticalAlignment = ExcelVAlign.VAlignCenter;

    tableHeader.Font.Color = ExcelKnownColors.White;
    tableHeader.Font.Bold = true;
    tableHeader.Font.Size = 11;
    tableHeader.Font.FontName = "Calibri";
    tableHeader.HorizontalAlignment = ExcelHAlign.HAlignCenter;
    tableHeader.VerticalAlignment = ExcelVAlign.VAlignCenter;
    tableHeader.Color = Color.FromArgb(0, 118, 147, 60);
    tableHeader.Borders[ExcelBordersIndex.EdgeLeft].LineStyle = ExcelLineStyle.Thin;
    tableHeader.Borders[ExcelBordersIndex.EdgeRight].LineStyle = ExcelLineStyle.Thin;
    tableHeader.Borders[ExcelBordersIndex.EdgeTop].LineStyle = ExcelLineStyle.Thin;
    tableHeader.Borders[ExcelBordersIndex.EdgeBottom].LineStyle = ExcelLineStyle.Thin;
    #endregion

    #region Apply Styles
    //Apply style to the header
    sheet["A1"].Text = "Yearly Sales Report";
    sheet["A1"].CellStyle = pageHeader;

    sheet["A2"].Text = "Namewise Sales Comparison Report";
    sheet["A2"].CellStyle = pageHeader;
    sheet["A2"].CellStyle.Font.Bold = false;
    sheet["A2"].CellStyle.Font.Size = 16;

    sheet["A1:D1"].Merge();
    sheet["A2:D2"].Merge();
    sheet["A3:A4"].Merge();
    sheet["D3:D4"].Merge();
    sheet["B3:C3"].Merge();

    sheet["B3"].Text = "Sales";
    sheet["A3"].Text = "Sales Person";
    sheet["B4"].Text = "January - June";
    sheet["C4"].Text = "July - December";
    sheet["D3"].Text = "Change(%)";
    sheet["A3:D4"].CellStyle = tableHeader;
    sheet.UsedRange.AutofitColumns();
    sheet.Columns[0].ColumnWidth = 24;
    sheet.Columns[1].ColumnWidth = 21;
    sheet.Columns[2].ColumnWidth = 21;
    sheet.Columns[3].ColumnWidth = 16;
    #endregion

    sheet.UsedRange.AutofitColumns();

    //Save the file in the given path
    Stream excelStream = File.Create(Path.GetFullPath(@"Output.xlsx"));
    workbook.SaveAs(excelStream);
    excelStream.Dispose();
} 
```

[![Export collection of objects to Excel in c#](img/c306997ecd875aaf1c18bd18ff06ea67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RQvl3tZt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/Collection-to-Excel.png)

*将对象集合输出到 Excel*

## 3。从数据库导出到 Excel

Excel 支持从不同的数据库创建 Excel 表格。如果您有一个场景，需要使用 Excel 从数据库创建一个或多个 Excel 表，则需要建立每个连接来创建这些表。这可能很费时间，所以如果你找到一种替代的方法来非常快速和容易地从数据库中生成 Excel 表格，这难道不是你的首选吗？

Syncfusion Excel (XlsIO)库可帮助您将数据从 MS SQL、MS Access、Oracle 等数据库导出到 Excel 工作表。通过在数据库和 Excel 应用程序之间建立连接，您可以将数据从**数据库导出到 Excel 表格**。

您可以使用*[【Refresh()](https://help.syncfusion.com/cr/file-formats/Syncfusion.XlsIO.Base~Syncfusion.XlsIO.IListObject~Refresh.html)*_ _ 选项更新映射到数据库的 Excel 表中的修改数据。

最重要的是，您可以参考文档[从外部连接](https://help.syncfusion.com/file-formats/xlsio/working-with-tables?cs-save-lang=1&cs-lang=csharp#create-a-table-from-external-connection)创建表格，以了解更多关于如何将数据库导出到 Excel 表格的信息。下面的代码示例显示了如何将数据从数据库导出到 Excel 表。

```
using (ExcelEngine excelEngine = new ExcelEngine())
{
    IApplication application = excelEngine.Excel;
    application.DefaultVersion = ExcelVersion.Excel2016;

    //Create a new workbook
    IWorkbook workbook = application.Workbooks.Create(1);
    IWorksheet sheet = workbook.Worksheets[0];

    if(sheet.ListObjects.Count == 0)
    {
        //Estabilishing the connection in the worksheet
        string dBPath = Path.GetFullPath(@"../../Data/EmployeeData.mdb");
        string ConnectionString = "OLEDB;Provider=Microsoft.JET.OLEDB.4.0;Password=\"\";User ID=Admin;Data Source="+ dBPath;
        string query = "SELECT EmployeeID,FirstName,LastName,Title,HireDate,Extension,ReportsTo FROM [Employees]";
        IConnection Connection = workbook.Connections.Add("Connection1", "Sample connection with MsAccess", ConnectionString, query, ExcelCommandType.Sql);
        sheet.ListObjects.AddEx(ExcelListObjectSourceType.SrcQuery, Connection, sheet.Range["A1"]);
    }

    //Refresh Excel table to get updated values from database
    sheet.ListObjects[0].Refresh();

    sheet.UsedRange.AutofitColumns();

    //Save the file in the given path
    Stream excelStream = File.Create(Path.GetFullPath(@"Output.xlsx"));
    workbook.SaveAs(excelStream);
    excelStream.Dispose();
} 
```

[![Export Database to Excel in c#](img/ce1e987c2054bec72da2a2bafa06b66d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hz_JI7ot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/Database-to-Excel-1.png)

*将数据库输出到 Excel 表格*

## 4。将数据从 DataGrid、GridView、DatGridView 导出到 Excel

将数据从 Microsoft grid 控件导出到 Excel 工作表有助于以不同的方式可视化数据。您可能需要工作数小时来迭代网格单元中的数据及其样式，以便将它们导出到 Excel 工作表中。对于那些将数据从 Microsoft grid 控件导出到 Excel 工作表的人来说应该是个好消息，因为使用 Syncfusion Excel 库导出要快得多。

Syncfusion Excel (XlsIO)库支持在单个 API 调用中将数据从 Microsoft Grid 控件导出到 Excel 工作表，如 DataGrid、GridView 和 DataGridView。此外，您可以导出带有标题和样式的数据。

下面的代码示例演示如何将数据从 DataGridView 导出到 Excel 工作表。

```
#region Loading the data to DataGridView DataSet customersDataSet = new DataSet();

//Read the XML file with data
string inputXmlPath = Path.GetFullPath(@"../../Data/Employees.xml");
customersDataSet.ReadXml(inputXmlPath);
DataTable dataTable = new DataTable();

//Copy the structure and data of the table
dataTable = customersDataSet.Tables[1].Copy();

//Removing unwanted columns
dataTable.Columns.RemoveAt(0);
dataTable.Columns.RemoveAt(10);
this.dataGridView1.DataSource = dataTable;

dataGridView1.AlternatingRowsDefaultCellStyle.BackColor = Color.White;
dataGridView1.RowsDefaultCellStyle.BackColor = Color.LightBlue;
dataGridView1.ColumnHeadersDefaultCellStyle.Font = new System.Drawing.Font("Tahoma", 9F, ((System.Drawing.FontStyle)(System.Drawing.FontStyle.Bold)));
dataGridView1.ForeColor = Color.Black;
dataGridView1.BorderStyle = BorderStyle.None;
#endregion 
using (ExcelEngine excelEngine = new ExcelEngine())
{
    IApplication application = excelEngine.Excel;

    //Create a workbook with single worksheet
    IWorkbook workbook = application.Workbooks.Create(1);

    IWorksheet worksheet = workbook.Worksheets[0];

    //Import from DataGridView to worksheet
    worksheet.ImportDataGridView(dataGridView1, 1, 1, isImportHeader: true, isImportStyle: true);

    worksheet.UsedRange.AutofitColumns();
    workbook.SaveAs("Output.xlsx");
} 
```

[![Export Microsoft DataGridView to Excel in c#](img/b2b336e4447b1e5360f5992c6630b6c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OEYBj_CD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/Grid-to-Excel.png)

*Microsoft DataGridView 转 Excel*

## 5。从数组导出到 Excel

有时，可能需要在 Excel 工作表的现有数据中插入或修改一组数据。在这种情况下，行数和列数是预先知道的。当大小固定时，数组很有用。

Syncfusion Excel (XlsIO)库支持将一组数据横向和纵向导出到 Excel 工作表中。此外，二维数组也可以导出。

让我们考虑一个场景，“人均费用”一个人一整年的开销都列在 Excel 工作表中。在这个场景中，您需要在新的一行中添加一个新的人*保罗·博格巴*的费用，并修改所有被跟踪的人在*十二月*的费用。

[![Excel data before exporting from array to Excel in c#](img/65b8bb9ead1d354ed24937fd7ac61348.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C2COUQ8a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/Array-to-Excel_1.png)

*从数组*导出前的 Excel 数据

通过 [ImportArray](https://help.syncfusion.com/file-formats/xlsio/working-with-data#import-data-from-array) 方法可以实现将一组数据导出到 Excel 工作表中。下面的代码示例展示了如何**将一组数据横向和纵向导出到 Excel** 工作表中。

```
using (ExcelEngine excelEngine = new ExcelEngine())
{
    IApplication application = excelEngine.Excel;
    application.DefaultVersion = ExcelVersion.Excel2016;

    //Reads input Excel stream as a workbook
    IWorkbook workbook = application.Workbooks.Open(File.OpenRead(Path.GetFullPath(@"../../../Expenses.xlsx")));
    IWorksheet sheet = workbook.Worksheets[0];

    //Preparing first array with different data types
    object[] expenseArray = new object[14]
    {"Paul Pogba", 469.00d, 263.00d, 131.00d, 139.00d, 474.00d, 253.00d, 467.00d, 142.00d, 417.00d, 324.00d, 328.00d, 497.00d, "=SUM(B11:M11)"};

    //Inserting a new row by formatting as a previous row.
    sheet.InsertRow(11, 1, ExcelInsertOptions.FormatAsBefore);

    //Import Peter's expenses and fill it horizontally
    sheet.ImportArray(expenseArray, 11, 1, false);

    //Preparing second array with double data type
    double[] expensesOnDec = new double[6]
    {179.00d, 298.00d, 484.00d, 145.00d, 20.00d, 497.00d};

    //Modify the December month's expenses and import it vertically
    sheet.ImportArray(expensesOnDec, 6, 13, true);

    //Save the file in the given path
    Stream excelStream = File.Create(Path.GetFullPath(@"Output.xlsx"));
    workbook.SaveAs(excelStream);
    excelStream.Dispose();
} 
```

[![Export array of data to Excel in c#](img/727428079cfc92069ad1059813f5af59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vw7luQsJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/Array-to-Excel_2.png)

*将数组数据输出到 Excel*

## 6。从 CSV 导出到 Excel

逗号分隔值(CSV)文件有助于生成包含少量列和大量行的表格数据或轻量级报告。Excel 打开这样的文件是为了使数据更容易阅读。

Syncfusion Excel (XlsIO)库支持在几秒钟内打开和保存 CSV 文件。下面的代码示例显示了如何打开一个 CSV 文件，并将其保存为 XLSX 文件。最重要的是，数据显示在应用了数字格式的表格中。

```
using (ExcelEngine excelEngine = new ExcelEngine())
{
    IApplication application = excelEngine.Excel;
    application.DefaultVersion = ExcelVersion.Excel2016;

    //Preserve data types as per the value
    application.PreserveCSVDataTypes = true;

    //Read the CSV file
    Stream csvStream = File.OpenRead(Path.GetFullPath(@"../../../TemplateSales.csv")); ;

    //Reads CSV stream as a workbook
    IWorkbook workbook = application.Workbooks.Open(csvStream);
    IWorksheet sheet = workbook.Worksheets[0];

    //Formatting the CSV data as a Table 
    IListObject table = sheet.ListObjects.Create("SalesTable", sheet.UsedRange);
    table.BuiltInTableStyle =  TableBuiltInStyles.TableStyleMedium6;
    IRange location = table.Location;
    location.AutofitColumns();

    //Apply the proper latitude & longitude numerformat in the table
    TryAndUpdateGeoLocation(table,"Latitude");
    TryAndUpdateGeoLocation(table,"Longitude");

    //Apply currency numberformat in the table column 'Price'
    IRange columnRange = GetListObjectColumnRange(table,"Price");
    if(columnRange != null)
        columnRange.CellStyle.NumberFormat = "$#,##0.00";

    //Apply Date time numberformat in the table column 'Transaction_date'
    columnRange = GetListObjectColumnRange(table,"Transaction_date");
    if(columnRange != null)
        columnRange.CellStyle.NumberFormat = "m/d/yy h:mm AM/PM;@";

    //Sort the data based on 'Products'
    IDataSort sorter = table.AutoFilters.DataSorter;
    ISortField sortField = sorter. SortFields. Add(0, SortOn. Values, OrderBy. Ascending);
    sorter. Sort();

    //Save the file in the given path
    Stream excelStream;
    excelStream = File.Create(Path.GetFullPath(@"../../../Output.xlsx"));
    workbook.SaveAs(excelStream);
    excelStream.Dispose();
} 
```

[![Input CSV File](img/3752b96c405dd09c65df47fea7c9b9af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GuiQg0PZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/CSV-to-Excel_Input.png)

*输入 CSV 文件*

[![Export CSV to Excel in c#](img/a4d1ce00cc2037fe9f809333bae717bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ubehnLyP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/CSV-to-Excel_Output.png)

*CSV 输出转换成 Excel*

## 包装完毕

可以看到， [**Syncfusion Excel (XlsIO)库**](https://www.syncfusion.com/excel-framework/net) 在 C#中提供了各种将数据导出到 Excel 的简便方法。有效地使用它们来生成高性能的 Excel 报表和处理大量数据。花点时间细读一下[文档](https://help.syncfusion.com/file-formats/xlsio/working-with-data#importing-data-to-worksheets)，在那里你会找到其他选项和特性，都附有代码示例。使用该库，您还可以将 Excel 数据导出为 [PDF](https://help.syncfusion.com/file-formats/xlsio/excel-to-pdf-conversion) 、[图片](https://help.syncfusion.com/file-formats/xlsio/worksheet-to-image-conversion)、[数据表](https://help.syncfusion.com/file-formats/xlsio/working-with-data#exporting-from-worksheet-to-data-table)、 [CSV](https://www.syncfusion.com/kb/9092/convert-excel-file-to-csv-in-c-vb-net) 、 [TSV](https://www.syncfusion.com/kb/8556/does-xlsio-support-tsv-files) 、HTML、[对象集合](https://help.syncfusion.com/file-formats/xlsio/working-with-data#exporting-from-worksheet-to-clr-objects)、 [ODS](https://help.syncfusion.com/file-formats/xlsio/excel-to-ods-conversion) 文件格式等。

如果您不熟悉我们的 Excel 库，强烈建议您遵循我们的[入门指南](https://help.syncfusion.com/file-formats/xlsio/getting-started)。

您已经是 Syncfusion 用户了吗？你可以在这里下载产品设置[。如果你还不是 Syncfusion 的用户，你可以在这里下载免费的 30 天试用版](https://www.syncfusion.com/account/downloads/studio/)。

如果您对这些功能有任何疑问或需要澄清，请在下面的评论中告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums?_ga=2.160385213.108886952.1555906495-214292665.1551328372)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/?_ga=2.160385213.108886952.1555906495-214292665.1551328372) 或[反馈门户](https://www.syncfusion.com/feedback/)联系我们。我们很乐意为您提供帮助！

帖子 [6 种用 C#](https://blog.syncfusion.com/post/6-easy-ways-to-export-data-to-excel-in-c-sharp.aspx) 将数据导出到 Excel 的简单方法最先出现在 [Syncfusion 博客](https://blog.syncfusion.com)上。