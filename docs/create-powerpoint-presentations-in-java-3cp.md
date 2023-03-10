# 用 Java 创建 PowerPoint 演示文稿

> 原文：<https://dev.to/eiceblue/create-powerpoint-presentations-in-java-3cp>

在本文中，我们将向您展示如何使用免费的 Java PowerPoint API-Free Spire 从头开始创建 PowerPoint 演示文稿。Java 演示文稿。

# 目录

1.  自由尖顶概述。Java 演示文稿
2.  创建“Hello World”演示文稿
3.  格式化演示文稿中的内容
4.  向演示文稿添加图像
5.  向演示文稿添加项目符号列表
6.  在演示文稿中创建表格
7.  在演示文稿中创建图表
8.  将文档属性设置为演示文稿
9.  用密码保护演示文稿

# 自由尖顶概述。Java 演示文稿

**自由的尖顶。Presentation for Java** 是一个免费的 Java PowerPoint API，开发者可以通过它在 Java 应用中创建、读取、修改、写入、转换和保存 PowerPoint 文档，而无需安装 Microsoft Office。

*了解更多关于自由尖顶的信息。Java 演示文稿，点击这里的。*

**下载**免费 Spire。presentation jars:[https://www . e-ice blue . com/Download/presentation-for-Java-free . html](https://www.e-iceblue.com/Download/presentation-for-java-free.html)

# 创建“Hello World”演示文稿

以下示例显示了如何创建一个带有“Hello World”文本的简单演示文稿。

```
public class CreateHelloWorldPresentation {
    public static void main(String[] args) throws Exception {
        //Create a Presentation instance
        Presentation ppt = new Presentation();
        //Get the first slide (By default there is 1 slide after creating)
        ISlide slide = ppt.getSlides().get(0);

        Rectangle2D rect = new Rectangle2D.Double(ppt.getSlideSize().getSize().getWidth() / 2 - 150, 80, 300, 150);

        //Append a rectangle shape to the slide
        IAutoShape shape = slide.getShapes().appendShape(ShapeType.RECTANGLE, rect);
        shape.getShapeStyle().getLineColor().setColor(Color.white);
        shape.getFill().setFillType(FillFormatType.NONE);

        //Append text to the rectangle
        shape.appendTextFrame("Hello World!");

        //Get the 1st paragraph from the text frame
        ParagraphEx paragraph = shape.getTextFrame().getParagraphs().get(0);
        //Set font
        paragraph.getTextRanges().get(0).setLatinFont(new TextFont("Calibri"));
        paragraph.getTextRanges().get(0).setFontHeight(30);
        paragraph.getTextRanges().get(0).getFill().setFillType(FillFormatType.SOLID);
        paragraph.getTextRanges().get(0).getFill().getSolidColor().setColor(Color.black);

        //Save the presentation with file name "HelloWorld.pptx"
        ppt.saveToFile("HelloWorld.pptx", FileFormat.PPTX_2013);
    }
} 
```

[![Hello World example](img/caa590905873c8d07af9453bdcb29afb.png "Hello world example")](https://res.cloudinary.com/practicaldev/image/fetch/s--y2nGpmVt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uzsfk07ct34tpvrjellj.png)

# 格式化演示文稿中的文本内容

以下示例显示如何设置演示文稿中文本内容的格式。

```
public class FormatContent {
    public static void main(String[] args) throws Exception {
        //Create a Presentation instance
        Presentation ppt = new Presentation();

        Rectangle2D rect = new Rectangle2D.Double(20, 70, ppt.getSlideSize().getSize().getWidth() - 40, 300);

        //Get the 1st slide
        ISlide slide = ppt.getSlides().get(0);

        //Append shape
        IAutoShape shape = slide.getShapes().appendShape(ShapeType.RECTANGLE, rect);
        shape.getShapeStyle().getLineColor().setColor(Color.white);
        shape.getFill().setFillType(FillFormatType.NONE);

        shape.getTextFrame().getParagraphs().clear();

        //Append paragraphs and set formatting
        shape.getTextFrame().getParagraphs().append(new ParagraphEx());
        ParagraphEx paragraph = shape.getTextFrame().getParagraphs().get(0);
        paragraph.getTextRanges().append(new PortionEx("This is paragraph 1"));
        paragraph.setAlignment(TextAlignmentType.LEFT);
        paragraph.getTextRanges().get(0).setTextUnderlineType(TextUnderlineType.WAVY);
        paragraph.getTextRanges().get(0).getFill().setFillType(FillFormatType.SOLID);
        paragraph.getTextRanges().get(0).getFill().getSolidColor().setColor(Color.BLUE);

        shape.getTextFrame().getParagraphs().append(new ParagraphEx());
        paragraph = shape.getTextFrame().getParagraphs().get(1);
        paragraph.getTextRanges().append(new PortionEx("This is paragraph 2"));
        paragraph.setAlignment(TextAlignmentType.CENTER);
        paragraph.getTextRanges().get(0).setTextUnderlineType(TextUnderlineType.DOTTED);
        paragraph.getTextRanges().get(0).getFill().setFillType(FillFormatType.GRADIENT);
        paragraph.getTextRanges().get(0).getFill().getSolidColor().setColor(Color.BLACK);

        shape.getTextFrame().getParagraphs().append(new ParagraphEx());
        paragraph = shape.getTextFrame().getParagraphs().get(2);
        paragraph.getTextRanges().append(new PortionEx("This is paragraph 3"));
        paragraph.setAlignment(TextAlignmentType.RIGHT);
        paragraph.getTextRanges().get(0).setTextUnderlineType(TextUnderlineType.WAVY);
        paragraph.getTextRanges().get(0).getFill().setFillType(FillFormatType.SOLID);
        paragraph.getTextRanges().get(0).getFill().getSolidColor().setColor(Color.GREEN);

        for(int i = 0; i < shape.getTextFrame().getParagraphs().getCount(); i++)
        {
           paragraph = shape.getTextFrame().getParagraphs().get(i);
           paragraph.setSpaceAfter(50);
           paragraph.getTextRanges().get(0).setLatinFont(new TextFont("Arial"));
           paragraph.getTextRanges().get(0).setFontHeight(30f);
           paragraph.getTextRanges().get(0).isBold(TriState.TRUE);
        }

        //Save the document
        ppt.saveToFile("FormatContent.pptx", FileFormat.PPTX_2013);
        ppt.dispose();
    }
} 
```

[![Format text content](img/77ba25132c138daa1efbfa014995458d.png "Format text content")](https://res.cloudinary.com/practicaldev/image/fetch/s--3YRuMvTO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bunuzlmhhz0ycnfr7qdf.png)

# 给演示文稿添加图像

以下示例显示如何向演示文稿添加图像。

```
public class AddImages {
    public static void main(String[] args) throws Exception {
        Presentation ppt = new Presentation();

        Rectangle2D rect = new Rectangle2D.Double(50, 50, 200, 150);

        //Get the first slide
        ISlide slide = ppt.getSlides().get(0);
        //Insert an image into the slide
        IEmbedImage image = slide.getShapes().appendEmbedImage(ShapeType.RECTANGLE, "1.jpg", rect);
        image.getLine().setFillType(FillFormatType.NONE);

        rect = new Rectangle2D.Double(50, 250, 300, 200);
        image = slide.getShapes().appendEmbedImage(ShapeType.RECTANGLE, "392491.jpg", rect);
        image.getLine().setFillType(FillFormatType.NONE);

        ppt.saveToFile("AddImages.pptx", FileFormat.PPTX_2013);
    }
} 
```

[![Add images](img/02156dab8068e18de2418b3a735944e9.png "Add images")](https://res.cloudinary.com/practicaldev/image/fetch/s---NKrgadq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h688wjpu5vfp6paylph1.png)

# 向演示文稿添加项目符号列表

以下示例显示如何向演示文稿添加项目符号列表。

```
public class AddBulletLists {
    public static void main(String[] args) throws Exception {
        //Create a Presentation instance
        Presentation ppt = new Presentation();

        Rectangle2D rect = new Rectangle2D.Double(50, 70, 300, 150);

        //Get the 1st slide
        ISlide slide = ppt.getSlides().get(0);

        //Append shape
        IAutoShape shape = slide.getShapes().appendShape(ShapeType.RECTANGLE, rect);
        shape.getShapeStyle().getLineColor().setColor(Color.white);
        shape.getFill().setFillType(FillFormatType.NONE);

        shape.getTextFrame().getParagraphs().clear();

        String[] str = new String[] {"Item 1", "Item 2", "Item 3" };

        //Add bullet list
        for(int i = 0; i < str.length; i ++)
        {
            ParagraphEx paragraph = new ParagraphEx();
            paragraph.setText(str[i]);
            paragraph.setAlignment(TextAlignmentType.LEFT);
            paragraph.getTextRanges().get(0).getFill().setFillType(FillFormatType.SOLID);
            paragraph.getTextRanges().get(0).getFill().getSolidColor().setColor(Color.black);
            paragraph.setBulletType(TextBulletType.NUMBERED);
            paragraph.setBulletStyle(NumberedBulletStyle.BULLET_ROMAN_LC_PERIOD);
            shape.getTextFrame().getParagraphs().append(paragraph);
        }

        //Save the document
        ppt.saveToFile("AddBullets.pptx", FileFormat.PPTX_2013);
    }
} 
```

[![Add bullet list](img/d2d6736b3e5a4a8036a34d9d4bbab0a5.png "Add bullet list")](https://res.cloudinary.com/practicaldev/image/fetch/s--rHYHMPkh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/grb67l26kfljc7p6u19i.png)

# 在演示文稿中创建表格

以下示例显示如何在演示文稿中创建表格。

```
public class CreateTable {
    public static void main(String[] args) throws Exception {
        //Create a Presentation instance
        Presentation ppt = new Presentation();

        Double[] widths = new Double[]{100d, 100d, 150d, 100d, 100d};
        Double[] heights = new Double[]{15d, 15d, 15d, 15d, 15d, 15d, 15d, 15d, 15d, 15d, 15d, 15d, 15d};

        //Add a table to PPT
        ITable table = ppt.getSlides().get(0).getShapes().appendTable((float) ppt.getSlideSize().getSize().getWidth() / 2 - 275, 90, widths, heights);
        String[][] dataStr = new String[][]
                {
                        {"Name", "Capital", "Continent", "Area", "Population"},
                        {"Venezuela", "Caracas", "South America", "912047", "19700000"},
                        {"Bolivia", "La Paz", "South America", "1098575", "7300000"},
                        {"Brazil", "Brasilia", "South America", "8511196", "150400000"},
                        {"Canada", "Ottawa", "North America", "9976147", "26500000"},
                        {"Chile", "Santiago", "South America", "756943", "13200000"},
                        {"Colombia", "Bagota", "South America", "1138907", "33000000"},
                        {"Cuba", "Havana", "North America", "114524", "10600000"},
                        {"Ecuador", "Quito", "South America", "455502", "10600000"},
                        {"Paraguay", "Asuncion", "South America", "406576", "4660000"},
                        {"Peru", "Lima", "South America", "1285215", "21600000"},
                        {"Jamaica", "Kingston", "North America", "11424", "2500000"},
                        {"Mexico", "Mexico City", "North America", "1967180", "88600000"}
                };
        //Add data to table
        for (int i = 0; i < 13; i++) {
            for (int j = 0; j < 5; j++) {
                //Fill the table with data
                table.get(j, i).getTextFrame().setText(dataStr[i][j]);

                //Set font
                table.get(j, i).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).setLatinFont(new TextFont("Arial Narrow"));
            }
        }
        //Set the alignment of the first row to Center
        for (int i = 0; i < 5; i++) {
            table.get(i, 0).getTextFrame().getParagraphs().get(0).setAlignment(TextAlignmentType.CENTER);
        }
        //Set the style of table
        table.setStylePreset(TableStylePreset.LIGHT_STYLE_3_ACCENT_1);

        //Save the document
        ppt.saveToFile("AddTable.pptx", FileFormat.PPTX_2013);
    }
} 
```

[![Create table](img/f3629533540b50073bbb8fd2103b2c3b.png "Create table")](https://res.cloudinary.com/practicaldev/image/fetch/s--bl1jCDwb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qfwmc0kew4dapj4t1cd8.png)

# 在演示文稿中创建图表

自由尖塔。Java 演示支持各种类型的图表。这里我们选择气泡图作为例子。

```
public class CreateChart {
    public static void main(String[] args) throws Exception {
        //Create a Presentation instance
        Presentation ppt = new Presentation();

        //Add bubble chart
        Rectangle2D.Double rect1 = new Rectangle2D.Double(90, 100, 550, 320);
        IChart chart = null;
        chart = ppt.getSlides().get(0).getShapes().appendChart(ChartType.BUBBLE, rect1, false);

        //Chart title
        chart.getChartTitle().getTextProperties().setText("Bubble Chart");
        chart.getChartTitle().getTextProperties().isCentered(true);
        chart.getChartTitle().setHeight(30);
        chart.hasTitle(true);

        //Attach the data to chart
        double[] xdata = new double[]{7.7, 8.9, 1.0, 2.4};
        double[] ydata = new double[]{15.2, 5.3, 6.7, 8};
        double[] size = new double[]{1.1, 2.4, 3.7, 4.8};

        chart.getChartData().get(0, 0).setText("X-Value");
        chart.getChartData().get(0, 1).setText("Y-Value");
        chart.getChartData().get(0, 2).setText("Size");

        for (int i = 0; i < xdata.length; ++i) {
            chart.getChartData().get(i + 1, 0).setValue(xdata[i]);
            chart.getChartData().get(i + 1, 1).setValue(ydata[i]);
            chart.getChartData().get(i + 1, 2).setValue(size[i]);
        }

        //Set series label
        chart.getSeries().setSeriesLabel(chart.getChartData().get("B1", "B1"));

        chart.getSeries().get(0).setXValues(chart.getChartData().get("A2", "A5"));
        chart.getSeries().get(0).setYValues(chart.getChartData().get("B2", "B5"));
        chart.getSeries().get(0).getBubbles().add(chart.getChartData().get("C2"));
        chart.getSeries().get(0).getBubbles().add(chart.getChartData().get("C3"));
        chart.getSeries().get(0).getBubbles().add(chart.getChartData().get("C4"));
        chart.getSeries().get(0).getBubbles().add(chart.getChartData().get("C5"));

        //Save the document
        ppt.saveToFile("CreateChart.pptx", FileFormat.PPTX_2013);
        ppt.dispose();
    }
} 
```

[![Create chart](img/959c38c6aa55d48d06883a255681e723.png "Create chart")](https://res.cloudinary.com/practicaldev/image/fetch/s--af0j6Hi0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iapmngcblhrotbzg5t1t.png)

# 设置文档属性为演示文稿

以下示例显示了如何为演示文稿设置文档属性，如作者、公司、关键字、注释、类别、标题和主题。

```
public class SetDocumentProperties {
    public static void main(String[] args) throws Exception {
        //Create a Presentation instance
        Presentation ppt = new Presentation();

        //Set the DocumentProperty of PPT document
        ppt.getDocumentProperty().setApplication("Spire.Presentation");
        ppt.getDocumentProperty().setAuthor("E-iceblue");
        ppt.getDocumentProperty().setCompany("E-iceblue Co., Ltd.");
        ppt.getDocumentProperty().setKeywords("Demo File");
        ppt.getDocumentProperty().setComments("This file is used to test Spire.Presentation.");
        ppt.getDocumentProperty().setCategory("Demo");
        ppt.getDocumentProperty().setTitle("This is a demo file.");
        ppt.getDocumentProperty().setSubject("Test");

        //Save the document
        ppt.saveToFile("SetProperties.pptx", FileFormat.PPTX_2013);
        ppt.dispose();
    }
} 
```

[![Set document properties](img/d367a6b2c616183729f56a466acbd42a.png "Set document properties")](https://res.cloudinary.com/practicaldev/image/fetch/s--YkPKUqQH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1im8jbcq8k8eudwyqrg.png)

# 用密码保护演示文稿

以下示例显示了如何用密码保护演示文稿。

```
public class ProtectPresentation {
    public static void main(String[] args) throws Exception {
        //Create a Presentation instance
        Presentation ppt = new Presentation();

        //Protect presentation with password
        String strPassword = "pwd123";
        ppt.encrypt(strPassword);

        //Save the document
        ppt.saveToFile("Protected.pptx", FileFormat.PPTX_2013);
        ppt.dispose();
    }
} 
```

[![Protect presentation](img/c700de7f464a3f893ee35aef1996e0e4.png "Protect presentation")](https://res.cloudinary.com/practicaldev/image/fetch/s--YPLHzGMM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s748jiww8relcm5efstu.png)