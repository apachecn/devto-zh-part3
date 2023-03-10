# React 文件上传:使用 NodeJS 的简单方法！

> 原文：<https://dev.to/kris/react-file-upload-the-easy-way-with-nodejs-5626>

### 如何用 React 和 Nodejs 开始上传文件

#### 在本教程中，我们将通过 express 和 React.js 构建一个全栈文件上传解决方案

[![](img/1f61574a235b5002037fce750f7d26b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eRgBUf0v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/940/1%2A1DOMi_VcKhBSHU5GmBJFcw.png)

我们将创建一个基本的表单，用 React 处理文件上传，用 Nodejs 处理服务器端文件上传。这个教程非常简单，你只需要花 5 分钟就可以完成，如果你认为是 TLDR 的话；只需在此处选择[完成代码](https://github.com/krissnawat/react-file-upload/tree/master)，现场演示将在 Codesandbox 上进行

[上传-前台-代码沙盒](https://codesandbox.io/s/github/krissnawat/react-file-upload/tree/master/?fontsize=14)

### 设置节点服务器

如果您遵循完整的教程，完整的服务器代码是可用的。转到服务器目录，打开终端并执行 npm 安装和节点索引，这将运行服务器。然后从 React 部分继续。

1.  创建一个目录*上传服务器*，在这个目录中打开终端并运行 npm init。接受所有默认值。
2.  在同一个终端中，安装 upload 和 cors 包将命令 NPM express express-file upload CORS

**start on index.js 是入口文件。**

这是服务器的初始代码。

```
const PORT = 5000;
const express = require('express');
const cors = require('cors');

const app = express();
app.use(cors());
app.get('/', (req, res) => {
  return res.status(200).send("It's working");
});

app.listen(PORT, () => {
  console.log('Server Running sucessfully.');
}); 
```

现在，转到你的浏览器 localhost:5000 T1，你会看到 T2 正在工作。

### 上传端点

让我们在上传端点定义/上传。它需要中间件 fileUpload，先设置这个东西。

```
app.use(
  fileUpload({
    useTempFiles: true,
    safeFileNames: true,
    preserveExtension: true,
    tempFileDir: `${__dirname}/public/files/temp`
  })
); 
```

它接受许多选项，你可以在这里找到。

临时目录用于临时存储文件，而不是使用计算机内存(RAM)。这在上传大文件时非常有用。

安全文件删除文件名中除破折号和下划线以外的非字母数字字符。您还可以为自定义名称定义 regex，而不是 true。

*preserveExtension* 保留文件名的扩展名，默认为 false。它还支持定制，[选项请参考文档](https://www.npmjs.com/package/express-fileupload)。

```
app.post('/upload', (req, res, next) => {
  let uploadFile = req.files.file;
  const name = uploadFile.name;
  const md5 = uploadFile.md5();
  const saveAs = `${md5}_${name}`;
  uploadFile.mv(`${__dirname}/public/files/${saveAs}`, function(err) {
    if (err) {
      return res.status(500).send(err);
    }
    return res.status(200).json({ status: 'uploaded', name, saveAs });
  });
}); 
```

*req.files.file* 中的**文件**是指 ***文件*** 作为输入栏的名称。即在和 req.file 中的**名**，**名**应该是一样的。

为了防止覆盖文件，我使用了 uploadFile 包中的 md5 函数，并将文件保存为 ***md5_filename.ext*** 。

您也可以使用 Postman 测试 API。

[![](img/6921f69c0529a14d10d35931f59caf55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JtyqnHq9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AD046OXLOhSAHjD17.png)

向/上传文件发送发布请求。应该能顺利通过。

### 反应前端

一个服务器正在等待文件，你需要一个界面允许用户输入文件。让我们使用 React 创建一个接口。

用 **create-react-app 设置 react 应用程序。**

npx 创建-反应-应用程序上传-前端

在 App.js 中放一个带有输入类型文件的表单，应该是这样的:

```
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <form className="App">
        <input type="file" name="file" />
        <button>Upload</button>
      </form>
    );
  }
}

export default App; 
```

按钮不会自己上传。定义一些方法和状态来实现功能。

**状态:**选择的文件

**方法:** handleFileChange 和 handleUpload

添加更改侦听器作为输入的 handleFileChange。您可以将 *handleFileUpload* 附加为提交动作的表单或按钮上的 click listener。我们将使用状态来保存文件，所以这无关紧要。

这是它的样子。

```
import React, { Component } from 'react';

class App extends Component {
  handleFileChange = () => {
    // define file change
  };
  handleUpload = () => {
    // define upload
  };
  render() {
    return (
      <form className="App" onSubmit={handleUpload}>
        <input type="file" name="file" onChange={handleFileChange} />
        <button onClick={handleUpload}>Upload</button>
      </form>
    );
  }
}

export default App; 
```

**不要保持*句柄上传*在 *onClick* 和 *onSubmit 上。*** 一个就够了。

处理文件更改，我们跟踪文件和上传按钮点击。

### 处理文件变更

```
handleFileChange = (event) => {
    this.setState({
      selectedFile: event.target.files[0]
    });
  }; 
```

文件输入是一个单击事件，文件存储在一个数组中(即使在单个文件上载中)。

### 处理上传

为了处理上传，我们将使用 Axios 发送 ajax 请求。安装并导入 Axios。

npm i axios

从' axios
导入 axios

```
handleUpload = (event) => {
    event.preventDefault();
    const data = new FormData();
    data.append('file', this.state.selectedFile, this.state.selectedFile.name);
    axios.post(endpoint, data).then((res) => {
      console.log(res.statusText);
    });
  }; 
```

文件作为 FormData 发送。它只是在完成 AJAX 请求后记录响应文本。

虽然它看起来太丑了，但上传现在应该开始工作了。

### 加载状态

文件上传成功后，用户看不到响应。显示完成后的响应非常简单。让我们显示进度。

**axios** 可以通过 post 方法获取一些参数。其中之一就是 *ProgressEvent* 。

```
{
        onUploadProgress: ProgressEvent => {
          this.setState({
            loaded: (ProgressEvent.loaded / ProgressEvent.total\*100),
          } 
```

这会将加载状态设置为任何完成百分比。

我们还需要显示它，所以将它包装在表单外的一个段落中。这将导致应用程序崩溃，因为 JSX 不能返回多个部分。将返回包装在 React 片段中。

下面是代码:

```
return (
      <React.Fragment>
        <form className="App" onSubmit={this.handleUpload}>
          <input
            className="inputfile"
            id="file"
            type="file"
            name="file"
            onChange={this.handleFileChange}
          />
          <label htmlFor="file">Choose a file</label>
          <button onClick={this.handleUpload}>Upload</button>
        </form>
        <p>{Math.round(this.state.loaded)}%</p>
      </React.Fragment>
    ); 
```

我给了它一个标签，我们很快就会开始玩。Math.round 是为了避免小数值。

但是这将在开始时抛出错误，因为加载的状态甚至不存在。

使用:
初始化组件安装的状态

```
state = { selectedFile: null, loaded: null }; 
```

没有必要为一个状态定义一个构造函数，如果你一直这样做的话。

下面是**如何处理上传**现在……

```
handleUpload = (event) => {
    // define upload
    event.preventDefault();
    const data = new FormData();
    data.append('file', this.state.selectedFile, this.state.selectedFile.name);
    axios
      .post(endpoint, data, {
        onUploadProgress: (ProgressEvent) => {
          this.setState({
            loaded: (ProgressEvent.loaded / ProgressEvent.total) \* 100
          });
        }
      })
      .then((res) => {
        console.log(res.statusText);
      });
  }; 
```

### 不丑化事物……

这就是现在的丑陋…

[![](img/c0a7a7011acc2371dd21798079c584be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--coBCQHfx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/574/0%2AhpPdsqxXYibgv501.png)

从[这里](https://tympanus.net/Tutorials/CustomFileInputs/)抓起 CSS，移到 index.css。

我还添加了状态*上传*、*消息、*和*默认消息*。如果当前正在上传，则**已加载**状态将显示完成百分比。否则，将显示**消息**。

返回的 JSX 也略有变化，反应部分和方法保持不变。这来自[同一个 HTML 文件](https://tympanus.net/Tutorials/CustomFileInputs/)。

```
return (
      <form className="box" onSubmit={this.handleUpload}>
        <input
          type="file"
          name="file-5[]"
          id="file-5"
          className="inputfile inputfile-4"
          onChange={this.handleFileChange}
        />
        <label htmlFor="file-5">
          <figure>
            
              <path d="M10 0l-5.2 4.9h3.3v5.1h3.8v-5.1h3.3l-5.2-4.9zm9.3 11.5l-3.2-2.1h-2l3.4 2.6h-3.5c-.1 0-.2.1-.2.1l-.8 2.3h-6l-.8-2.2c-.1-.1-.1-.2-.2-.2h-3.6l3.4-2.6h-2l-3.2 2.1c-.4.3-.7 1-.6 1.5l.6 3.1c.1.5.7.9 1.2.9h16.3c.6 0 1.1-.4 1.3-.9l.6-3.1c.1-.5-.2-1.2-.7-1.5z" />
            
          </figure>
          <span>
            {this.state.uploading
              ? this.state.loaded + '%'
              : this.state.message}
          </span>
        </label>
        <button className="submit" onClick={this.handleUpload}>
          Upload
        </button>
      </form>
    ); 
```

[![](img/6c5ef691ff1fe9e346e6a2d0c99e730d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZSD1tmUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A2XGPJRJs9DL2y4sg.png)

在为按钮添加了一些 CSS 之后，看起来像这样:

[![](img/02c2def7fd5dbb5c99c80851b484cf3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EknKlzfs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AuLJSbOKpbuNlj1gv.png)

如果你没有选择文件就点击上传按钮，它会崩溃。

[![](img/eb3d847cffe560b7c2f6a7d14f38b45c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WCPy0dc8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/907/0%2Ab_G7mZgOyUG5CLzh.png)

这是因为选择的文件为 null，而 null 没有 name 属性。

要解决这个问题，如果 *selectedFile* 为 *null，则从 *handleUpload* 方法返回。*此外，将消息设置为*首先上传*，这样用户可以得到一些关于该做什么的反馈。

```
handleUpload = (event) => {
    event.preventDefault();
    if (this.state.uploading) return;
    if (!this.state.selectedFile) {
      this.setState({ message: 'Select a file first' });
      return;
    }
    this.setState({ uploading: true });
    const data = new FormData();
    data.append('file', this.state.selectedFile, this.state.selectedFile.name);
    axios
      .post(endpoint, data, {
        onUploadProgress: (ProgressEvent) => {
          this.setState({
            loaded: Math.round(
              (ProgressEvent.loaded / ProgressEvent.total) \* 100
            )
          });
        }
      })
      .then((res) => {
        this.setState({
          selectedFile: null,
          message: 'Uploaded successfully',
          uploading: false
        });
        console.log(res.statusText);
      });
  }; 
```

此外，我对加载的状态本身进行了舍入，而不是在输出中进行舍入。如果您没有选择文件就点击上传，信息将变为*先选择一个文件*。

[![](img/007fb93545f5808910923ecce365f1ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uPGXHzEh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/434/0%2AZZvPMO3J2UH81CtB.png)

在前一个文件完成之前，您可以上传另一个文件。我们可以用卸载状态来处理这个问题。如果上传是真的，不上传直接从句柄上传返回即可。

```
if (this.state.uploading) return;
    if (!this.state.selectedFile) {
      this.setState({ message: 'Select a file first' });
      return;
    }
    this.setState({ uploading: true }); 
```

上传成功后，将**消息**更改为 ***上传成功*** ，而**上传**状态更改为*假。*

**handleFileChange** 修改为显示将要上传的文件名。

```
handleFileChange = (event) => {
    this.setState({
      selectedFile: event.target.files[0],
      loaded: 0,
      message: event.target.files[0]
        ? event.target.files[0].name
        : this.state.defaultmessage
    });
  }; 
```

### 错误处理

如果您关闭服务器，此应用程序仍将尝试上传文件，并且 loaded 将更改为 NaN(不是一个数字)。由于*上传*被设置*为真*，由于*手柄上传*被此阻挡，即使服务器恢复也无法上传。

向 post 请求添加一个 catch..

```
.catch((err) => {
        this.setState({
          uploading: false,
          message: 'Failed to upload'
        });
      }); 
```

太棒了，它上传一个文件，同时显示错误和成功的消息。

[![](img/02cf43e51258ff70849878264ec84c5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zd_BUXkY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AxNmrMnZ8D4PG9if9.gif)

#### 结论

您将学习如何以简单的方式使用 Reactjs 和 Nodejs 进行基本上传，以继续下一步您可以在客户端和服务器上实现验证或在上传前显示预览

### 精选 React JS 课程

#### [React 16 —完全指南(含 React 路由器 4 & Redux)](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1362070&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Freact-the-complete-guide-incl-redux%2F)

4.7/5 星 **||** 33.5 小时视频**| |**61597 名学生

学习反应或深入研究。学习理论，解决作业，在演示项目中实践，并构建一个在整个课程中不断改进的大型应用程序:汉堡生成器！ [**了解更多。**T3】](http://thebestdevlist.link/react-the-complete-16-guide-incl-redux)

[React 16:完整课程(包括 React 路由器 4 & Redux)](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1362070&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Freact-the-complete-guide-incl-redux%2F)

### 免责声明

此帖子包含产品的附属链接。我们可能会收到通过这些链接购买的佣金。

* * *