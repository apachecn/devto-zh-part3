# 如何提高 OCR 准确率？

> 原文：<https://dev.to/devabanoub/how-to-improve-ocr-accuracy-1ee8>

我花了几天时间开发 Android 应用程序，以识别图像上的英文文本，让用户轻松复制/共享文本。我在 Google Play 上发布了该应用的[测试版。](https://play.google.com/store/apps/details?id=com.abanoubhanna.ocrit)

用户界面很糟糕，但我想首先提高应用程序的准确性。我在考虑两种方法:

1.  改进 tesseract **traineddata** 文件，以更好地识别文本。
2.  使用一个错误识别字典，该字典替换像`boxmg`到`boxing`这样的单词，因为`in`被错误地检测为`m`。

如果你对这个 [OCR app](https://play.google.com/store/apps/details?id=com.abanoubhanna.ocrit) 有任何进一步的改进和建议，请告诉我。

谢谢你的努力！