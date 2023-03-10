# 我如何制作一个具有人脸检测功能的 Instagram 机器人。

> 原文：<https://dev.to/reliefs/how-i-made-a-instagram-bot-with-face-detection-2l38>

# 带有自动发布和人脸检测的 Instagram scraper。

我做了一个 Instagram 机器人，它自动抓取人们的个人资料，采用机器学习模块，并将图像重新发布到 Instagram。我是 python 的新手，所以很多基础知识都没学到，所有的反馈都很好。

这可能是我的第三个 python 机器人，我在 Github 上玩了几个不同的社交网络机器人，他们都在同一条路上结束了，喜欢狗的照片，商业广告和其他奇怪的东西，我联系了机器人的创造者，大多数人说这是不可能的，太多的工作。

下载我的 insta gram Bot:
[https://github . com/instagrambot/insta gram-scraper-with-auto post](https://github.com/instagrambot/Instagram-scraper-with-autopost)

## 我是如何实现人脸检测的

所以我最终只用 2 行代码自己实现了它。

```
 image = face_recognition.load_image_file(instapath)
    face_locations = face_recognition.face_locations(image)
    # If no face located scrape the next profile
    if not face_locations:
        print("There is no Face Detected scraping next profile")
        x += 1
        instascraper()
    else:
        print("There is a Face Detected scraping and posting this image") 
```

### 通过网络摄像头实时检测工作中的人脸

[![Instagram Scraper](img/be0c43929751ffc6373504319c9e78ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qdvR8Vl8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cloud.githubusercontent.com/assets/896692/24430398/36f0e3f0-13cb-11e7-8258-4d0c9ce1e419.gif)

我已经在几天内自己制作了 scraper/reposter，所以实现这个代码，它就工作了..太完美了

我的脚本现在有几个第三方包，看到实现不同的模块是多么容易，这真的很有趣。我认为 Python 是一种非常有趣的语言，有无限的可能性。

我的 Instagram 刮刀网址:
[https://instabotai.com](https://instabotai.com)

下载我的 insta gram Bot:
[https://github . com/instagrambot/insta gram-scraper-with-auto post](https://github.com/instagrambot/Instagram-scraper-with-autopost)