# 在 Java 应用程序中创建演示图表

> 原文：<https://dev.to/eiceblue/create-presentation-chart-in-java-applications-3807>

演示图表用于显示包含大量数据的重要信息，很容易吸引读者的眼球。PowerPoint 幻灯片中有许多不同类型的数据图表，如柱形图、条形图、折线图、饼图、散点图、圆环图和组合图。在这篇文章中，我们将清楚地展示如何使用 [Free Spire 在 PowerPoint 文档中创建图表。Java 的演示文稿](https://www.e-iceblue.com/Introduce/free-presentation-for-java.html)。

本文包含以下两个部分:

*在 PowerPoint 中创建单柱形图。
*在 PowerPoint 中创建柱形图和折线图的组合图。

[下载免费 Spire。展示罐](https://www.e-iceblue.com/Download/presentation-for-java-free.html)

如何用 Java 创建演示柱形图的代码片段:

```
import com.spire.presentation.*;
import com.spire.pdf.tables.table.*;
import com.spire.presentation.charts.*;
import com.spire.presentation.drawing.FillFormatType;
import java.awt.geom.Rectangle2D;
import java.lang.Object;

public class PPTColumnChart {
    public static void main(String[] args) throws Exception{
        //Create a presentation instance
        Presentation presentation = new Presentation();

        //Add a column clustered chart
        Rectangle2D.Double rect = new Rectangle2D.Double(40, 100, 550, 320);
        IChart chart = null;
        chart = presentation.getSlides().get(0).getShapes().appendChart(ChartType.COLUMN_CLUSTERED, rect);

        //Set chart title
        chart.getChartTitle().getTextProperties().setText("Sales Report");
        chart.getChartTitle().getTextProperties().isCentered(true);
        chart.getChartTitle().setHeight(30);
        chart.hasTitle(true);

        //Create a dataTable
        DataTable dataTable = new DataTable();
        dataTable.getColumns().add(new DataColumn("SalesPers", DataTypes.DATATABLE_STRING));
        dataTable.getColumns().add(new DataColumn("SaleAmt", DataTypes.DATATABLE_INT));
        dataTable.getColumns().add(new DataColumn("ComPct", DataTypes.DATATABLE_INT));
        dataTable.getColumns().add(new DataColumn("ComAmt", DataTypes.DATATABLE_INT));
        DataRow row1 = dataTable.newRow();
        row1.setString("SalesPers", "Joe");
        row1.setInt("SaleAmt", 250);
        row1.setInt("ComPct", 150);
        row1.setInt("ComAmt", 99);
        DataRow row2 = dataTable.newRow();
        row2.setString("SalesPers", "Robert");
        row2.setInt("SaleAmt", 270);
        row2.setInt("ComPct", 150);
        row2.setInt("ComAmt", 99);
        DataRow row3 = dataTable.newRow();
        row3.setString("SalesPers", "Michelle");
        row3.setInt("SaleAmt", 310);
        row3.setInt("ComPct", 120);
        row3.setInt("ComAmt", 49);
        DataRow row4 = dataTable.newRow();
        row4.setString("SalesPers", "Erich");
        row4.setInt("SaleAmt", 330);
        row4.setInt("ComPct", 120);
        row4.setInt("ComAmt", 49);
        DataRow row5 = dataTable.newRow();
        row5.setString("SalesPers", "Dafna");
        row5.setInt("SaleAmt", 360);
        row5.setInt("ComPct", 150);
        row5.setInt("ComAmt", 141);
        DataRow row6 = dataTable.newRow();
        row6.setString("SalesPers", "Rob");
        row6.setInt("SaleAmt", 380);
        row6.setInt("ComPct", 150);
        row6.setInt("ComAmt", 135);
        dataTable.getRows().add(row1);
        dataTable.getRows().add(row2);
        dataTable.getRows().add(row3);
        dataTable.getRows().add(row4);
        dataTable.getRows().add(row5);
        dataTable.getRows().add(row6);

        //Import data from dataTable to chart data
        for (int c = 0; c < dataTable.getColumns().size(); c++) {
            chart.getChartData().get(0, c).setText(dataTable.getColumns().get(c).getColumnName());
        }
        for (int r = 0; r < dataTable.getRows().size(); r++) {
            Object[] datas = dataTable.getRows().get(r).getArrayList();
            for (int c = 0; c < datas.length; c++) {
                chart.getChartData().get(r + 1, c).setValue(datas[c]);

            }
        }

        chart.getSeries().setSeriesLabel(chart.getChartData().get("B1", "D1"));
        chart.getCategories().setCategoryLabels(chart.getChartData().get("A2", "A7"));
        chart.getSeries().get(0).setValues(chart.getChartData().get("B2", "B7"));

        chart.getSeries().get(1).setValues(chart.getChartData().get("C2", "C7"));

        chart.getSeries().get(2).setValues(chart.getChartData().get("D2", "D7"));
        chart.getSeries().get(2).getFill().setFillType(FillFormatType.SOLID);
        chart.getSeries().get(2).getFill().getSolidColor().setKnownColor(KnownColors.LIGHT_BLUE);

        //Set overlap
        chart.setOverLap(-50);

        //Set gap width
        chart.setGapDepth(200);

        //Save the document
        presentation.saveToFile("output/CreateChart.pptx", FileFormat.PPTX_2010);
    }
} 
```

柱形图有效截图:
[![](img/b6818225beb15f58ce5584266bf1ce61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--doEBu1ap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qt7c1sd8rjtlakzcy7ud.png)

组合图是将至少两种图表类型组合在一个图表中的图表，此示例将显示一个包含柱形图和折线图的组合图。

```
import com.spire.presentation.*;
import com.spire.presentation.charts.*;
import com.spire.presentation.drawing.FillFormatType;
import java.awt.geom.Rectangle2D;

public class CombinationChart {
    public static void main(String[] args) throws Exception{

            //create a PowerPoint document
            Presentation presentation = new Presentation();

            //insert a column clustered chart
            Rectangle2D.Double rect = new   Rectangle2D.Double(50, 100, 550, 300);
            IChart chart = presentation.getSlides().get(0).getShapes().appendChart(ChartType.COLUMN_CLUSTERED, rect);

            //set chart title
            chart.getChartTitle().getTextProperties().setText("Sales vs. Unit Price");
            chart.getChartTitle().getTextProperties().isCentered(true);
            chart.getChartTitle().setHeight(30);
            chart.hasTitle(true);

            //write data to chart as chart data
            chart.getChartData().get(0,0).setText("Month");
            chart.getChartData().get(0,1).setText("Unit Price");
            chart.getChartData().get(0,2).setText("Sales");
            chart.getChartData().get(1,0).setText("January");
            chart.getChartData().get(1,1).setNumberValue(120);
            chart.getChartData().get(1,2).setNumberValue(5600);
            chart.getChartData().get(2,0).setText("February");
            chart.getChartData().get(2,1).setNumberValue(100);
            chart.getChartData().get(2,2).setNumberValue(7300);
            chart.getChartData().get(3,0).setText("March");
            chart.getChartData().get(3,1).setNumberValue(80);
            chart.getChartData().get(3,2).setNumberValue(10200);
            chart.getChartData().get(4,0).setText("April");
            chart.getChartData().get(4,1).setNumberValue(120);
            chart.getChartData().get(4,2).setNumberValue(5900);
            chart.getChartData().get(5,0).setText("May");
            chart.getChartData().get(5,1).setNumberValue(90);
            chart.getChartData().get(5,2).setNumberValue(9500);
            chart.getChartData().get(6,0).setText("June");
            chart.getChartData().get(6,1).setNumberValue(110);
            chart.getChartData().get(6,2).setNumberValue(7200);

            //set series labels
            chart.getSeries().setSeriesLabel(chart.getChartData().get("B1", "C1"));

            //set categories labels
            chart.getCategories().setCategoryLabels(chart.getChartData().get("A2", "A7"));

            //assign data to series values
            chart.getSeries().get(0).setValues(chart.getChartData().get("B2", "B7"));
            chart.getSeries().get(1).setValues(chart.getChartData().get("C2", "C7"));

            //change the chart type of series 2 to line with markers
            chart.getSeries().get(1).setType(ChartType.LINE_MARKERS);

            //plot data of series 2 on the secondary axis
            chart.getSeries().get(1).setUseSecondAxis(true);

            //hide grid links of secondary axis
            chart.getSecondaryValueAxis().getMajorGridTextLines().setFillType(FillFormatType.NONE);

            //set overlap
            chart.setOverLap(-50);

            //set gap width
            chart.setGapDepth(200);

            //save the document
            presentation.saveToFile("output/CombinationChart.pptx", FileFormat.PPTX_2010);
        }
} 
```

组合图的有效截图

[![](img/792ef1643f65918a198da71154d7a727.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--61o2YO8D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ubcw8df8ft7489brez8t.png)

结论

感谢自由尖塔。Java 演示文稿，我可以向您展示如何在 Java 中以编程方式插入演示图表。我希望你看完我的文章后能对演示幻灯片上的图表工具有更深的理解。感谢阅读。