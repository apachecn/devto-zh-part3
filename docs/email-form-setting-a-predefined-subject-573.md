# 电子邮件表单:设置预定义的主题

> 原文：<https://dev.to/eecms/email-form-setting-a-predefined-subject-573>

有时，当邮件到达收件箱时，您想要控制邮件的“主题”文本。这是我使用的一种方法。

## 问题

使用自由文本字段作为主题文本意味着客户必须想出合适的措辞来描述他们的查询。除了这样做需要额外的努力(和增加的认知负荷)，你经常会得到一些非常晦涩的单词和短语。

那么，如果我们想让事情对客户来说更容易，同时给我们统一和有用的主题文本呢？

## 一解

假设你有一张联系表，顾客可以询问某个特定的产品。您的产品条目存储在一个通道中，因此所有的产品数据都是可用的，只需要将它们插入到您的表单中。

为此，创建一个`select`字段，供客户选择他们感兴趣的产品。使用`form`标签中的 channel entries 标签，您可以用您的产品名称/标题或其他引用来填充`option`字段。

## 示例代码

这里我补充一下:

*   主题的自定义`select`字段
*   姓名和电子邮件地址的标准`input`字段
*   几个自定义字段收集隐藏字段的信息，以增强电子邮件正文文本

```
{exp:email:contact_form user_recipients="no" recipients="admin@example.com" charset="utf-8"}
    {!-- subject field --}
    <p>
        <label for=“subject”>Subject</label><br>
        <select id=“subject” name=“subject”> 
        {!-- set a default value in case no option is selected --}
        <option value=“General enquiry”>Select a product</option>
        {!— channel entries tag to pull in product names to populate the options —}
        {ex:channel:entries channel=“my_product_channel” dynamic=“no”}
        <option value=“{title} enquiry”>{title}</option>
        {/exp:channel:entries}
        </select>
    </p>
    {!— set standards form fields for name and email —}
    <p>
        <label for=“name”>Name</label><br>
        <input type=“name” id=“name” name=“name” value="">
    </p>
    <p>
        <label for="from">Email address</label><br>
        <input type=“email” id="from" name="from" value="" required>
    </p>
    {!— add some custom form fields to collect information —}
    <p>
        <label for=“tel”>Telephone</label><br>
        <input type=“hidden” name=“message[]” value=“—-telephone—-“>
        <input type=“tel” id=“tel” name=“message[]” value="">
    </p>
    <p>
        <label for="message">Message</label><br>
        <input type=“hidden” name=“message[]” value=“—-message—-“>
        <textarea id="message" name="message[]” required></textarea>
    </p>
    <p>
        <button name="submit”>Submit</button>
    </p>
{/exp:email:contact_form} 
```

Enter fullscreen mode Exit fullscreen mode

## 胜负

提交的电子邮件内容可能如下所示:

> 发件人:无名氏
> 
> 电子邮件:【johndoe@example.com】T2
> T5】主题:蓝色极致 widget 查询
> 
> —电话—
> 
> 01234 567 890
> 
> —留言—
> 
> 您好请问小部件容易清洗吗？

## 单选按钮比选择字段更容易使用？

没问题，这只是一个调整主题字段以适应的例子:

```
<label for=“s1”>Subject</label>
<ul> 
    {!— set a default value in case no option is selected —}
    <li>
        <label for=“s1”><input type=“radio” id=“s1” name=“subject” value=“General enquiry” checked> Select a product</label>
    </li>
    {!— channel entries tag to pull in product names to populate the options —}
    {ex:channel:entries channel=“my_product_channel” dynamic=“no”}
    <li>
        <label for=“s{entry_id}”><input type=“radio” id=“s{entry_id}” name=“subject” value=“{title} enquiry”>{title}</label>
    </li>
    {/exp:channel:entries}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

-
最初由[罗布·艾伦](https://u.expressionengine.com/author/rob-allen)在[u.expressionengine.com](https://u.expressionengine.com/article/email-form-setting-a-predefined-subject)发表