# 解决标签问题

> 原文：<https://dev.to/smakosh/solving-hashtags-problems-5a7i>

> 这篇文章最初发表在我的博客上

Hashtag ~hashtags 是复数名词~是任何以“#”开头的短语或单词，它主要用于社交媒体平台，以针对特定主题，并聚集对该主题感兴趣的用户。

因为我们将“hashtags”作为主题，所以您可以使用“#hashtags”作为本文的主题。

到目前为止，标签本身没有问题，但是很少用户使用标签的方式，不管他们是过度使用还是伪造标签，最终所有用户都会感到恼火，比如当你滚动浏览与“技术”相关的帖子时，你最终会发现许多无关的帖子，这不会让你恼火吗？

当你试图阅读某人的帖子描述时，情况也是如此，但它充满了他们滥用的标签，这就是为什么最好有一些换行符或把它们放在第一条评论中。

少数平台已经通过警告用户并最终禁止标签来解决标签的过度使用问题，不像使用不相关标签的问题，这个问题还没有解决，这就是为什么我建立了 [Ai 标签](https://play.google.com/store/apps/details?id=com.aihashtags)来解决它！

Ai Hashtags 使用人工智能生成 Hashtags，其工作原理如下:

1.  上传你的图片，分享或粘贴你的 Instagram 帖子的链接
2.  点击生成标签按钮
3.  选择特定的标签来复制或全部复制
4.  将它们粘贴到您的帖子中

您可以为您的标签创建一个新的组，以便将来重复使用，每个组都可以编辑或删除。

该应用程序支持英语、西班牙语、简体中文和阿拉伯语(我们将很快添加更多语言)，可在 [Play Store](https://play.google.com/store/apps/details?id=com.aihashtags) 和 [App Store](https://itunes.apple.com/us/app/ai-hashtags/id1460674572?mt=8) 获得。

Android 应用是免费的，但带有用户友好的广告，而 iOS 应用是免费的，但价格为 0.99 美元。

这是一个有趣的项目，我建立它的原因可以追溯到我和我的团队[赢得在线 JAMStack FreeCodeCamp 黑客马拉松](https://medium.freecodecamp.org/winners-from-the-2018-freecodecamp-jamstack-hackathon-at-github-2a39bd1db878)的二等奖时，我们使用 [ClarifAi](https://clarifai.com) API 从你试图出售的产品图片中自动生成标签，这样你就可以轻松地在社交媒体上分享它，并收集对特定产品感兴趣的实际用户。ClarifAi 让我使用他们的社区 API Key，免费提供第一个 5k 请求，然后我不得不使用基本计划，费用为 1.20 美元/ 1k 操作，在我看来这是一个合理的价格。

## 工具

*   我用 Adobe Illustrator 设计了手机图标
*   Adobe XD 设计线框和用户界面
*   使用 Trello 更好地处理项目管理的看板精益方法
*   使用 React Native 开发:(0.60.5)
    *   导航:react-navigation(因为这个应用不是很大，否则我会使用 react-native-navigation)
    *   样式:样式组件
    *   部署:浪子
    *   环境变量:react-native-config
    *   状态管理: ~~redux~~ useReducer 和 useContext！
    *   hoc:~~重新组合~~不再有 hoc
    *   HTTP 请求:axios
    *   表单:formik
    *   表单验证:是
    *   广告:react-native-admob
    *   toast:react-native-simple-toast
    *   上传:反应-原生-照片-上传
    *   Swiper: react-native-swiper
    *   中间件:不再是了
    *   更漂亮&林挺的 Eslint 格式化我的代码
    *   VSCode 作为代码编辑器
    *   图像识别:clarifai
    *   i18n:反应国际
    *   分析:react-native-mixpanel

请随意下载人工智能标签，并在应用程序上或评论中留下您的反馈:[下载人工智能标签](https://ai-hashtags.com)

> 乔恩·泰森在 Unsplash
> 网站上的照片是和盖茨比一起拍的