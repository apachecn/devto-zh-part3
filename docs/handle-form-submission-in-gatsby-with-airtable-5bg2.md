# 用 air table 处理 gatsby 中的表单提交

> 原文：<https://dev.to/fabiorosado/handle-form-submission-in-gatsby-with-airtable-5bg2>

我目前正在建设 [Find Communities Today](https://findcommunities.today) ，一个你可以在网上找到社区的地方。我想让用户提交他们最喜欢的社区，所以我添加了一个用户可以填写并提交表单的页面。

最初，我对 Airtable 创建的表单使用了一个`<iframe>`,但是这个页面需要很长时间才能加载，所以我决定尝试自己制作一个表单并发送给 Airtable。

# 你需要什么

*   [Airtable 账户](https://airtable.com/invite/r/abr1Wgbb) -这是我的参考链接。
*   [净寿命账户](https://www.netlify.com) -我在净寿命中使用环境变量
*   盖茨比——盖茨比自动处理环境变量。
*   。env 文件夹——我将提到环境变量

这个例子非常简单。今天的 Find Communities 由 Netlify 直接从 GitHub 的私有回购中提供服务。我已经在使用 Netlify 表单供人们报告社区细节的问题，所以我不想使用 Netlify 来处理这个表单。

此外，当您需要以逻辑方式查看数据时，Airtable 非常棒。因为我已经在 Airtable 中创建了这个库，所以我决定继续使用它来处理社区的提交。

# 设置充气台

[如果您还没有注册 Airtable](https://airtable.com/invite/r/abr1Wgbb) ，请注册，然后登录。有很多模板可以使用，但我们将从一个全新的模板开始。

按下按钮`+ Add a Base`，从下拉菜单中选择`start from scratch`，选择一个名称，然后按下`Enter`。Airtable 将创建一个包含三列的电子表格

*   名字
*   笔记
*   附件

用一些随机的文本填充第一行，然后在右下角寻找有三个点的黄色图标。点击它并选择`API Documentation`

然后向下滚动，直到您在`Create record`上。在右边，你可以看到卷曲呼叫。这将有助于我们在提交表单时构建 API 调用。

最后，您将需要您的 API 密钥。转到您的 [Airtable 帐户](https://airtable.com/account)并点击生成 API 密钥，点击输入字段以便您可以看到密钥并复制它。

# 设置站点

如果你从未使用过盖茨比，我建议你阅读盖茨比官方网站上的教程。

用命令
安装 Gatsby Cli

```
npm install --global gatsby-cli 
```

Enter fullscreen mode Exit fullscreen mode

然后运行此命令创建一个新站点，并将目录更改到新文件夹中。

```
gatsby new gatsby-site && cd gatsby-site 
```

Enter fullscreen mode Exit fullscreen mode

最后，在你最喜欢的代码编辑器上打开文件夹，运行下面的命令让你的站点运行起来。

```
gatsby develop 
```

Enter fullscreen mode Exit fullscreen mode

# 创建一个环境变量

如果你正在使用 Netlify，你可以直接进入`Build & Deploy`标签，向下滚动直到找到“构建环境变量”，确保你的环境变量以`GATSBY_<name>`开始，这样你就可以把你的 API 密匙存储在一个名为`GATSBY_AIRTABLE`的变量上。

如果您只想在您的开发版本上用 Gatsby 测试这一点，您可以在您的项目的根目录上创建文件`.env.development`，然后像这样从 airtable 添加 API 键:`AIRTABLE_API=<your API key here>`。

然后，当你想使用你的 API 密匙时，你需要做的就是键入`process.env.AIRTABLE_API`，Gatsby 会自动把它翻译成你的 API 密匙。

# 构建表单

让我们最终构建表单。在您的`pages`文件夹中创建一个新页面，命名为`contact`，并在页面中添加以下代码。

```
import React from "react"

class Contact extends React.component {
  constructor(props) {
    super(props)
    this.state = {}
  }

  handleSubmit = e => {
    console.log(this.state);

    e.preventDefault();
  }

  handleChange = e => this.setState({ [e.target.name]: e.target.value})

  render() {
    return() (
      <form>
        <label>
          Name
          <input type="text" name="name" onChange={this.handleChange} />
        </label>
        <label>
          Notes
          <input type="text" name="notes" onChange={this.handleChange} />
        </label>
        <button type="submit">Submit</button>
      </form>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

前往[http://localhost:8000/contact](http://localhost:8000/contact)，您将看到我们创建的非常难看的表单。打开 devtools，然后填写表单，您会看到您刚才在输入字段中输入的任何内容都将被记录到控制台中。

如您所见，我们的组件正在跟踪输入字段中写入了什么。我们现在可以调用 Airtable API，并将它发送到我们的 base。

# 提交到 Airtable

API post 请求将在`handleSubmit`方法中完成。确保您在 API 文档中的*上创建记录*，因为您需要知道发送 post 请求的 URL。

```
handleSubmit = e => {
  const fields = {"fields": {"Name": this.state.name, "Notes": this.state.notes}}
  fetch("https://api.airtable.com/v0/<account id>/<table name>", {
    method: "POST",
    headers: {"Authorization": `Bearer ${process.env.AIRTABLE_API}`,
              "Content-Type": "application/json"},
    body: JSON.stringify(fields)
  })
  .then(() => alert("Form Sent!"))
  .catch(error => alert(error))

  e.preventDefault();
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我在授权头上使用了反勾号，如果你愿意，你可以这样写:`"Bearer " + process.env.AIRTABLE_API"`。另外，确保您传递的环境变量的名称与您的`.env.development`文件中的名称完全相同。

# 全码

所以完整的工作代码看起来会像这样

```
import React from "react"

class Contact extends React.component {
  constructor(props) {
    super(props)
    this.state = {}
  }

  handleSubmit = e => {
    const fields = {"fields": {"Name": this.state.name, "Notes": this.state.notes}}
    fetch("https://api.airtable.com/v0/<account id>/<table name>", {
      method: "POST",
      headers: {"Authorization": `Bearer ${process.env.AIRTABLE_API}`,
                "Content-Type": "application/json"},
      body: JSON.stringify(fields)
    })
    .then(() => alert("Form Sent!"))
    .catch(error => alert(error))

    e.preventDefault();
  }

  handleChange = e => this.setState({ [e.target.name]: e.target.value})

  render() {
    return() (
      <form>
        <label>
          Name
          <input type="text" name="name" onChange={this.handleChange} />
        </label>
        <label>
          Notes
          <input type="text" name="notes" onChange={this.handleChange} />
        </label>
        <button type="submit">Submit</button>
      </form>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果使用 Netlify，不要忘记将环境变量改为`GATSBY_<your env variable name>`。

请注意，您将需要更改表单以在生产中使用它，您可能需要更改输入名称、airtable 列的名称，还需要创建一个蜜罐以避免僵尸工具向您的 Airtable 库发送垃圾邮件。这是使用 Airtable API 提交表单的一种快速而简单的方法。

*注意:你的 API 密匙可以在网络标签中看到。在发出请求后，你可以阅读教程的下一部分- [用网络函数隐藏 API 密匙](https://dev.to/fabiorosado/hide-your-api-keys-with-netlify-functions-93m) -并学习如何安全地隐藏你的密匙。*