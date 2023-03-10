# 使用 Azure 函数创建电子邮件订阅-第 3 部分

> 原文：<https://dev.to/azure/create-an-email-subscription-with-azure-functions-part-3-104n>

*我们选择了由[迈克尔·克伦普](https://twitter.com/mbcrump)创造的我们最喜欢的技巧和诀窍，并在整个四月在 Azure 上提供新的技术内容！错过一天(或更多)？[赶上系列](https://dev.to/t/azureapril)。*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

这是关于如何用 Azure 功能构建无服务器电子邮件订阅服务的 4 篇系列文章中的第三篇。

完整源代码该应用的源代码可以在 [GitHub](https://github.com/mbcrump/EmailSubscription) 上找到

[第 1 部分——我们要构建什么以及如何构建](http://www.michaelcrump.net/azure-tips-and-tricks97/)
[第 2 部分——使用 Azure 表存储存储电子邮件](http://www.michaelcrump.net/azure-tips-and-tricks98/)
[第 3 部分——用 HTML5 和 jQuery 编写前端](https://dev.toPart%203%20-%20Writing%20the%20Frontend%20with%20HTML5%20and%20jQuery)
[第 4 部分——用 Sendgrid 和 Azure 函数发送电子邮件](http://www.michaelcrump.net/azure-tips-and-tricks100/)

我们正在尝试建立一个类似下面的电子邮件订阅。

[![](img/5ce57bea9a1ff708d3b86c4a2571717a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kj3ft86a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1i1bl0nqnic47xjf5bxd.png)

## 从我们停止的地方继续

在上一篇文章中，我们创建了一个 Azure 函数，它能够接受 post 请求，并使用 Azure Table Storage 为我们的电子邮件地址存储数据。虽然这在类似 Postman 的东西中工作得很好，但我们需要创建一种允许用户与之交互的方式。

现在是发布我们的 Azure 函数的好时机。只需右键单击项目名称并选择发布，然后再次发布，如下所示。

[![](img/8449bee7c775dc802a89c89b19d386fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6KbnKwKf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ij2gi983tdkjzyacosry.png)

部署完成后，如果您单击 StoreEmail 函数，您将看到如下所示的 Get 函数 URL。

[![](img/8a0bfc2c4516da51b2b57584d96bca5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iIUeh7VY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1bamciuitfu1ec2qmrdt.png)

如果您单击它，然后复制并粘贴函数 url，因为您稍后会用到它。

[![](img/6bb24e7c883b6fae9693d39d975accb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UjeDxq12--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3lbp407bqpsjggmv0cph.png)

## 与 CORS 打交道

当我们在门户中时，点击你的 Azure 功能，在平台功能下，你会看到 API，然后是 CORS。

跨源资源共享(CORS)允许在外部主机上的浏览器中运行的 JavaScript 代码与您的后端进行交互。

因为我们很快就会将它转移到一个 web 主机上，所以您需要指定您的域名。这是我的:

## 最后，前端。

创建新的。HTML 页面的任意位置，并通过添加以下代码开始:

```
<div class="style-1')">
  <h1>Subscribe to Michael's Weekly Blog Digest</h1>
  <i>I'll send you an email once a week of all my recent blog posts and notable Azure news.</i>
</div>
<form id="target')">
  <ul class="form-style-1')">
    <div id="contactForm')">
       Email: <span class="required')">*</span>
       <li><input type="text" name="fromEmail" /></li>
       <li>
         <br/>
         <input type="submit" value="Send!" /></li>
     </div>
  </ul>
</form>
<div id="contactFormStatus" class="style-1')" /> 
```

这给了我们一个表单，以及我们的电子邮件地址的输入类型和提交按钮。我们在底部还有一个 div 标签，我们将使用它来显示请求的状态。

现在我们将使用 jQuery 和一个 Ajax 调用将电子邮件地址发送到我们之前复制的 Azure 函数 url。我们将使用一个我从网上偷来的正则表达式进行额外的验证，他们提供了一个有效的电子邮件地址。

```
<script src="https://code.jquery.com/jquery-3.1.1.min.js')"></script>
<script type="text/javascript')">
  var url = "https://functionsendemails.azurewebsites.net/api/StoreEmail?code=hKngrr6sq35GJ8cb6al4K6oP0cRphKNDMXpLrCtoNCJzM0ZDwJNkJQ==";
  $("form").on('submit', function (event) {
    event.preventDefault();
    var data = $(this).serialize();
         $("#target :input").prop("disabled", true);
         var reg = /^((([a-z]|\d|[!#\$%&'\*\+\-\/=\?\^_`{\|}~]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])+(\.([a-z]|\d|[!#\$%&'\*\+\-\/=\?\^_`{\|}~]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])+)*)|((\x22)((((\x20|\x09)*(\x0d\x0a))?(\x20|\x09)+)?(([\x01-\x08\x0b\x0c\x0e-\x1f\x7f]|\x21|[\x23-\x5b]|[\x5d-\x7e]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(\\([\x01-\x09\x0b\x0c\x0d-\x7f]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF]))))*(((\x20|\x09)*(\x0d\x0a))?(\x20|\x09)+)?(\x22)))@((([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|\d|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.)+(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])|(([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])([a-z]|\d|-|\.|_|~|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])*([a-z]|[\u00A0-\uD7FF\uF900-\uFDCF\uFDF0-\uFFEF])))\.?$/i
         var address = $('input:text').val();
            if(reg.test(address) == false) {
                // Validation Failed
                $("#contactFormStatus").html("Enter a valid email address").css({ 'color': 'red', 'font-size': '110%' });;
                $("#target :input").prop("disabled", false);
            }
            else {
            $.ajax({
                type: "POST",
                url: url,
                data: data,
                dataType: "text",
                headers: {'Content-Type': 'application/x-www-form-urlencoded'},
                success: function (respData) {
                    $("#contactFormStatus").html(respData).css({ 'color': 'green', 'font-size': '100%' });;
                    $("#target :input").prop("disabled", true);
                },
                error: function (jqXHR) {
                    $("#contactFormStatus").html("An error occurred: " + jqXHR.responseText).css({ 'color': 'red', 'font-size': '110%' });;
                    $("#target :input").prop("disabled", false);
                }
            });
            }
     });
 </script> 
```

我们现在将放入一些 CSS 来使网站看起来更漂亮。

```
<style type="text/css')">
.style-1 {
  margin: 10px auto;
  max-width: 400px;
  padding: 20px 12px 10px 20px;
  font: 13px "Lucida Sans Unicode", "Lucida Grande", sans-serif;
}

.form-style-1 {
  margin: 10px auto;
  max-width: 400px;
  padding: 20px 12px 10px 20px;
  font: 13px "Lucida Sans Unicode", "Lucida Grande", sans-serif;
}

.form-style-1 li {
  padding: 0;
  display: block;
  list-style: none;
  margin: 10px 0 0 0;
}

.form-style-1 label {
  margin: 0 0 3px 0;
  padding: 0px;
  display: block;
  font-weight: bold;
}

.form-style-1 input[type=text],
.form-style-1 input[type=date],
.form-style-1 input[type=datetime],
.form-style-1 input[type=number],
.form-style-1 input[type=search],
.form-style-1 input[type=time],
.form-style-1 input[type=url],
.form-style-1 input[type=email],
textarea,
select {
  box-sizing: border-box;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  border: 1px solid #BEBEBE;
  padding: 7px;
  margin: 0px;
  -webkit-transition: all 0.30s ease-in-out;
  -moz-transition: all 0.30s ease-in-out;
  -ms-transition: all 0.30s ease-in-out;
  -o-transition: all 0.30s ease-in-out;
  outline: none;
}

.form-style-1 input[type=text]:focus,
.form-style-1 input[type=date]:focus,
.form-style-1 input[type=datetime]:focus,
.form-style-1 input[type=number]:focus,
.form-style-1 input[type=search]:focus,
.form-style-1 input[type=time]:focus,
.form-style-1 input[type=url]:focus,
.form-style-1 input[type=email]:focus,
.form-style-1 textarea:focus,
.form-style-1 select:focus {
  -moz-box-shadow: 0 0 8px #88D5E9;
  -webkit-box-shadow: 0 0 8px #88D5E9;
  box-shadow: 0 0 8px #88D5E9;
  border: 1px solid #88D5E9;
}

.form-style-1 .field-divided {
  width: 49%;
}

.form-style-1 .field-long {
  width: 100%;
}

.form-style-1 .field-select {
  width: 100%;
}

.form-style-1 .field-textarea {
  height: 100px;
}

.form-style-1 input[type=submit],
.form-style-1 input[type=button] {
  background: #4B99AD;
  padding: 8px 15px 8px 15px;
  border: none;
  color: #fff;
}

.form-style-1 input[type=submit]:hover,
.form-style-1 input[type=button]:hover {
  background: #4691A4;
  box-shadow: none;
  -moz-box-shadow: none;
  -webkit-box-shadow: none;
}

.form-style-1 .required {
  color: red;
}
</style> 
```

如果你试着在你的服务器上运行这些代码，你会看到它像设计的那样工作。还可以使用类似 Azure Storage Explorer 的东西来查看表中的数据。

[![](img/8886d4a934315e8a6a4c30874584c313.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iVu3ZXKU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdx2ak2dlqc6i4w1fejc.gif)

暂时就这样吧！我们将在明天的第四部分完成这个项目。

想要更多 Azure 功能？查看我们的[快速入门和教程](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=azureapril_devto-blog-cxa)！

* * *

我们将在 4 月份每天发布文章，所以请继续关注或提前查看更多提示和技巧[现在](http://azuredev.tips)。