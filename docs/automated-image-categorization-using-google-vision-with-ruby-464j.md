# Ruby on Rails:使用 Google Vision API 的自动图像分类

> 原文：<https://dev.to/botreetech/automated-image-categorization-using-google-vision-with-ruby-464j>

[![Automated Image Categorization using Google Vision with Ruby](img/7b0eb00c5a8f5dd13178c24955d46fde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aRtqQGkd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.botreetechnologies.com/blog/wp-content/uploads/2019/05/Automated-Image-Categorization-using-Google-Vision-with-Ruby.png)

现在，有很多情况下你需要对用户上传的图片进行分类，并对其进行标记，以防止不需要的内容进入网站。我们已经为我们的一个[电子商务](https://www.botreetechnologies.com/ecommerce-app-development)客户建立了一个解决方案，来检测上传的时装照片的颜色和风格。

谷歌一直在大力倡导人工智能和人工智能，并免费提供了许多工具集来启动这一旅程。Google Vision 提供的 API 可以检测图像中的各种类别，从不同的交通方式到动物等等。

在[之前的文章](https://www.botreetechnologies.com/blog/integrating-google-cloud-vision-api-with-ruby-for-image-object-detection)中，我们已经看到了如何设置 [Ruby on Rails Development](https://www.botreetechnologies.com/blog/ruby-on-rails-development-obstacles-opportunities) 来访问 Google Vision API。我还分享了另一篇关于使用相同 API 从图像中提取[文本的帖子。在本文中，我们将看到如何使用 Google vision API 对图像进行分类。](https://www.botreetechnologies.com/blog/extracting-text-from-image-using-google-cloud-vision-ocr-with-ruby?fbclid=IwAR0P6V2njx5VMwyozsNllDCgBaL_IUnEVYuVuA4t7GBRY9yHrg-PzCc2UGQ)

下面是一个小的 Ruby 脚本，展示了我们如何检测用户护照身份图像的标签。谷歌将其归类为“身份证件”、“脸”、“皮肤”、“鼻子”、“下巴”、“执照”。