# 在 Java 中对所有幻灯片应用背景图像

> 原文：<https://dev.to/eiceblue/apply-background-image-to-all-slides-in-java-58mf>

[自由的尖顶。Java 的 Presentation】提供了一个 SlideBackground 类来处理幻灯片的背景。通过使用它，你可以用纯色填充、渐变填充、图片填充或图案填充来设置幻灯片背景。](https://www.e-iceblue.com/Download/presentation-for-java-free.html)

在这篇文章中，你将学习两种将背景图像应用到现有 PowerPoint 演示文稿中所有幻灯片的方法。第一种方法是使用 for 循环来设置每张幻灯片的背景，第二种方法是使用主幻灯片。主幻灯片不仅可以保留背景，还可以保留您想要在所有幻灯片上显示的图像(例如，公司徽标)或文本。

下面是输入的 PowerPoint 文件的样子。

[![Format text content](img/f1058063e9cf2a7c47ab4a865945cb01.png "Format text content")](https://res.cloudinary.com/practicaldev/image/fetch/s--GR2_Vooo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z6fyh7pc4qn6jyffrghs.jpg)

## 方法 1。使用 for 循环

```
import com.spire.presentation.FileFormat;
import com.spire.presentation.Presentation;
import com.spire.presentation.SlideBackground;
import com.spire.presentation.drawing.*;

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.io.FileInputStream;

public class AppplyBgToAllSlides {

    public static void main(String[] args) throws Exception {

        //load a PowerPoint file
        Presentation presentation = new Presentation();
        presentation.loadFromFile("C:\\Users\\Administrator\\Desktop\\input.pptx");

        //get the image data
        BufferedImage image = ImageIO.read(new FileInputStream("C:\\Users\\Administrator\\Desktop\\bg.jpg"));
        IImageData imageData = presentation.getImages().append(image);

        //loop through the slides
        for (int i = 0; i < presentation.getSlides().getCount() ; i++) {

            //apply the image to the certain slide as background
            SlideBackground background = presentation.getSlides().get(i).getSlideBackground();
            background.setType(BackgroundType.CUSTOM);
            background.getFill().setFillType(FillFormatType.PICTURE);
            background.getFill().getPictureFill().setFillType(PictureFillType.STRETCH);
            background.getFill().getPictureFill().getPicture().setEmbedImage(imageData);
        }

        //save to file
        presentation.saveToFile("output.pptx", FileFormat.PPTX_2013);
        presentation.dispose();
    }
} 
```

[![Format text content](img/9348b26e242f7021ea108b5ccc67dcb7.png "Format text content")](https://res.cloudinary.com/practicaldev/image/fetch/s--zxJfKXU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nq0bwe8xo5qo5hso7vdz.jpg)

## 方法二。使用母版幻灯片

```
import com.spire.presentation.*;
import com.spire.presentation.drawing.BackgroundType;
import com.spire.presentation.drawing.FillFormatType;
import com.spire.presentation.drawing.IImageData;
import com.spire.presentation.drawing.PictureFillType;

import javax.imageio.ImageIO;
import java.awt.geom.Rectangle2D;
import java.awt.image.BufferedImage;
import java.io.FileInputStream;

public class ApplySlideMaster {

    public static void main(String[] args) throws Exception  {

        //load a PowerPoint file
        Presentation presentation = new Presentation();
        presentation.loadFromFile("C:\\Users\\Administrator\\Desktop\\input.pptx");

        //get the first slide master
        IMasterSlide masterSlide = presentation.getMasters().get(0);

        //set the background image of the slide master
        String backgroundPic = "C:/Users/Administrator/Desktop/bg.jpg";
        BufferedImage image = ImageIO.read(new FileInputStream(backgroundPic));
        IImageData imageData = presentation.getImages().append(image);
        masterSlide.getSlideBackground().setType(BackgroundType.CUSTOM);
        masterSlide.getSlideBackground().getFill().setFillType(FillFormatType.PICTURE);
        masterSlide.getSlideBackground().getFill().getPictureFill().setFillType(PictureFillType.STRETCH);
        masterSlide.getSlideBackground().getFill().getPictureFill().getPicture().setEmbedImage(imageData);

        //add an image (company logo) to the slide master
        String logo = "C:/Users/Administrator/Desktop/logo.png";
        image = ImageIO.read(new FileInputStream(logo));
        imageData = presentation.getImages().append(image);
        IEmbedImage imageShape = masterSlide.getShapes().appendEmbedImage(ShapeType.RECTANGLE,imageData,new Rectangle2D.Float(40,40,200,60));
        imageShape.getLine().setFillType(FillFormatType.NONE);

        //save to file
        presentation.saveToFile("output.pptx", FileFormat.PPTX_2013);
        presentation.dispose();
    }
} 
```

[![Format text content](img/37a5f07c945605fff3038e9982c841d8.png "Format text content")](https://res.cloudinary.com/practicaldev/image/fetch/s--qTK0Dm3i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/md3vh3y687ot4c5azb2g.jpg)