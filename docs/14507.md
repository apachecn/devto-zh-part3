# python 中如何通过图像或摄像机检测人脸

> 原文：<https://dev.to/mkhy19/detect-your-face-through-image-or-video-camera-in-python-55em>

## 人脸检测与识别是一项重要的技术，应用于各种应用中，比如我的毕业设计——学生在教室里的考勤。

人脸识别过程可以分为两个主要阶段:人脸检测和人脸识别。人脸检测是知道物体在哪里的过程，人脸识别是从数据库中匹配和提取物体。

*   人脸检测:
    是人脸识别的预处理。该步骤的主要功能是确定人脸是否出现在给定的图像中，以及这些人脸位于何处。除了作为人脸识别的预处理，人脸检测还可以用于感兴趣区域检测和图像分类。

*   人脸识别:
    将每张人脸的表示形式化后，第二步就是识别这些人脸的身份。为了实现自动识别，需要建立人脸数据库。对于每个物体/人，拍摄若干图像，提取它们的特征并存储在数据库中。然后，当输入人脸图像进入时，我们执行人脸检测和特征提取，并将其特征与数据库中存储的每个人脸类别进行比较。

在本次讨论中，我们将讨论人脸检测，并将这种检测应用于任何图像或使用相机

## 首先，通过图像检测你的脸

首先我们必须导入我们的库，numpy 和 cv2

```
import cv2
import numpy as np 
```

然后，我们必须下载具有某些功能的 xml 文件来使用它。我们可以从打开的 cv 文件、源代码、数据和 haarcascades 中得到它，这些文件具有所有的特性
C:\ opencv \ sources \ data \ Haar cascades

在这里，我们想检测正面和眼睛，所以我们将复制这个 xml 文件与我们的项目

```
face_cascade = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
eye_cascade = cv2.CascadeClassifier('haarcascade_eye.xml') 
```

下载我们的图像。然后将其转换为灰度图像

```
img = cv2.imread('mm.jpg')
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY) 
```

然后，我们必须检测图像中与 xml 文件中的所有特征相匹配的所有人脸。所以我们将使用 detectMultiScale 函数，它有三个参数(我们的图像，比例因子，minNeighbors )

```
faces = face_cascade.detectMultiScale(gray, 1.3, 5) 
```

在我们的图像中检测到任何人脸后，它将返回左上角(x，y)，高度和宽度。然后，我们必须为每个面绘制矩形。
cv2.rectangle 取我们的图像，左上，右上，颜色和粗细

```
for (x,y,w,h) in faces:
    cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2) 
```

我们把图像放在感兴趣的区域。换句话说，我们定义了脸。

```
 roi_gray = gray[y:y+h, x:x+w]
    roi_color = img[y:y+h, x:x+w] 
```

因为每张脸有两只眼睛，所以我们在我们感兴趣的区域搜索两只眼睛。然后对于每只眼睛，我们画出我们的矩形。

```
 eyes = eye_cascade.detectMultiScale(roi_gray)

    for (ex,ey,ew,eh) in eyes:
        cv2.rectangle(roi_color,(ex,ey),(ex+ew,ey+eh),(0,255,0),2) 
```

最后我们展示我们的图像

```
cv2.imshow('img',img)
cv2.waitKey(0)
cv2.destroyAllWindows() 
```

最后，看这个视频。[https://www.youtube.com/embed/I4QkzbL61Wk](https://www.youtube.com/embed/I4QkzbL61Wk)

如果你想通过摄像头检测你的脸。这里的也是同样的想法

### [这里的](https://github.com/mkhy19/Crash-courses/tree/master/Computer%20vision/face%20detection%20-%20openvc)是回购，可以在 python 中找到源代码。