# 在 Wordpress 中处理 CSS 响应

> 原文：<https://dev.to/onmyway133/dealing-with-css-responsiveness-in-wordpress-4m6g>

在 [LearnTalks](https://medium.com/swlh/introducing-learn-talks-awesome-conference-and-meetup-talks-e97c8cf7e0f) 的 alpha 测试中，我的一些朋友反映在搜索页面中屏幕完全空白，而且这种情况只发生在移动端。这篇文章是我如何识别问题并找到解决问题的方法，它可能不是解决方案，但至少屏幕不再出现空白。

作为一个喜欢通过观看会议视频来跟上科技发展的人，我认为收集所有这些视频是一个好主意，以便以后更好地搜索和探索。所以我用 React 和 Firebase 做了一个 web 应用，目前还在进行中。由于时间限制，我决定先用 Wordpress 来快速实现这个想法。所以有 LearnTalks。

我用的主题卤汁不想正确渲染搜索页面。因此，我转向 Chrome 开发工具的响应视窗模式和 Safari 的 Web Inspector

[![a1](img/f1a756c461f1d50581da461bacfab482.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CkqB-B3a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/2284279/56269409-e5c93900-60f3-11e9-9ce4-b5094464b659.png)

[![a2](img/d5ef63429cad3118aedb552160e5f194.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NKzPBjKZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/2284279/56269433-ef52a100-60f3-11e9-9bad-f3bfd5dca95c.png)

测试表明，问题只发生在特定的屏幕分辨率上。这一定是由于 CSS @media query 为不同的屏幕尺寸显示不同的布局。不知何故，它不适合某些尺寸。

```
@media screen and (max-width: 800px) {
 #site-header {
 display: none;
 }
} 
```

## 稍微讲一下规则

在媒体查询中使用`@media`规则，为不同的媒体类型/设备应用不同的样式。

媒体查询可用于检查许多事情，例如:

视窗的宽度和高度
设备的宽度和高度
方向(平板电脑/手机是横向模式还是纵向模式？)
解析
使用媒体查询是一种流行的技术，用于向台式机、笔记本电脑、平板电脑和手机交付定制的样式表(响应式 web 设计)。

所以我去 Wordpress `Dashboard -> Appearance -> Editor`查看 CSS 文件。不出所料，有一堆媒体提问

```
@media (min-width: 600px) and (max-width: 767px) {
 .main-navigation {
 padding: 0;
 }
 .site-header .pushmenu {
 margin-top:0px; 
 } 
 .social-networks li a {
 line-height:2.1em !Important; 
 } 
 .search {
 display: none !important;
 }
 .customer blockquote {
 padding: 10px;
 text-align: justify;
 }
} 
```

`.search`选择器显示可疑:无！重要；

这个重要的指令非常重要

> 本质上，它意味着它所说的；“这很重要，忽略后续规则，以及任何常见的特殊性问题，应用此规则！”
> 
> 在正常使用中，外部样式表中定义的规则被文档头中定义的样式所支配，而文档头中定义的样式又被元素本身中的内嵌样式所支配(假设选择器具有相同的特性)。用`!important 'attribute' (?)`定义一个规则抛弃了关于“较晚的”规则覆盖“较早的”规则的正常考虑。

幸运的是，这个主题允许编辑 CSS，所以我可以将它覆盖为一个 block 属性，以便总是显示搜索页面

[![a3](img/4bf0b5fb903a3f11421e6e02ee109a96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NLyRWk1P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/2284279/56269449-f4afeb80-60f3-11e9-9032-89647c02d35c.png)

原帖[https://github.com/onmyway133/blog/issues/208](https://github.com/onmyway133/blog/issues/208)