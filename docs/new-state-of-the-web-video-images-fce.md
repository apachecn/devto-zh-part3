# 新的“网络状态”视频:图像！

> 原文：<https://dev.to/chromiumdev/new-state-of-the-web-video-images-fce>

今天，我在我的“网络现状”系列中发布了一个新视频，在视频中，我与来自 Cloudinary 的科林·本德尔谈论了图像的现状。在这个 47 分钟的视频中，我们涉及了*很多*的内容，所以我做了这个谈话要点的书面总结。希望你喜欢！

[https://www.youtube.com/embed/nEyvFpz5A6Q](https://www.youtube.com/embed/nEyvFpz5A6Q)

*   布局稳定性的图像加载技术
    *   设置明确的高度/宽度
    *   低质量图像占位符
*   文件格式的传统观点
    *   JPEG 非常适合照片
    *   GIF/PNG 非常适合插图和图标
    *   SVG 非常适合基于矢量的图像
    *   因浏览器支持而异
    *   因透明度要求而异
*   微调图像质量
    *   JPEG 有质量指数(例如 80)
    *   色度子采样
    *   渐进 JPEG
*   为移动友好提供响应图像
    *   一个秘密
    *   图片和 src
    *   媒体查询
    *   笔记本电脑*是*移动设备
*   使用客户端提示了解用户的能力
    *   服务器根据它们可以支持的内容协商返回给客户端的内容
    *   viewport size
    *   连接类型
    *   指纹安全
    *   用户代理字符串不可靠
*   图像在可访问性中的作用
    *   [根据 HTTP 存档和 Lighthouse 数据，50%的图像正确使用了 alt 属性](https://httparchive.org/reports/accessibility#a11yImageAlt)
    *   在缺少图像的 alt 属性的情况下，屏幕阅读器读出 URL
    *   有能力的开发人员可能会忘记可访问性
    *   机器学习系统正致力于生成替代文本
*   人类视觉感知
    *   色盲
    *   感色度
    *   我们视觉优化图像的方式不一定对每个人都是最佳的
    *   图像/色彩保真度的电子商务含义
*   WebP 权衡
    *   比 JPEG 节省约 10-30%的字节
    *   强制色度二次采样可能导致带有文本的图像质量不佳
    *   限于 sRGB 色彩空间
*   动画的最佳实践
    *   动画 gif 真的很糟糕，可以很快增长到 MB
    *   img src 中的 mp4 支持
    *   广告技术行业发现动画/动作对广告来说很棒
    *   使用视频标签很好，但是可能会像在 CMS 上一样受到平台的限制
*   图像页面重量
    *   [根据 HTTP 档案，三分之二的页面由图像字节](https://httparchive.org/reports/state-of-images#bytesImg)组成
    *   以低优先级下载图像
    *   优化 JavaScript 将会产生最大的影响
    *   延迟加载文件夹下的图像
*   自动化图像质量
    *   我们应该依靠算法来确定最佳技术
    *   Squoosh 应用可视化优化的影响
    *   并入 CMS 或构建系统
*   用户生成的图像
    *   优化需要扩大规模，以处理用户可能上传的任何图像
    *   安全问题
*   推荐资源
    *   [阿迪·奥斯马尼的图片指南](http://images.guide)
    *   科林在[客户端上的文章提示](https://cloudinary.com/blog/client_hints_and_responsive_images_what_changed_in_chrome_67)，Safari 中的[图片](https://calendar.perfplanet.com/2017/animated-gif-without-the-gif/)
    *   [web.dev 图像优化策略](https://web.dev/fast#optimize-your-images)