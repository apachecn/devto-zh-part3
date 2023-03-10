# Bootstrap 4 联系人表单使用 Bootstrap 4 创建一个简单有效的联系人表单

> 原文：<https://dev.to/drsavvina/build-a-simple-working-contact-form-with-bootstrap-4-mcf>

[![](img/adab63e9f79d4f6ffec7f2cac8a465d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pw0BMfnl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugs-bunny.net/wp-content/uploads/2019/03/bootstrap4.jpg)

在这篇文章中，你将看到如何只用 HTML5 和 Bootstrap 类构建一个简单而有效的表单。没有 CSS 将是必要的！

使用 Bootstrap 4，您可以通过应用 Bootstrap 类来快速轻松地构建定制组件。您是否想学习如何为您的项目构建现代化的响应组件？Bootstrap 使它变得简单，这篇文章将向你展示如何建立一个简单的联系方式。

最后，您的联系方式应该是这样的:

[https://codepen.io/BlackNina/pen/MxoVqv?editors=1000](https://codepen.io/BlackNina/pen/MxoVqv?editors=1000)

**1。设置引导程序 4**

为了让我们开始在 bootstrap 框架中编码，我们首先需要将相关文件链接到我们的项目，以便我们可以利用所有默认的 bootstrap 风格。

首先，打开你的文本编辑器，我个人更喜欢 VS Code，因为我喜欢它强大的扩展。创建文件 index.html 和样式

**CSS**

在项目的标题标记之间包含此链接:

```
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
```

**2。建立联系表单**

将以下代码复制并粘贴到您的 index.html 文件中

```
<section class="resume-section p-4 p-lg-5 text-center" id="contact">
        <div class="my-auto">
          <h2 class="mb-4">< Contact Information ></h2>

          <ul class="fa-ul mb-4 ml-0">
            <li id="mail-address">
<!--               Replace with your email address -->
                <i class="fas fa-envelope-open mr-2 contact-icons"></i
                >youremail@mail.com</a
              >
            </li>
            <li>
              <i class="fas fa-mobile-alt mr-2 contact-icons"></i>+48 888 888
            </li>
            <li>
              <i class="fas fa-map-marker-alt mr-2 contact-icons"></i>Athens, GR
            </li>
          </ul>

          <p>
            ...or leave your message below and I will be back to you as soon as
            possible.
          </p>

          <form
            class="contact-form d-flex flex-column align-items-center"
            action="https://formspree.io/youremail@mail.com"
            method="POST"
          >
            <div class="form-group w-75">
              <input
                type="name"
                class="form-control"
                placeholder="Name"
                name="name"
                required
              />
            </div>
            <div class="form-group w-75">
              <input
                type="email"
                class="form-control"
                placeholder="Email"
                name="name"
                required
              />
            </div>

            <div class="form-group w-75">
              <textarea
                class="form-control"
                type="text"
                placeholder="Message"
                rows="7"
                name="name"
                required
              ></textarea>
            </div>

            <button type="submit" class="btn btn-submit btn-info w-75">Submit</button>
          </form>
        </div>
      </section>
```

将以下脚本复制并粘贴到

```
<script
      defer
      src="https://use.fontawesome.com/releases/v5.7.2/js/all.js"
      integrity="sha384-0pzryjIRos8mFBWMzSSZApWtPl/5++eIfzYmTgBBmXYdhvxPc+XcFEk+zJwDgWbP"
      crossorigin="anonymous"
    ></script>
```

正如你在上面看到的，我已经使用 Bootstrap 工具来定制一些元素，而不是使用 CSS。例如，w-75 类将表单字段的宽度设置为 75%。btn-info 类负责我们的提交按钮的汽油蓝色。事实上，在定制元素的引导工具中，我们有很多选择。不使用 CSS。

你可以在官方文档中读到更多关于引导类和工具的内容。

我使用了 [Formspree](https://formspree.io/) 来使联系表单工作。不需要 PHP、JS 或注册。你只需要把 youremail@mail.com 替换成你的电子邮件地址就可以收到提交的邮件了。

感谢阅读！我希望这有所帮助。如果你有任何反馈或建议，我很乐意在评论中与你交谈。