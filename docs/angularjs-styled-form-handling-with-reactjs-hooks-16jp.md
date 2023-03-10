# 使用 ReactJs 挂钩的 AngularJs 风格的表单处理

> 原文：<https://dev.to/kapilgorve/angularjs-styled-form-handling-with-reactjs-hooks-16jp>

[![](img/56ff1620dfb7a96c1c2e90f03a8f6830.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nDOqwXTo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://friendly-heyrovsky-86d9f9.netlify.app/opengraph%3F%26author%3Dkapilgorve%26title%3DAngularJs%2520styled%2520form%2520handling%2520with%2520ReactJs%2520Hooks%26tags%3DReactJs%2CUX)

### 我喜欢 AngularJs 表单验证

我倾向于 AngularJs(不要被后来的版本称为 Angular)所混淆。我开始和 AngularJs 一起在前端空间工作。我喜欢对我来说如此简单。我认为我在更短的时间内发布了很多复杂的功能。无论如何，我真的很喜欢 AngularJs 的表单验证。

但是不可能有相似的 API。我打算在这里解决安古拉杰风格的形式验证 UX。

## 给我看看代码

如果你想直接出去玩这个演示，那就来吧。

这是一个使用钩子的基本 React 函数组件。以防你错过了聚会。

根据 React 文件-

> 钩子是 React 16.8 中的新增功能。它们允许您使用状态和其他 React 特性，而无需编写类。

```
import React, { useState } from 'react';

export function FormInput(props) {
  const [inputValue, setValue] = useState('');

  const handleInput = (e) => {
      setValue(e.target.value);
  }

  return (
    <div style={inputWrap} >
        <label>{props.label}</label>
      <input
        style={inputStyle}
        value={inputValue}
        onChange={handleInput}
      />
    </div>
  );
} 
```

`useState`是一个钩子，可以让你给函数组件添加反应状态。什么钩子？你可以在这里查阅官方文件，了解更多关于钩子的细节。相信我，他们很棒。

### 那么这到底是怎么回事呢？

我们在 React 中导入一个预定义的钩子，命名为`useState`。

`import React, { useState } from 'react';`

这将让我们在函数组件中使用 React 的状态概念。如果你不是来自 React Land，在此之前我们不能在函数组件中使用状态。函数组件用来传递来自父组件的状态，然后呈现通过`props`传递给它的任何东西。

`const [inputValue, setValue] = useState('');`

`inputValue`是我们的状态变量，我们用`useState('')`将它初始化为空字符串。`setValue`是我们可以用来更新`inputValue`的函数。

```
 const [isTouched, setTouched] = useState(false);
  const [inputValue, setValue] = useState('');
  const [isValid, setValid] = useState(true); 
```

我们声明另外两个状态变量

1.  `isTouched` -跟踪用户是否触摸了输入。
2.  `isValid` -跟踪输入值是否有效。

```
 <input
        style={inputStyle}
        value={inputValue}
        onChange={handleInput}
        onBlur={ ()=> setTouched(true)}
      /> 
```

我们为`onBlur`添加一个处理程序。当用户触摸输入并离开输入时，将触发`onBlur`事件。我们将使用这个事件来设置我们的状态变量`isTouched`。现在我们知道用户何时触摸了输入，如果用户没有输入，我们可以显示一个错误。

```
 <input
        style={inputStyle}
        value={inputValue}
        onChange={handleInput}
        onBlur={ ()=> setTouched(true)}
    />
      { touched && !inputValue && <p style={{ color: 'red' }}>Please enter value</p>} 
```

让我们再走一步，验证输入，如果我们认为输入无效，就显示一个错误。我们不想打扰用户，除非他们已经输入了值，然后离开输入。我被即时错误激怒了，这些错误甚至在我还没有完成的时候就出现了。所以让我们先给用户一个机会，不要说他们愚蠢。

```
App.js -

 <div className="App">
        <FormInput required={false} type="text" label="Name" />
        <FormInput required={true} type="email" label="Your Email"/>
        <FormInput required={true} type="number" label="Pin"/>
 </div>

  const handleInput = (e) => {
      setValue(e.target.value);
      validate(e.target.value);
  }

  const validate = (val) => {
      if(props.type === 'email') {
        if(emailIsValid(val)) setValid(true);
        else setValid(false);
      }
      if(props.type === 'number') {
        if(isNum(val)) setValid(true);
        else setValid(false);
      }
}

....

     <input
        style={inputStyle}
        value={inputValue}
        onChange={handleInput}
        onBlur={ ()=> setTouched(true)}
      />
      {props.required && touched && !inputValue && <p style={{ color: 'red' }}>Please enter value</p>}
      {touched && !isValid && <p style={{ color: 'red' }}>Please enter valid value.</p>}

...

function emailIsValid (email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)
  }

  function isNum(val){
   return /^\d+$/.test(val);
  } 
```

我们验证输入值。如果输入值无效，我们等待用户离开输入并显示错误。传递输入类型并基于正则表达式进行验证。

`<FormInput required={true} type="number" label="Pin"/>`

## 为什么我更喜欢这种方法？

我和很多人讨论过表单验证的方法。对于企业客户，产品经理和其他人在表单提交后参与验证。我不喜欢即时验证，因为它们会提醒用户你不知道你在做什么。我也不喜欢表单提交后的验证。在某些情况下，这可能意味着输入一个`captcha`输入。这些验证码可能是对残疾人或视力不好的人的直接骚扰。我想采取中间路线。让用户填写输入，并在离开输入后显示一个验证错误。

本帖最初发表于[https://www . jskap . com/blog/angular js-styled-form-handling-with-React-hooks/](https://www.jskap.com/blog/AngularJS-styled-form-handling-with-React-hooks/)

👋嗨！我是卡皮尔。我总是喋喋不休地谈论建造东西，分享我的知识，自由职业。来 https://twitter.com/kapilgorve 和我打招呼吧