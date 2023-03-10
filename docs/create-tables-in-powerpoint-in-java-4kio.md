# 用 Java 在 PowerPoint 中创建表格

> 原文：<https://dev.to/eiceblue/create-tables-in-powerpoint-in-java-4kio>

电子文档中的表格通常用于显示表格数据。然而，在某些特定类型的文档中，如 PowerPoint 和 HTML，表格也可以用于创建现代平面图形。

本文将向您展示如何使用 [Free Spire 在 PowerPoint 文档中创建一个普通的表格和一个时尚的表格图形。Java 的演示文稿](https://www.e-iceblue.com/Download/presentation-for-java-free.html)。

## 创建普通表格

```
import com.spire.presentation.*;

public class NormalTable {

    public static void main(String[] args) throws Exception {

        //create a Presentation instance
        Presentation presentation = new Presentation();
        presentation.getSlideSize().setType(SlideSizeType.SCREEN_16_X_9);

        //define column widths and row heights
        Double[] widths = new Double[]{100d, 100d, 100d, 150d, 100d,100d};
        Double[] heights = new Double[]{15d, 15d, 15d, 15d, 15d};

        //add a table
        ITable table = presentation.getSlides().get(0).getShapes().appendTable((float)(presentation.getSlideSize().getSize().getWidth() - 650)/2, 40, widths, heights);

        //define data
        String[][] data = new String[][]
                {
                        {"Emp#", "Name", "Job", "HiringD", "Salary", "Dept#"},
                        {"7369", "Andrews", "Engineer", "05/12/2006", "1600.00", "20"},
                        {"7499", "Nero", "Technician", "03/23/2006", "1800.00", "30"},
                        {"7521", "Adams", "Technician", "11/03/2007", "2400.00", "30"},
                        {"7566", "Martin", "Manager", "12/21/2007", "2600.00", "10"},
                };

        for (int i = 0; i < data.length; i++) {
            for (int j = 0; j < data[i].length; j++) {

                //fill the table with data
                table.get(j, i).getTextFrame().setText(data[i][j]);

                //align text in each cell to center
                table.get(j, i).getTextFrame().getParagraphs().get(0).setAlignment(TextAlignmentType.CENTER);
            }
       }

        //apply built-in table style
        table.setStylePreset(TableStylePreset.DARK_STYLE_2_ACCENT_3_ACCENT_4);

        //save the document
        presentation.saveToFile("output/NormalTable.pptx", FileFormat.PPTX_2013);
    }
} 
```

输出:
[![Format text content](img/2385b91b0a741ed47fb745f98034b373.png "Format text content")](https://res.cloudinary.com/practicaldev/image/fetch/s--4CcZYzJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qo7zbrnkgan165krk85.png)

## 创建表格图形

```
import com.spire.presentation.*;
import com.spire.presentation.drawing.FillFormatType;
import com.spire.presentation.drawing.IImageData;
import com.spire.presentation.drawing.PictureFillType;

import javax.imageio.ImageIO;
import java.awt.*;
import java.io.FileInputStream;
import java.util.ArrayList;

public class TableGraphic {

    public static void main(String[] args) throws Exception {

        //create a Presentation instance
        Presentation presentation = new Presentation();
        presentation.getSlideSize().setType(SlideSizeType.SCREEN_16_X_9);

        //define column widths and row heights
        Double[] widths = new Double[]{150d, 150d, 150d, 150d};
        Double[] heights = new Double[]{120d, 50d, 20d, 80d, 3d};

        //add a table
        ITable table = presentation.getSlides().get(0).getShapes().appendTable((float) (presentation.getSlideSize().getSize().getWidth() - 600) / 2, (float) (presentation.getSlideSize().getSize().getHeight() - 273) / 2, widths, heights);

        //set the table style to none
        table.setStylePreset(TableStylePreset.NONE);

        //create an arraylist of colors
        ArrayList<Color> colorList = new ArrayList<Color>();
        colorList.add(new Color(255, 69, 0));
        colorList.add(new Color(70, 130, 180));
        colorList.add(new Color(255, 140, 0));
        colorList.add(new Color(34, 139, 34));

        //create an arraylist of image data
        ArrayList<IImageData> imageData = new ArrayList<IImageData>();
        imageData.add(presentation.getImages().append(ImageIO.read(new FileInputStream("C:\\Users\\Administrator\\Desktop\\Question-mark.png"))));
        imageData.add(presentation.getImages().append(ImageIO.read(new FileInputStream("C:\\Users\\Administrator\\Desktop\\Explore-mark.png"))));
        imageData.add(presentation.getImages().append(ImageIO.read(new FileInputStream("C:\\Users\\Administrator\\Desktop\\Plan-mark.png"))));
        imageData.add(presentation.getImages().append(ImageIO.read(new FileInputStream("C:\\Users\\Administrator\\Desktop\\Go-mark.png"))));

        //create two string array to store text
        String[] subtitles = new String[]{"KNOW", "EXPLORE", "PLAN", "GO!"};
        String[] details = new String[]{
                "Who am I? Get to know your interests, skills, and strengths.",
                "Where do I want to go? Explore career pathways and education opportunities.",
                "How do I get there? Set your route! Set goals, choose courses, join clubs and activities, write resumes, obtain financing.",
                "Recalculate as needed."
        };

        //loop through the columns
        for (int i = 0; i < 4; i++) {

            //fill the cells of the first row with background colors
            table.get(i, 0).getFillFormat().setFillType(FillFormatType.SOLID);
            table.get(i, 0).getFillFormat().getSolidColor().setColor(colorList.get(i));
            //insert formatted text to the cells of the first row
            table.get(i, 0).getTextFrame().setText(String.format("0%d", i + 1));
            table.get(i, 0).getTextFrame().getParagraphs().get(0).setAlignment(TextAlignmentType.CENTER);
            table.get(i, 0).setTextAnchorType(TextAnchorType.CENTER);
            table.get(i, 0).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).setLatinFont(new TextFont("Calibri"));
            table.get(i, 0).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).setFontHeight(60f);
            table.get(i, 0).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).getFill().setFillType(FillFormatType.SOLID);
            table.get(i, 0).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).getFill().getSolidColor().setColor(Color.white);

            //fill the cells of the second row with pictures
            table.get(i, 1).getFillFormat().setFillType(FillFormatType.PICTURE);
            table.get(i, 1).getFillFormat().getPictureFill().setFillType(PictureFillType.STRETCH);
            table.get(i, 1).getFillFormat().getPictureFill().getPicture().setEmbedImage(imageData.get(i));

            //insert formatted text to the cells of the third row
            table.get(i, 2).getTextFrame().setText(subtitles[i]);
            table.get(i, 2).getTextFrame().getParagraphs().get(0).setAlignment(TextAlignmentType.CENTER);
            table.get(i, 2).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).setLatinFont(new TextFont("Calibri"));
            table.get(i, 2).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).setFontHeight(20f);
            table.get(i, 2).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).getFill().setFillType(FillFormatType.SOLID);
            table.get(i, 2).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).getFill().getSolidColor().setColor(colorList.get(i));

            //insert formatted text to the cells of the fourth row
            table.get(i, 3).getTextFrame().setText(details[i]);
            table.get(i, 3).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).setLatinFont(new TextFont("Calibri"));
            table.get(i, 3).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).setFontHeight(11f);
            table.get(i, 3).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).getFill().setFillType(FillFormatType.SOLID);
            table.get(i, 3).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).getFill().getSolidColor().setColor(new Color(112,128,144));

            //fill the cells of the fifth row with background colors
            table.get(i, 4).getFillFormat().setFillType(FillFormatType.SOLID);
            table.get(i, 4).getFillFormat().getSolidColor().setColor(colorList.get(i));
            //set the font size of the fifth row smaller than the row height
            table.get(i, 4).getTextFrame().getParagraphs().get(0).getTextRanges().get(0).setFontHeight(2f);

            //loop through the rows
            for (int j = 0; j < 5; j++) {

                //set the border style of each cell to none
                table.get(i, j).getBorderTop().setFillType(FillFormatType.NONE);
                table.get(i, j).getBorderBottom().setFillType(FillFormatType.NONE);
                table.get(i, j).getBorderLeft().setFillType(FillFormatType.NONE);
                table.get(i, j).getBorderRight().setFillType(FillFormatType.NONE);

                //fill the third and the fourth row with background color
                if (j != 0 & j!=1 & j != 4) {
                    table.get(i, j).getFillFormat().setFillType(FillFormatType.SOLID);
                    table.get(i, j).getFillFormat().getSolidColor().setColor(new Color(248, 248, 255));
                }

            }
        }

        //save to file
        presentation.saveToFile("output/GriddedGraphic.pptx", FileFormat.PPTX_2013);
    }

} 
```

输出:
[![](img/491d0c70dcaa6d13dc541859a1d11fbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OWFJMyOD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tltokv1ovh18ogib29ab.png)