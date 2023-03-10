# uniform:react 应用程序的权威表单库

> 原文：<https://dev.to/italomlp/unform-the-definitive-form-library-for-react-apps-35lh>

我们所有人，React 开发人员——甚至是已经听说过 React 生态系统的 javascript 开发人员——都知道一件事:React 应用程序中的表单真的很麻烦而且冗长(就像 [Formik 的开发人员所说的一样](https://github.com/jaredpalmer/formik))。

我们有 [Redux Forms](https://github.com/erikras/redux-form/) 、Formik 和许多其他库来使表单在 React 中发生。但是第一次没有说服我...为什么？我不同意在 Redux 中保持表单状态(我们可以继续做朋友吗？).第二个非常强大...但是(如果后面有“但是”就没关系了)由于非常强大，它对于简单的表单变得非常冗长或复杂(对于大表单也有性能问题，将在另一篇文章中讨论)。这些库中的大多数都使用输入字段的受控组件。如果您要处理大型表单和/或需要高性能，那么在项目中包含它并不总是更好的事情。

我们能做什么呢？因为这几点，位于巴西的一个名为 [Rocketseat](https://rocketseat.com.br/) 的生活方式/公司/社区让[变得统一](https://github.com/Rocketseat/unform):

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [火箭座](https://github.com/Rocketseat) / [未定型](https://github.com/Rocketseat/unform)

### 高度可扩展的 ReactJS & React Native forms！🚀

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Unform](img/b1ba64e59cbc86ead058642b8632716d.png)](https://camo.githubusercontent.com/c1abe5344f2db0df1eefb99431ad017de75b3318/68747470733a2f2f73746f726167652e676f6f676c65617069732e636f6d2f676f6c64656e2d77696e642f756e666f726d2f756e666f726d2e737667)

### 高度可扩展的 ReactJS & React Native forms！<g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji>

[![npm](img/35993b4da2f1c3e70f44c43e7d14e240.png) ](https://www.npmjs.com/package/@unform/core) [ ![Coverage Status](img/27ee58bf9de743a06a7b79b0503a39c2.png)](https://coveralls.io/github/Rocketseat/unform?branch=master)

## 概观

Unform 是一个注重性能的库，它可以帮助您在 ReactJS & React Native 中创建漂亮的表单，并具有不受控制的组件性能和 React 钩子的功能。

*   **[入门](https://unform.dev/)T3】**
*   **[文档](https://unform.dev/guides/basic-form)**
*   **[为什么 un format？](https://unform.dev/why-unform)T3】**
*   **[常见问题](https://unform.dev/faq)**

想在穿制服前测试一下吗？

*   **[ReactJS 游乐场](https://codesandbox.io/embed/agitated-tdd-uf177?autoresize=1&expanddevtools=1&fontsize=14&hidenavigation=1&theme=dark)T3】**
*   **[反应过来原生游乐场](https://snack.expo.io/@diego3g/1e9fb3)**

【React Native Web 或 Expo Web 不支持，请使用 Expo 零食中的 iOS/Android 设备

## 贡献的

感谢您对改进这个包感兴趣。我们鼓励每个人都来帮助改善这个项目的一些新功能，错误修复和性能问题。请花一点时间阅读我们的指南，这样这个过程会更快更容易。

### 投稿指南

花点时间阅读一下我们的[投稿指南](https://raw.githubusercontent.com/Rocketseat/unform/master//.github/CONTRIBUTING.md)，这样你就能了解如何提交问题、提交和创建拉式请求。

### 行动守则

我们期待你…

</article>

[View on GitHub](https://github.com/Rocketseat/unform)

让我们看看它的实际效果吧！

> 你可以在[https://github.com/italomlp/unform-example](https://github.com/italomlp/unform-example)查看这个例子的源代码

## 1。我们需要什么？

*   [NodeJS 和 NPM](https://nodejs.org/) (LTS 版？)
*   [纱线](https://yarnpkg.com/)(这是可选的，但速度更快)
*   [创建 React 应用](https://github.com/facebook/create-react-app)
*   [未格式化](https://github.com/Rocketseat/unform)
*   [是](https://github.com/jquense/yup)(用于验证)
*   [React Datepicker](https://github.com/Hacker0x01/react-datepicker/) (用于 Datepicker 和演示第三方组件集成的组件)
*   浏览器(我用谷歌浏览器)
*   一个文本编辑器(我使用 [VS 代码](https://code.visualstudio.com/)
*   终端(我用 [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh)

## 2。初始化一个反应应用程序

```
yarn create react-app form-example 
```

或者

```
npx create-react-app form-example 
```

或者在 create-react-app 储存库中描述的另一种方式

你会看到下面的结果:
[![create-react-app result](img/f62bc96a1e45ccc416f0f94aee80f09e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R0Ue3-8w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/H1kUi3B.png)

然后我们可以用以下代码运行我们的项目:

```
cd form-example/
yarn start 
```

并在浏览器中看到如下:
[![create-react-app start](img/565ded4710ff0fb05c98423fcaa06948.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hD1GFy3R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/IA62CgZ.png)

## 3。清理 src 文件夹并安装依赖项

创建 app 后，我们会有这样的文件夹结构:
[![react-app folder structure](img/37e6585f73f53fc7e345a2e07959cb0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BZVgw-ty--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/nKSK5r5.png)

首先，我将删除`src/App.test.js`、`src/App.css`、`src/logo.svg`和`src/serviceWorker.js`文件，我们不会在这篇文章中使用它们。

```
rm src/App.test.js src/App.css src/logo.svg src/serviceWorker.js 
```

然后我们需要安装我们的依赖项。对于验证目的来说，`yup`是一个很好的选择(并且是来自 unform 团队的建议)。而`react-datepicker`是一个很棒的组件，用来演示 Unform 和 third libs 之间的集成。

```
yarn add @rocketseat/unform yup react-datepicker 
```

所以，我们可以开始编码了。

## 4。制作表格

如果你看看你的浏览器，你会发现这个应用程序不再编译了。为了避免这种情况，我们必须将我们的`src/index.js`改为:

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root')); 
```

还有我们的`src/App.js`到:

```
import React from 'react';

function App() {
  return (
    <div>
      <h1>Contact form</h1>
    </div>
  );
}

export default App; 
```

Unform 的使用非常简单:我们导入一些东西并在我们的 JSX 中使用它们。让我们看看。

```
// ...
import { Input, Form } from "@rocketseat/unform";

function App() {
  return (
    <div>
      <h1>Contact form</h1>
      <Form>
        <Input name="fullname" label="Full name" />
        <Input name="phone" label="Phone" />
        <Input name="email" label="Email" />

        <button type="submit">Save</button>
      </Form>
    </div>
  );
} 
```

这个很好用...

#### 但是我们怎么才能得到数据呢？

简单:向表单组件传递一个句柄提交函数。

```
// ...
const handleSubmit = data => {
    console.log(data);
};

return (
  <div>
    <h1>Contact form</h1>
    <Form onSubmit={handleSubmit}>
    {/* ... */} 
```

很好。结果是:
[![unform working](img/a6928f1213998dd342ef9ea0b0bc3ab2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IwKi3yVw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/pAQqtR4.png)

#### 但是，如果我想要嵌套字段呢？

酷，只需使用`Scope`组件来包装嵌套字段。如您所见，我添加了这样的地址字段:

```
import { Input, Form, Scope } from "@rocketseat/unform"; // added Scope component import

// ...
<Form onSubmit={handleSubmit} >
  {/* other Input components */}

  <Scope path="address">
    <Input name="city" label="City" />
    <Input name="state" label="State" />
  </Scope>
  {/* ... */}
</Form> 
```

组件用来告诉 React:“嘿，我的孩子是我所代表的对象的属性”。并将结果归纳为:
[![unform scope](img/90939fa59a5bfa55ea37fd4945c7658a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jD4eI_Cg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/0eJHTDd.png)

#### 如果我想用初始值填充表单呢？

你可以。只需将一个对象传递给`Form`组件的`initialData`道具:

```
// ...
const initialData = {
  fullname: "Italo Menezes",
  phone: "999999999",
  email: "italo@email.com",
  address: {
    city: "Rio de Janeiro",
    state: "Rio de Janeiro"
  }
};

// ...

<Form onSubmit={handleSubmit} initialData={initialData}>
  {/* ... */}
</Form> 
```

#### Ok ok。但是认可有时是一种痛苦。怎么才能用 Unform 做出来？

对于验证，我们集成了`Yup`，它只是一个对象模式验证器。当我说“简单”的时候不要误解我。它非常强大。我的意思是，权力并不总是那么复杂。让我们来看看。

通过对象模式验证，我们可以为对象属性声明规则，使它们成为具有最小或最大长度的字符串，匹配正则表达式，等等。

我们将通过以下规则验证我们的表单:

*   全名必须至少有 5 个字符。必需的。
*   电话号码必须是数字，并且正好有 9 个数字。必需的。
*   电子邮件必须是电子邮件格式(如此明显)。不需要。
*   城市不是必需的。
*   该州不是必需的。

所以，用 Yup，让我们创建这个对象模式:

```
const schema = Yup.object().shape({
  fullname: Yup.string()
    .min(5, "The FULL name is only this?")
    .required("Full name is required."),
  phone: Yup.string()
    .matches(/^[0-9]{9}$/g, "Is this a phone number?")
    .required("Phone is required."),
  email: Yup.string().email("Is this an email?"),
  address: Yup.object().shape({
    city: Yup.string().notRequired(),
    state: Yup.string().notRequired()
  })
}); 
```

并将其添加到`Form`组件:
中的`schema`道具

```
<Form onSubmit={handleSubmit} schema={schema}>
  {/* ... */}
</Form> 
```

### 停！让我们添加一个简单的 CSS。这个项目变得很难看！

将`src/index.css`的代码替换为:

```
body {
  padding: 10px;
}

input {
  display: block;
  margin-bottom: 10px;
}

label {
  display: block;
}

span {
  display: block;
  font-size: 10px;
  color: red;
  margin-bottom: 15px;
} 
```

### 返回验证...

如果您现在运行它，并在没有输入值的情况下单击 save，您将得到这个:
[![yup validations](img/7fc80ab624a120e6f6901dd1a0d82ee6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O33c4ij7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/bwBeHGr.png)

#### Ok。到目前为止都很好。如果我需要在表单中使用我自己的输入字段呢？

Rocketseat 的开发人员也考虑到了这一点，并制作了一个名为`useField`的钩子来与其他组件一起使用。

我们将使用最初添加到我们项目中的`react-datepicker`。为此，我们需要包装它并像这样添加`useField`:

```
import React, { useEffect, useState, useRef } from "react"; // add the hooks

import { Input, Form, Scope, useField } from "@rocketseat/unform"; // useField hook
import * as Yup from "yup";

import ReactDatepicker from "react-datepicker"; // react datepicker component
import "react-datepicker/dist/react-datepicker.css"; // react datepicker css

// ...

const Datepicker = ({ name, label }) => {
  const ref = useRef(null); // for ref manipulation purposes
  const { fieldName, registerField, defaultValue, error } = useField(name); // the name of the prop in form object is used here
  const [selectedDate, setSelectedDate] = useState(defaultValue); // the state of our datepicker component

  useEffect(() => {
    registerField({ // here, we're registering the field in the whole form
      name: fieldName,
      ref: ref.current,
      path: "props.selected", // this is the path to selected date in ReactDatepicker (wich is the selected prop)
      clearValue: pickerRef => { // for reset purposes
        pickerRef.clear();
      }
    });
  }, [fieldName]);

  return (
    <>
      {/* the label is like label in Unform Input component */}
      {!!label && <label htmlFor="datepicker">{label}</label>}
      <ReactDatepicker
        id="datepicker"
        name={fieldName}
        selected={selectedDate}
        onChange={date => setSelectedDate(date)}
        ref={ref}
      />
      {/* the error is like error in Unform Input component */}
      {error && <span>{error}</span>}
    </>
  );
};

// ...
  <Form onSubmit={handleSubmit} schema={schema}>
    {/* ... */}
    <Datepicker name="birthDate" label="Birth date" />
    {/* ... */} 
  </Form> 
```

好了，我在代码中添加了注释，希望你理解。

所以，这就导致了这个:
[![with datepicker](img/2a13219eed7cfc82ed3fdd4ce86edc78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RN5MMqZV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/3zqNcES.png) 
[![result with datepicker](img/98ae3ac676dfba62d9b43f5541d7c66f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LJ7xyi6v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vaJx1VM.png)

> 如果你对 React hooks 不熟悉，我推荐一本阅读:
> Hooks 一瞥:[https://reactjs.org/docs/hooks-overview.html](https://reactjs.org/docs/hooks-overview.html)

#### 最后，如果我想在提交后重置值呢？

`Form`的`onSubmit`函数有第二个参数，它是一个对象。这个对象只有一个属性助手，叫做`resetForm`。我们可以这样使用:

```
const handleSubmit = (data, { resetForm }) => {
  console.log(data);
  resetForm();
}; 
```

* * *

我们结束了。你可以在 Github 的 Unform 库中看到更多的例子和文档。Unform 还在它的初始版本中，但是有一个很棒的社区和我从未见过的最好的开发者。

如果你喜欢这篇文章，分享并给它一个❤️.还有，你可以在 Github 和社交媒体上关注我。感谢您的阅读，再见！