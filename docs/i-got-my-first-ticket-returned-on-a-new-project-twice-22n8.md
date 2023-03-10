# 我在一个项目上拿到了我的第一张返程票。两次。

> 原文：<https://dev.to/clovis1122/i-got-my-first-ticket-returned-on-a-new-project-twice-22n8>

[![So you're telling me you're pushing code without testing](img/badb859c45983005d16f2891106a17f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mCN5dpyH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2ypiy4.jpg)

一直以来都是同样的错误。一张看似简单的票分配给了你。您快速浏览代码，并识别出导致问题的那一行...

## 场景

我被分配了一个任务:“*公司的网址不正常*”。我首先设法重现了这个问题，并看到当前的 URL 在方括号内附加了某种点符号。

这段代码引起了我的注意:

```
<div class="company-details">
    {% if company.logo %}
          <img src="{{ company.logo.url }}">
      {% endif %}
  <h3>Company Details</h3>
  <p>{{ company.description }}</p>
  <ul class="social">

    {% if company.homepage %}
      <!-- NOTICE THE NEXT LINE -->
      <li><a href="[[ company.homepage.url ]]" class="icon" target="_blank"></a></li>
    {% endif %}

    {% for link in company.links.all %}
        {% if link.share_platform %}
            <li><a href="{{ link.url }}" class="icon" target="_blank">
                <img src="{% static images/app/company/background.jpg %}">
            </a></li>
        {% endif %}
    {% endfor %}
  </ul>
</div> 
```

我很快注意到这里断章取义的方括号。我很快改变了括号，提交并把代码推给了 QA。

QA 退了票。

## 以更大的胆识修复胆识

当我看到证据时，方括号似乎还在。我检查了 GitHub，看看我是否输入了正确的代码，然后搜索了我们使用该公司主页的所有地方。

原来还有第二个模板也犯了同样的错误。"*好吧，这确实是个问题！*——我对自己说，但又一次——快速修复、提交并推出更多未经测试的代码。**我完全相信这就是问题所在。**

别管我。QA 又把票退了。

## 做事正确

结果这次公司的网址把用户送回了...同一页。看到票第二次被退回，我又回到了现实。这显然不是我闭着眼睛也能做的事情，所以我决定这次我要用正确的方式去做。

查看案例后，我发现`company.homepage`是一个字符串！没有`url`属性，所以没有呈现任何内容。我解决了这个问题，但这次我以普通用户的身份进入应用程序，以确保问题得到解决。确认了这一点，我又把代码推给了 QA，一直盯着，直到成功通过 QA。

## TL；博士，总是测试你在做什么

有时，我们可能会受到诱惑，做出改变，并保证它会工作。你可能会这样做，因为你不想花时间编译项目，打开和关闭那些`docker-compose`服务，将你的代码重新部署到 Dokku，或者像我一样，只是不想去你项目的公司页面点击一个 URL。

尽管验证过程可能很乏味，但从长远来看，在将更改提交给 QA 之前，您将会少花很多时间来测试它们。他们也将保持你的 QA 指标健康:)