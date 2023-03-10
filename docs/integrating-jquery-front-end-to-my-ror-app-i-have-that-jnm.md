# 将 JQuery 前端集成到我的 RoR 应用程序(我有！)

> 原文：<https://dev.to/emiko/integrating-jquery-front-end-to-my-ror-app-i-have-that-jnm>

# 将 JQuery 前端集成到我的 RoR App(我有那个！)

# 开头

为了这个最后的项目，我决定打开我的 Rails 应用，[我有了！](https://github.com/emikojima/i-have-that-v2)一个社区借贷 app，变成一个有 JQuery 前端的 app。要阅读更多关于该应用程序及其功能的信息，请点击[这里。](http://emikojima.com/rails_final_project_i_have_that_v_2)。

我为这个项目奋斗了很久。我在这项任务中遇到的最大挑战之一是理论上的。为什么我应该在哪些页面视图上集成 JQuery 前端，在满足[项目需求](https://learn.co/tracks/full-stack-web-development-v6/rails-and-javascript/project-mode/rails-app-with-a-jquery-front-end#requirements)的同时，我的选择会对用户体验产生什么样的推理和影响。我遇到的下一个心理障碍是思考如何调整没有考虑不同前端集成的现有代码。

我将每个问题都写在一个要点列表中:

*   JQuery 和 AJAX 可以替代哪些页面视图来优化用户体验？
*   我的 JQuery 前端会完全取代我的 rails 前端吗？
*   我的选择会对我的应用程序整体和用户体验产生什么影响？

带着这些问题，我开始观看一些来自熨斗学校优秀技术教练的视频教程:

*   [用 AJAX 提交表单](https://www.youtube.com/watch?v=XxzayZma5Ew)
*   [JQuery 办公时间](https://instruction.learn.co/student/video_lectures#/146)
*   [JQuery JS API 实验室](https://www.youtube.com/watch?v=G9K7-lDXXss)

# 流程

观看这些视频让我对从哪里以及如何开始构建我的新 Javascript (JS)前端有了一个很好的想法。我决定做的第一件事是绘制出我想要动态加载到 DOM 的页面视图。

我的应用程序中的“社区”页面(`users#index`)列出了该应用程序的所有用户及其位置。每个用户名都链接到他们的`users#show`页面，点击链接会将你重定向到显示用户列出的借出物品的页面。我知道我想劫持这个点击事件，使用 AJAX 显示用户的条目，并动态添加一个`NEXT`按钮，这样每次按钮被按下时，DOM 就会更新以显示下一个用户的信息和条目。

为了完成这项任务，我列了一张待办事项清单。

1.  生成用户[序列化器](https://github.com/rails-api/active_model_serializers)
2.  生成`users.js`(删除`users.coffee`)
3.  使在应用程序布局中注入数据。
4.  劫持点击事件。
5.  向 AJAX 请求数据。*编辑用户控制器`show`动作以呈现 JSON
6.  解析数据——也许创建类和原型？
7.  Inject data into appropriate along with a button for `NEXT`

    然后，我制作了一个待办事项列表来完成劫持下一个按钮并显示下一个用户的任务。

    1.  弄清楚获取下一个用户的方法或函数放在哪里(user.rb，users.js，users_controller？)
    2.  找出获取下一个用户的方法或函数
    3.  将 user :id 传递给 NEXT 按钮函数
    4.  向 AJAX 请求数据
    5.  删除 DOM
    6.  用`NEXT`按钮将新数据注入 DOM。

    一旦我完成了这个，我就继续我的下一个任务，以此类推，直到我创建了一个非常接近单页面应用(SPA)的应用——除了我的登录和注销页面，我的应用都是 JQuery 和 AJAX，没有重定向。

    # 外卖

    在创建了一个非常接近 SPA 的表兄弟(见下文)后，我开始意识到 SPA 的局限性，并阅读了一些文章(比如这篇伟大的 [one](https://medium.com/@pshrmn/demystifying-single-page-applications-3068d0555d46) )揭露了我所编写代码的缺点。我还读了这篇很棒的[文章](http://voidcanvas.com/should-i-use-ajax-if-yes-when-where-and-why/)，它讲述了 AJAX 的最佳用途(何时何地使用 AJAX)以及使用 AJAX 的缺点，我真希望在开始这个项目之前读过这篇文章！

    另一个重要的收获是计划是无价的 T2。例如，我在我的`items#index`视图上有一个条目列表，它通过名称、描述、条目所属的用户以及条目是否可以借用来呈现每个条目。我有项目名称

    ### 中的其余信息

    。所以当我决定有一个搜索字段，因为我有其他文本

    ### 在 DOM 中，我很难为我的搜索功能分离出项目名称

    ### 。尽管修复相对容易——我不得不将

    ### 放在

    和中
8.  -这使我不得不返回并编写代码，而我本应该在
9.  as basic protocol. You never know how your app will need to expand (what it will need to integrate, what functionality will need to be added) and it is always good practice to be neat, write clean code, and be smart about your HTML markup (another big takeaway - give `id`s or `class`es to your s)!

    我喜欢编码的一点是，那里有如此丰富的信息，因为那里有如此慷慨的程序员社区，你看得越多，要学习的就越多。虽然这有时会让人感到不知所措，但它也让人感觉如此富有成效和令人兴奋。

    这里是最终产品的视频演示。

    前进！

    快乐编码。