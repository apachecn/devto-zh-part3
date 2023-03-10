# 用 Jest 在 React 组件中进行快照测试

> 原文：<https://dev.to/skptricks/snapshot-testing-in-react-components-with-jest-2fnk>

* * *

## 来源:[用 Jest 对 React 组件进行快照测试](https://www.skptricks.com/2019/01/Snapshot-testing-React-Components-with-Jest.html)

在本教程中，我们将学习如何在 react 应用程序中执行快照测试。Jest 是一个 JavaScript **单元测试框架**，被脸书用来测试服务和反应应用。Jest 充当了**测试运行器、断言库和嘲讽库**。快照测试是 Jest 提供的一个非常酷的特性。它可以记住你的 UI 组件是如何呈现的，并将其与当前测试进行比较，如果不匹配，就会产生错误。

[![Snapshot testing in React Components with Jest](img/a240c93d2ab353b5647a541ff8c45525.png "Snapshot testing in React Components with Jest")](https://1.bp.blogspot.com/-yMokHtmqIhc/XDIqH7huV7I/AAAAAAAACTg/sWCaiZbaoTgcVoRhUTWC9V-UoB2CdY_0QCLcBGAs/s1600/snap.png)

为了使用 **create-react-app** 应用程序在 react 应用程序中执行快照测试，您需要使用 npm 命令安装 **react-test-renderer 包**:

## **安装*反应-测试-渲染器*包:**

```
npm install react-test-renderer 
```

Jest 还提供快照测试，即创建组件的渲染“快照”并将其与之前保存的“快照”进行比较的能力。如果两者不匹配，测试将失败。快照将会保存在创建它们的测试文件旁边的自动生成的**_ _ snapshot _ _**文件夹中。

在这个演示中，我们将对 **App** 组件进行快照测试，其中我们已经导入了 **RegisterForm** 组件。

让我们从和**应用**组件中看到**注册:**

* *GitHub 项目链接:**

[https://GitHub . com/skp tricks/react/tree/master/Snapshot % 20 testing % 20 example](https://github.com/skptricks/react/tree/master/Snapshot%20testing%20example)

## **注册自。js**

```
import React from 'react';  
import './style.css';  

class RegisterForm extends React.Component {  
    constructor() {  
super();  
this.state = {  
        fields: {},  
        errors: {}  
}  

this.handleChange = this.handleChange.bind(this);  
this.submituserRegistrationForm = this.submituserRegistrationForm.bind(this);  

};  

    handleChange(e) {  
let fields = this.state.fields;  
      fields[e.target.name] = e.target.value;  
this.setState({  
        fields  
});  

}  

    submituserRegistrationForm(e) {  
      e.preventDefault();  
if (this.validateForm()) {  
let fields = {};  
          fields["username"] = "";  
          fields["emailid"] = "";  
          fields["mobileno"] = "";  
          fields["password"] = "";  
this.setState({fields:fields});  
          alert("Form submitted");  
}  

}  

    validateForm() {  

let fields = this.state.fields;  
let errors = {};  
let formIsValid = true;  

if (!fields["username"]) {  
        formIsValid = false;  
        errors["username"] = "\*Please enter your username.";  
}  

if (typeof fields["username"] !== "undefined") {  
if (!fields["username"].match(/^[a-zA-Z]\*$/)) {  
          formIsValid = false;  
          errors["username"] = "\*Please enter alphabet characters only.";  
}  
}  

if (!fields["emailid"]) {  
        formIsValid = false;  
        errors["emailid"] = "\*Please enter your email-ID.";  
}  

if (typeof fields["emailid"] !== "undefined") {  
//regular expression for email validation  
var pattern = new RegExp(/^(("[\w-\s]+")|([\w-]+(?:\.[\w-]+)\*)|("[\w-\s]+")([\w-]+(?:\.[\w-]+)\*))(@((?:[\w-]+\.)\*\w[\w-]{0,66})\.([a-z]{2,6}(?:\.[a-z]{2})?)$)|(@\[?((25[0-5]\.|2[0-4][0-9]\.|1[0-9]{2}\.|[0-9]{1,2}\.))((25[0-5]|2[0-4][0-9]|1[0-9]{2}|[0-9]{1,2})\.){2}(25[0-5]|2[0-4][0-9]|1[0-9]{2}|[0-9]{1,2})\]?$)/i);  
if (!pattern.test(fields["emailid"])) {  
          formIsValid = false;  
          errors["emailid"] = "\*Please enter valid email-ID.";  
}  
}  

if (!fields["mobileno"]) {  
        formIsValid = false;  
        errors["mobileno"] = "\*Please enter your mobile no.";  
}  

if (typeof fields["mobileno"] !== "undefined") {  
if (!fields["mobileno"].match(/^[0-9]{10}$/)) {  
          formIsValid = false;  
          errors["mobileno"] = "\*Please enter valid mobile no.";  
}  
}  

if (!fields["password"]) {  
        formIsValid = false;  
        errors["password"] = "\*Please enter your password.";  
}  

if (typeof fields["password"] !== "undefined") {  
if (!fields["password"].match(/^.\*(?=.{8,})(?=.\*\d)(?=.\*[a-z])(?=.\*[A-Z])(?=.\*[@#$%&]).\*$/)) {  
          formIsValid = false;  
          errors["password"] = "\*Please enter secure and strong password.";  
}  
}  

this.setState({  
        errors: errors  
});  
return formIsValid;  

}  

  render() {  
return (  
<div id="main-registration-container">  
<div id="register">  
<h3>Registration page</h3>  
<form method="post" name="userRegistrationForm" onSubmit= {this.submituserRegistrationForm} >  
<label>Name</label>  
<input type="text" name="username" value={this.state.fields.username} onChange={this.handleChange} />  
<div className="errorMsg">{this.state.errors.username}</div>  
<label>Email ID:</label>  
<input type="text" name="emailid" value={this.state.fields.emailid} onChange={this.handleChange}/>  
<div className="errorMsg">{this.state.errors.emailid}</div>  
<label>Mobile No:</label>  
<input type="text" name="mobileno" value={this.state.fields.mobileno} onChange={this.handleChange}/>  
<div className="errorMsg">{this.state.errors.mobileno}</div>  
<label>Password</label>  
<input type="password" name="password" value={this.state.fields.password} onChange={this.handleChange} />  
<div className="errorMsg">{this.state.errors.password}</div>  
<input type="submit" className="button" value="Register"/>  
</form>  
</div>  
</div>  

);  
}  

}  

export default RegisterForm; 
```

## **App.js**

```
import React, { Component } from 'react';  
import RegisterForm from './components/RegisterForm';  
//import './App.css';  

class App extends Component {  

  render() {  
return (  
<RegisterForm />  
);  
}  
}  

export default App; 
```

现在，您必须将 **jest 测试脚本**放在同一个目录中，在这个目录中，您已经将 **App** 组件放在 react 项目中。请记住 **jest 测试脚本**文件以组件名开头，后跟 **.spec.js** 或。 **test.js** 扩展。由于我们正在对 App 组件进行快照测试，所以 jest 测试脚本文件名应该是 **App.test.js** 。

让我们看看 jest 测试脚本文件:

## [T1**app . test . js**](#apptestjs)

```
import React from 'react';  
import App from './App';  
import renderer from 'react-test-renderer';  

it('renders correctly', () => {  
const tree = renderer  
.create(<App />)  
.toJSON();  
  expect(tree).toMatchSnapshot();  
}); 
```

现在我们已经完成了 jest 测试脚本的创建，最后一步是运行测试脚本。为了在 react 应用程序中运行 jest 测试脚本，请遵循以下步骤:

1.  打开终端或 cmd 提示符，
2.  在终端或 cmd 提示符下切换到您的项目目录。
3.  在命令提示符下应用下面的命令。

**运行测试脚本**

```
npm test 
```

1.  等待一段时间，然后它会显示 jest 单元测试脚本的结果，请参考下面的单元测试测试脚本，我们在我们的测试中得到:

[![Snapshot testing React Components with Jest](img/10eee28e344ac700569ac283763fec36.png "Snapshot testing React Components with Jest")](https://4.bp.blogspot.com/-BjBGlbCeupw/XDIhRj5g-iI/AAAAAAAACSs/mVXaYqiqiVc57NO7t4g7fYxrOojYoP77wCLcBGAs/s1600/11.PNG)

1.  在项目目录中，您会看到自动生成的**_ _ snapshot _ _**文件夹。参考下面的截图，这是我们在测试过程中捕获的。

[![Snapshot testing React Components with Jest](img/62b16c8ae565b94aba5a3a4f47129ded.png "Snapshot testing React Components with Jest")](https://3.bp.blogspot.com/-xV_L2RUZNv4/XDIh6Uzs6RI/AAAAAAAACS0/327EDxV5ioYdDYeB07rxIrio2dDacAVBACLcBGAs/s1600/22.PNG)

1.  让我们看看快照文件的内容。

## [T1**app . test . js . snap**](#apptestjssnap)

```
// Jest Snapshot v1, https://goo.gl/fbAQLP  

exports[`renders correctly 1`] = `  
<div  
  id="main-registration-container"  
>  
  <div  
    id="register"  
  >  
    <h3>  
      Registration page  
    </h3>  
    <form  
      method="post"  
      name="userRegistrationForm"  
      onSubmit={[Function]}  
    >  
      <label>  
        Name  
      </label>  
      <input  
        name="username"  
        onChange={[Function]}  
        type="text"  
        value={undefined}  
      />  
      <div  
        className="errorMsg"  
      />  
      <label>  
        Email ID:  
      </label>  
      <input  
        name="emailid"  
        onChange={[Function]}  
        type="text"  
        value={undefined}  
      />  
      <div  
        className="errorMsg"  
      />  
      <label>  
        Mobile No:  
      </label>  
      <input  
        name="mobileno"  
        onChange={[Function]}  
        type="text"  
        value={undefined}  
      />  
      <div  
        className="errorMsg"  
      />  
      <label>  
        Password  
      </label>  
      <input  
        name="password"  
        onChange={[Function]}  
        type="password"  
        value={undefined}  
      />  
      <div  
        className="errorMsg"  
      />  
      <input  
        className="button"  
        type="submit"  
        value="Register"  
      />  
    </form>  
  </div>  
</div>  
`; 
```

这都是关于使用 jest 脚本的单元测试。但是让我们想想这些场景:

1.  当用户误更改了 **App** 组件内容或修改了 **App.test.js.snap** 文件时。
2.  由于要求发生变化，用户修改了应用程序组件内容，但没有更新快照。在上述两种情况下，当我们执行快照测试时，它会在控制台中抛出如下错误消息。

[![Snapshot testing React Components with Jest](img/be4e0d25fd42e12434a1f2d959ec52f3.png "Snapshot testing React Components with Jest")](https://4.bp.blogspot.com/-Gz_IleL-yWQ/XDIk7hPL2WI/AAAAAAAACTA/cc2OLXU_aeUgfvLl598hI4zFaqTgE6cUQCLcBGAs/s1600/11.PNG)

[![Snapshot testing React Components with Jest](img/524e0333c4600f5773e198a9d00f62a4.png "Snapshot testing React Components with Jest")](https://3.bp.blogspot.com/-haSzt4KKBB0/XDIk_uBE1ZI/AAAAAAAACTE/tpdSYwErJ_Uxt8s7REdHj8JE68ylH_A2QCLcBGAs/s1600/22.PNG)

如果你想用最新的或者新的文件更新现有的快照文件，你需要在你的控制台上按下键盘上的 u 键。

[![Snapshot testing React Components with Jest](img/a4554dfc6e53ed9ae33f4403ece31429.png "Snapshot testing React Components with Jest")](https://1.bp.blogspot.com/-jW_9OXxWIfE/XDInGHuD7TI/AAAAAAAACTU/B_jIxNTfqtggdKCH0hsegcQvLOtC2kVaACLcBGAs/s1600/11.PNG) 
这都是关于**快照测试用 Jest 反应组件**。感谢你阅读这篇文章，如果你有任何问题，有关于这篇文章的另一个更好的有用的解决方案，请在评论区写消息。