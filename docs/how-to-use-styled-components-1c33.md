# 如何使用样式化组件

> 原文：<https://dev.to/thevenicelive/how-to-use-styled-components-1c33>

我在这个迷你项目中使用的库:

1.  样式组件
2.  创建-反应-应用

如果你想使用 CSS-in-JS，但不知道如何在 React 中使用样式化组件，我希望你能获得足够的基础知识来开始使用它。我们将通过在 CRA 使用样式组件创建一个简单的登录表单(没有验证的东西)来学习。

让我们从安装开始:
打开您的终端，写下:

```
npm install --save -g styled-components 
```

* * *

这是我们第一个在 CRA 显示标签和输入的变量 div 的例子:
fileName: StyledForm.jsx

```
import styled from "styled-components"

 //now below style a parent div as a variable and name it 
 //Note: always use Pascal Case for Naming Styled Components
 //we use styled.variableType to create a variable 

const ParentForm = styled.form`
  display: grid;
  grid-template-columns;1fr;
  grid-template-rows:auto;
    border: 1px solid Black;
    margin: auto;
    *{
      margin: auto;
      padding: 0.5rem 1rem;
      display:grid
    }

`;

// don't export default anything from styled components file

export {ParentForm}

```

好了，这里我们创建了第一个样式变量，它是登录表单的父 div。

* * *

现在让我们创建主窗体组件。

文件名:Form.jsx

```
import React from "react";

//create a stateful class

class Form extends React.Component {}

```

现在我们导入在 StyledForm.jsx 中创建的 ParentForm。

```
import React from "react";import {
  ParentForm
} from "../StyledForm.jsx";

//create a stateful class

class Form extends React.Component {}

```

## 现在我们将创建一个渲染函数，我们将返回这个组件。

```
import React from "react";import {
  ParentForm
} from "../StyledForm.jsx";

//create a stateful class

class Form extends React.Component {
render(){
return Here we will add form labels and inputs 
}
}

```

* * *

但是等等，如果你不得不多次添加同一个模板呢？为了提高效率，我们将创建一个函数，它将表单
的所有元素作为参数，并返回给我们模板 html。

```
import React from "react";import {
  ParentForm
} from "../StyledForm.jsx";

//create a stateful class

class Form extends React.Component {
render(){  
const functionalDiv = (id, type, placeHolder) => {
      return (

          {id}

      );
    };
return Here we will add form labels and inputs 
}
}

```

* * *

现在让我们添加其余的组件:

```
import React from "react";
import {
  ParentForm
} from "../StyledForm.jsx";

//create a stateful class

class Form extends React.Component {
render(){  
const functionalDiv = (id, type, placeHolder) => {
      return (

          {id}

      );
    };
return (
    <>

            请登录以访问您的帐户。

          {functionalDiv("username", "text", "enter username")}
          {functionalDiv("password", "password", "enter password")}

             alert("Successfully Logged in ")}
            >
              Submit

      >

)
}
}

```

* * *

现在创建一个 onSubmit Stylebutton，它将阻止整个页面刷新。

```
const onSubmit=e=>{
e.preventDefault()
}
```

Now we will create the remaining styling for the form and export them.

```
import styled from "styled-components";
const ParentForm = styled.form`
  display: grid;
  grid-template-columns;1fr;
  grid-template-rows:auto;
    border: 1px solid Black;
    margin: auto;
    *{
      margin: auto;
      padding: 0.5rem 1rem;
      display:grid
    }

`;
const HeaderForm = styled.div`
  > p {
    font-size: 1.5em;
    font-weight: 600;
  }
`;
const FormMainBody = styled.div`
  grid-template-columns: 1fr;
  grid-template-rows: 1fr 1fr;
  margin: auto;
  label {
    text-align: left;
    text-transform: uppercase;
    font-weight: 600;
  }
  input {
    border: 1px solid Black;
    padding: 5px 10px;
    caret-color: green;
  }
`;
const StyleButton = styled.Stylebutton`
  padding: 5px 20px;
  color: white;
  background-color: ${props => [props.bgcolor]};
`;

export { ParentForm, HeaderForm, FormMainBody, StyleButton };

```

* * *

下面是最终的 Form.js 文件:

```
import React from "react";
import {
  ParentForm,
  HeaderForm,
  FormMainBody,
  StyleButton
} from "../StyledForm.jsx";

class Form extends React.Component {
  render() {
    const functionalDiv = (id, type, placeHolder) => {
      return (

          {id}

      );
    };
    const onSubmit = e => {
      e.preventDefault();
    };
    return (

            请登录以访问您的帐户。

          {functionalDiv("username", "text", "enter username")}
          {functionalDiv("password", "password", "enter password")}

             alert("Successfully Logged in ")}
            >
              Submit

    );
  }
}
export default Form;

```

* * *

这就是我们如何使用样式化组件在表单中创建了一个简单的登录。
这里是 codesandbox 链接:[这里](https://codesandbox.io/embed/6zo26xvv43)T3】如果你在 Twitter 上有任何关于这个 DM me 的问题。