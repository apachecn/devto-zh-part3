# 如何将数据导出到 XLSX 文件

> 原文：<https://dev.to/djangotricks/how-to-export-data-to-xlsx-files-1dj0>

[![](img/fb5bb7e842ea37ef775a61020e525624.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L2zViE2g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-isH4SKIR1No/XGYHR4fL36I/AAAAAAAAB8c/21tlFQt5RJYeNuKWZKReVRyRY0CULZcUwCLcBGAs/s1600/how-to-export-data-to-xlsx-files.png)

不久前，我写了一篇关于将数据导出到不同电子表格格式的文章。最近我在为[第一件事第一件事](https://www.1st-things-1st.com)项目重新实现导出到 Excel 时，我注意到 API 发生了一点变化，所以是时候再次写博客了。

对于 Excel 导出，我使用 XLSX 文件格式，这是一种基于 XML 的压缩格式，支持电子表格格式。XLSX 文件可以用 Microsoft Excel、Apache OpenOffice、Apple Numbers、LibreOffice、Google Drive 和其他一些应用程序打开。为了构建 XLSX 文件，我使用了 **openpyxl** 库。

## 安装 openpyxl

您可以使用 pip:
以通常的方式将 openpyxl 安装到您的虚拟环境中

```
(venv) pip install openpyxl==2.6.0 
```

Enter fullscreen mode Exit fullscreen mode

## 最简单的导出视图

要创建一个将数据从 QuerySet 导出到 XLSX 文件的函数，您需要创建一个视图，该视图返回一个带有特殊内容类型和文件内容的响应作为附件。将视图插入 URL 规则，然后从模板中的导出按钮链接它。

从 Django QuerySet 生成 XLSX 文件的最简单的视图可能是这样的:

```
# movies/views.py from datetime import datetime
from datetime import timedelta
from openpyxl import Workbook
from django.http import HttpResponse

from .models import MovieCategory, Movie

def export_movies_to_xlsx(request):
    """
    Downloads all movies as Excel file with a single worksheet
    """
    movie_queryset = Movie.objects.all()

    response = HttpResponse(
        content_type='application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
    )
    response['Content-Disposition'] = 'attachment; filename={date}-movies.xlsx'.format(
        date=datetime.now().strftime('%Y-%m-%d'),
    )
    workbook = Workbook()

    # Get active worksheet/tab
    worksheet = workbook.active
    worksheet.title = 'Movies'

    # Define the titles for columns
    columns = [
        'ID',
        'Title',
        'Description',
        'Length',
        'Rating',
        'Price',
    ]
    row_num = 1

    # Assign the titles for each cell of the header
    for col_num, column_title in enumerate(columns, 1):
        cell = worksheet.cell(row=row_num, column=col_num)
        cell.value = column_title

    # Iterate through all movies
    for movie in movie_queryset:
        row_num += 1

        # Define the data for each cell in the row 
        row = [
            movie.pk,
            movie.title,
            movie.description,
            movie.length_in_minutes,
            movie.rating,
            movie.price,
        ]

        # Assign the data for each cell of the row 
        for col_num, cell_value in enumerate(row, 1):
            cell = worksheet.cell(row=row_num, column=col_num)
            cell.value = cell_value

    workbook.save(response)

    return response 
```

Enter fullscreen mode Exit fullscreen mode

如果您尝试这样做，您会注意到，其中没有特殊的格式，所有列都是相同的宽度，值类型几乎无法识别，标题显示与内容相同。这足以进一步将数据导出到 CSV 或用 **pandas** 进行操作。但是如果你想以一种友好的方式将数据呈现给用户，你需要增加一些魔力。

## 创建更多工作表

默认情况下，每个 Excel 文件都有一个表示为选项卡的工作表。您可以使用
来访问它

```
worksheet = workbook.active
worksheet.title = 'The New Tab Title' 
```

Enter fullscreen mode Exit fullscreen mode

如果您想要使用 Python 结构数据库中的数据动态创建选项卡，您可以首先删除当前选项卡，然后使用:
添加其他选项卡

```
workbook.remove(workbook.active)

for index, category in enumerate(category_queryset):
    worksheet = workbook.create_sheet(
        title=category.title,
        index=index,
    ) 
```

Enter fullscreen mode Exit fullscreen mode

虽然不是所有的电子表格应用程序都支持这一点，但是您可以使用
来设置工作表标签的背景颜色

```
worksheet.sheet_properties.tabColor = 'f7f7f9' 
```

Enter fullscreen mode Exit fullscreen mode

## 处理单元格

每个单元格都可以通过其行和列的基于 1 的索引来访问:

```
top_left_cell = worksheet.cell(row=1, column=1)
top_left_cell.value = "This is good!" 
```

Enter fullscreen mode Exit fullscreen mode

样式和格式应用于单个单元格，而不是行或列。有几个样式类别，每个类别都有多种配置。您可以从[文档](https://openpyxl.readthedocs.io/en/latest/styles.html)中找到一些可用的选项，但是通过探索[源代码](https://bitbucket.org/openpyxl/openpyxl/)可以找到更多选项。

```
from openpyxl.styles import Font, Alignment, Border, Side, PatternFill

top_left_cell.font = Font(name='Calibri', bold=True)
top_left_cell.alignment = Alignment(horizontal='center')
top_left_cell.border = Border(
    bottom=Side(border_style='medium', color='FF000000'),
)
top_left_cell.fill = PatternFill(
    start_color='f7f7f9',
    end_color='f7f7f9',
    fill_type='solid',
) 
```

Enter fullscreen mode Exit fullscreen mode

如果您计划拥有多个样式元素，那么预先实例化字体、对齐、边框、填充选项，然后将实例分配给单元格属性。否则，当您有大量数据条目时，您可能会遇到内存问题。

## 设置列宽

如果希望某些列的宽度更宽或更窄，可以通过修改列尺寸来实现。它们通过列字母进行访问，可以使用实用函数
进行检索

```
from openpyxl.utils import get_column_letter

column_letter = get_column_letter(col_num)
column_dimensions = worksheet.column_dimensions[column_letter]
column_dimensions.width = 40 
```

Enter fullscreen mode Exit fullscreen mode

这里的单位是一些相对点，取决于指定字体中字母的宽度。我建议你试试宽度值，直到找到适合你的为止。

当定义列宽还不够时，您可能希望将文本换行，以便人们可以毫无问题地阅读所有内容。这可以通过单元格的对齐设置来完成，如下:

```
from openpyxl.styles import Alignment

wrapped_alignment = Alignment(vertical='top', wrap_text=True)
cell.alignment = wrapped_alignment 
```

Enter fullscreen mode Exit fullscreen mode

## 数据格式化

Excel 自动检测文本或数字类型，并将文本靠左对齐，数字靠右对齐。如有必要，可以覆盖。

当您需要百分比、价格或持续时间时，有一些关于如何格式化单元格的技巧。

### 百分比

对于百分比，您必须以浮点格式传递从 0.0 到 1.0 的数字，并且样式应该是“百分比”，如下所示:

```
cell.value = 0.75
cell.style = 'Percent' 
```

Enter fullscreen mode Exit fullscreen mode

### 货币

对于货币，您需要`Decimal`格式的值，样式应该是“货币”,对于美元以外的货币，您将需要一个特殊的数字格式，例如:

```
from decimal import Decimal
cell.value = Decimal('14.99')
cell.style = 'Currency'
cell.number_format = '#,##0.00 €' 
```

Enter fullscreen mode Exit fullscreen mode

### 持续时间

对于持续时间，您必须将 timedelta 作为值传递，并定义特殊的数字格式:

```
from datetime import timedelta

cell.value = timedelta(minutes=90)
cell.number_format = '[h]:mm;@' 
```

Enter fullscreen mode Exit fullscreen mode

此数字格式确保您的持续时间可以大于“23:59”，例如，“140:00”。

## 冻结行和列

在 Excel 中，您可以冻结行和列，以便在垂直或水平滚动内容时它们保持固定。那类似于 CSS 中的`position: fixed`。

若要冻结行和列，请找到位于要冻结的行下方、要冻结的列右侧的左上角单元格。例如，如果您想要冻结一行和一列，单元格应该是“B2”。然后运行这个:

```
worksheet.freeze_panes = worksheet['B2'] 
```

Enter fullscreen mode Exit fullscreen mode

## 完全定制的导出视图

因此，了解了本文之后，我们现在可以构建一个视图来创建单独的工作表。对于每个电影类别。每张表都会列出该类别的电影，包括标题、描述、时长(小时和分钟)、评级(百分比)和欧元价格。每个电影类别的标签和标题可以有不同的背景颜色。单元格将被很好地格式化。标题和描述将使用多行来完全适应单元格。

```
# movies/views.py from datetime import datetime
from datetime import timedelta
from openpyxl import Workbook
from openpyxl.styles import Font, Alignment, Border, Side, PatternFill
from openpyxl.utils import get_column_letter
from django.http import HttpResponse

from .models import MovieCategory, Movie

def export_movies_to_xlsx(request):
    """
    Downloads all movies as Excel file with a worksheet for each movie category
    """
    category_queryset = MovieCategory.objects.all()

    response = HttpResponse(
        content_type='application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
    )
    response['Content-Disposition'] = 'attachment; filename={date}-movies.xlsx'.format(
        date=datetime.now().strftime('%Y-%m-%d'),
    )
    workbook = Workbook()

    # Delete the default worksheet
    workbook.remove(workbook.active)

    # Define some styles and formatting that will be later used for cells
    header_font = Font(name='Calibri', bold=True)
    centered_alignment = Alignment(horizontal='center')
    border_bottom = Border(
        bottom=Side(border_style='medium', color='FF000000'),
    )
    wrapped_alignment = Alignment(
        vertical='top',
        wrap_text=True
    )

    # Define the column titles and widths
    columns = [
        ('ID', 8),
        ('Title', 40),
        ('Description', 80),
        ('Length', 15),
        ('Rating', 15),
        ('Price', 15),
    ]

    # Iterate through movie categories
    for category_index, category in enumerate(category_queryset):
        # Create a worksheet/tab with the title of the category
        worksheet = workbook.create_sheet(
            title=category.title,
            index=category_index,
        )
        # Define the background color of the header cells
        fill = PatternFill(
            start_color=category.html_color,
            end_color=category.html_color,
            fill_type='solid',
        )
        row_num = 1

        # Assign values, styles, and formatting for each cell in the header
        for col_num, (column_title, column_width) in enumerate(columns, 1):
            cell = worksheet.cell(row=row_num, column=col_num)
            cell.value = column_title
            cell.font = header_font
            cell.border = border_bottom
            cell.alignment = centered_alignment
            cell.fill = fill
            # set column width
            column_letter = get_column_letter(col_num)
            column_dimensions = worksheet.column_dimensions[column_letter]
            column_dimensions.width = column_width

        # Iterate through all movies of a category
        for movie in category.movie_set.all():
            row_num += 1

            # Define data and formats for each cell in the row
            row = [
                (movie.pk, 'Normal'),
                (movie.title, 'Normal'),
                (movie.description, 'Normal'),
                (timedelta(minutes=movie.length_in_minutes), 'Normal'),
                (movie.rating / 100, 'Percent'),
                (movie.price, 'Currency'),
            ]

            # Assign values, styles, and formatting for each cell in the row
            for col_num, (cell_value, cell_format) in enumerate(row, 1):
                cell = worksheet.cell(row=row_num, column=col_num)
                cell.value = cell_value
                cell.style = cell_format
                if cell_format == 'Currency':
                    cell.number_format = '#,##0.00 €'
                if col_num == 4:
                    cell.number_format = '[h]:mm;@'
                cell.alignment = wrapped_alignment

        # freeze the first row
        worksheet.freeze_panes = worksheet['A2']

        # set tab color
        worksheet.sheet_properties.tabColor = category.html_color

    workbook.save(response)

    return response 
```

Enter fullscreen mode Exit fullscreen mode

## 外卖

*   电子表格数据可以用**熊猫**做进一步的数学处理。
*   XLSX 文件格式允许相当多的格式选项，可以使您的电子表格数据更美观和用户友好。
*   要查看实际的 Excel 导出，请转到[第一件事第一件事](https://www.1st-things-1st.com)，以演示用户身份登录，并导航到项目结果，在那里您可以将它们导出为 XLSX。随时欢迎反馈。

* * *

蒂姆·埃文斯的封面照片。