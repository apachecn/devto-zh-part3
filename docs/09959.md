# 设置 cookie 读取 cookie！如何在 Rails 应用程序中(温和地)收集用户电子邮件，而无需设计或要求密码。

> 原文：<https://dev.to/leenyburger/set-a-cookie-read-a-cookie-how-to-gently-collect-user-emails-in-a-rails-application-without-devise-or-requiring-a-password-147j>

一位客户要求我“软关”用户的电子邮件。客户想要一份使用该应用程序的人的电子邮件列表，但不想阻止任何人使用。这就提出了一个问题——我如何在不阻止用户使用应用程序的情况下向他们索要电子邮件(或者让他们非常恼火，以至于关上了窗户)？我怎么能记得他们来过，并且不再问他们的电子邮件地址呢？

首先，这个解决方案一点也不安全。Devise 有一个“软”注册功能，允许用户开始时只输入他们的电子邮件地址(然后添加密码)，如果你正在寻找真正的用户身份验证，你可能想检查一下。在这种情况下，我只是试图为邮件列表收集电子邮件地址。

## 问题:

如何收集用户的邮箱地址，又不至于太烦人或者妨碍他们使用 app？

## 问题:

用户状态必须以某种方式保存，因为你不希望应用程序在用户每次访问网站时都请求电子邮件地址。

## 解:

在用户的浏览器上保存一个 cookie，并读取该 cookie 以确定用户是否已经访问过该站点。如果有，让他们通过。如果他们没有，显示一个弹出模式，询问他们的电子邮件地址，但允许他们点击页面上的任何地方关闭该模式，并仍然使用该网站。

### 第一步:保存饼干

如果用户保存成功，在`users`控制器的`create`方法中添加
`cookies[:user_email] = @user.email`。

### 第二步:创建模态

我很懒，这几乎直接来自于 bootstrap 文档。我的模态视图呈现我的`users#new`表单。我将`locals`作为`User.new`发送，因为`form_for user`需要一个用户。如果通过`create`动作访问表单，这通常会自动完成。:

```
<!-- Modal -->
<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Modal title</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        <%= render partial: 'users/form', locals: { user: User.new } %>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
      </div> 
    </div>
  </div>
</div> 
```

### 第三步:检查饼干

因此，自举模式被设计成由按钮触发。这是直接从文件。我想只在 cookie 未设置时触发它。起初这让我犯了个错误，我试图在视图中访问`cookies[:user_email]`。没有骰子。正确的做法:

在`root`控制器索引(首页)里我加了这个方法:
( `the index action of the root controller` )

```
 def index
    @user_email = cookies[:user_email]
  end 
```

然后在视图中我检查了我的 cookie:
`layouts/application.html.haml`(或者 erb 随便你怎么说)

```
- if !@user_email
  #render-modal
  = render 'modals/user_email.html.erb' 
```

所以在`index`方法中设置了`@user_email`。如果`@user_email`不存在(即还没有创建 cookie)，我创建一个 id 为`render-modal`的`div`。

### 第四步:显示模态

如果上面创建的 div 存在，则显示模态的快速 jquery 代码段:
`app/assets/javascripts/modals.js`

```
$(document).ready(function() {
  if ($('#render-modal').length) {
    $('#myModal').modal({show:true});
  }
}); 
```

你有它！如果用户以前没有访问过您的站点，这是一种快速呈现弹出模式的方法。现在开始收集电子邮件地址，并向您的用户发送精彩内容！