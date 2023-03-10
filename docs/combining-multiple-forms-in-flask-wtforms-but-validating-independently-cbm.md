# 在 Flask-WTForms 中组合多个表单，但独立验证

> 原文：<https://dev.to/sampart/combining-multiple-forms-in-flask-wtforms-but-validating-independently-cbm>

# 简介

我们有一个烧瓶项目即将在[白色十月](https://www.whiteoctober.co.uk/)其中将包括一个用户资料表。该表格可以被视为一个大表格或多个小表格，也可以作为一个整体或逐节提交。

作为这项工作计划的一部分，我们围绕在 Flask-WTForms 中将多个子表单组合在一起并验证它们进行了概念验证。

请注意，这是一种与“嵌套表单”略有不同的模式。嵌套表单通常用于动态重复元素——比如重复使用单个嵌套地址表单向配置文件添加多个地址。但是我们的用例是以非动态方式组合在一起的几个表单，并且可能是独立处理的。本文也没有考虑在一个页面上有多个单独的 HTML 表单的情况。

本文解释了在 Flask-WTF 中将表单组合在一起需要知道的关键事情，无论您使用的是 AJAX 还是普通回发。

# 子表单

首先要知道的是如何将多个 WTForms 表单组合成一个。为此，使用[表单字段](http://wtforms.simplecodes.com/docs/1.0.1/fields.html#field-enclosures)字段类型(也称为“字段封装”)。这里有一个例子:

```
from flask_wtf import FlaskForm
import wtforms

class AboutYouForm(FlaskForm):
    first_name = wtforms.StringField(
        label="First name", validators=[wtforms.validators.DataRequired()]
    )
    last_name = wtforms.StringField(label="Last name")

class ContactDetailsForm(FlaskForm):
    address_1 = wtforms.StringField(
        label="Address 1", validators=[wtforms.validators.DataRequired()]
    )
    address_2 = wtforms.StringField(label="Address 2")

class GiantForm(FlaskForm):
    about_you = wtforms.FormField(AboutYouForm)
    contact_details = wtforms.FormField(ContactDetailsForm) 
```

如你所见，第三种形式是由前两种形式组合而成的。

您可以像呈现任何其他表单字段一样呈现这些子表单:

```
{{ form.about_you }} 
```

(下面将更详细地讨论表单呈现。)

# 验证子表单

一旦我们组合了我们的表单，我们想要证明的第二件事是它们可以被独立地验证。

通常，您会像这样验证一个(完整的)表单:

```
if form.validate_on_submit()
    # do something 
```

(`validate_on_submit`如果表单已经提交并且有效，则返回 true。)

事实证明，您可以非常容易地验证单个表单字段。对于我们的`about_you`字段(这是一个子表单)，它看起来就像这样:

```
form.about_you.validate(form) 
```

## 决定验证什么

我们在表单中添加了多个提交按钮，以便可以处理单个子表单或整个表单。如果您给提交按钮起了不同的名字，您可以很容易地检查哪个按钮被按下，并适当地验证和保存(确保您只保存您已经验证的数据):

```
<input type="submit" name="submit-about-you" value="Just submit About You subform">
<input type="submit" name="submit-whole-form" value="Submit whole form"> 
```

然后:

```
if "submit-about-you" in request.form and form.about_you.validate(form):
    # save About You data here
elif "submit-whole-form" in request.form and form.validate():
    # save all data here 
```

如果有一个 route 方法同时处理 HTTP GET 和 POST 方法，那么在运行上述检查之前，没有必要明确检查这是否是一个回发——如果不是 POST，那么两个按钮都不会出现在`request.form`中。

### 替代方法

或者，您可以给两个提交按钮相同的名称，并区分值。然而，这意味着改变按钮上面向用户的措辞(因为这是它们的 value 属性)可能会破坏代码中的 if 语句，这并不理想，因此不同的名称是我们推荐的方法。

如果您想将提交按钮包含在 WTForms 表单类本身中，而不是硬编码 HTML，您可以通过检查相关字段的`data`属性来检查哪个按钮被提交了——参见[这里的](https://stackoverflow.com/a/35776874/328817)中的一个小例子。

### 明白了:基于浏览器的验证和多个提交按钮

如果使用多个提交按钮来验证/保存一个大表单的一个子表单中的数据，就会遇到一个问题。

如果您的表单字段设置了`required`属性(例如，如果您使用`DataRequired`验证器，WTForms 将会这样做)，那么浏览器将会阻止您提交表单，直到*所有的*必填字段都被填写完毕——它不知道您只关心表单的一部分(因为这部分提交是在服务器端实现的)。

因此，假设您想继续使用`required`属性(您应该这样做)，您需要添加一些 Javascript 来在提交时动态改变表单字段属性。

如果您使用 AJAX 而不是回发来提交表单，这不是问题；见下文如何做。

# 在模板中渲染子表单

本节中的示例使用显式字段名称。在实践中，您会想要创建一个[字段呈现宏](http://flask.pocoo.org/docs/1.0/patterns/wtforms/#forms-in-templates)，您可以将每个表单字段传递给它，而不是为您拥有的每个表单字段重复这些代码。该链接还展示了如何分别呈现字段的标签和小部件，这使您可以更好地控制标记。

如上所述，子表单可以用一行来呈现，就像任何其他字段一样:

```
{{ form.about_you }} 
```

如果您想单独呈现子表单中的字段，看起来会像这样:

```
<label for="{{ form.about_you.first_name.id }}">{{ form.about_you.first_name.label }}</label>
{{ form.about_you.first_name }} 
```

如您所见，您不能对表单字段及其子表单中单个字段的标签进行单行呈现——您必须显式呈现标签。

## 显示子表单错误

对于一个普通的表单字段，可以通过迭代`errors`属性来显示相关的错误，如下所示:

```
{% if form.your_field_name.errors %}
    <ul class=errors>
        {% for error in field.errors %}
            <li>{{ error }}</li>
        {% endfor %}
    </ul>
{% endif %} 
```

在这种情况下，`errors`只是该字段错误字符串的**列表**。

然而，对于使用 FormField 字段类型的子表单来说，`errors`是一个将字段名映射到错误列表的**字典**。例如:

```
{
    'first_name': ['This field is required.'],
    'last_name': ['This field is required.'],
} 
```

因此，在您的模板中迭代它会更加复杂。下面的例子显示了子表单中所有字段的错误(注意使用了`[items](https://docs.python.org/3/tutorial/datastructures.html#looping-techniques)`方法):

```
{% if form.about_you.errors %}
    <ul class="errors">
        {% for error_field, errors in form.about_you.errors.items() %}
            <li>{{ error_field }}: {{ errors|join(', ') }}</li>
        {% endfor %}
    </ul>
{% endif %} 
```

# 用 AJAX 实现

到目前为止，我们已经考虑了通过整页回发提交数据时验证子表单的情况。但是，您可能希望使用 AJAX 进行子表单验证，使用 JavaScript 将表单数据异步发送回 Flask 应用程序。

已经有一篇由 Anthony Plunkett 撰写的优秀文章，名为“[使用 Flask](https://medium.com/@doobeh/posting-a-wtform-via-ajax-with-flask-b977782edeee) 通过 AJAX 发布 WTForm”，其中几乎包含了这样做所需要知道的一切。

因此，在本文中，我将详细阐述在使用多个提交按钮时会遇到的一个问题——确定提交按钮被按下

## 确定提交按钮被按下

当用 JavaScript 将数据发送回服务器时，您可能会使用类似 jQuery 的 [serialize](https://api.jquery.com/serialize/) 这样的方法。但是，这种方法产生的数据不包括提交表单时单击的按钮的详细信息。

有各种各样的方法可以解决这个限制。我发现最有用的方法是在表单中动态添加一个隐藏字段，它的名称和值与提交按钮相同(参见[这里的](https://stackoverflow.com/a/11271850/328817))。这样，无论您使用 AJAX 还是回发，像`if "submit-about-you" in request.form`(见上文)这样的 Python 代码都可以保持不变。