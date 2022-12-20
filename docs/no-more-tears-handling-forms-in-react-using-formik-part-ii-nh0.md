# 不再流泪，使用 Formik 处理 React 中的表单，第二部分

> 原文：<https://dev.to/azure/no-more-tears-handling-forms-in-react-using-formik-part-ii-nh0>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 这是我们关于 Formik 的第一部分的继续，Formik 是 React 的神奇表单库

本文是系列文章的一部分:

*   [不再流泪，使用 Formik 处理表单，第一部分](https://dev.to/azure/no-more-tears-handling-forms-in-react-using-formik-part-i-20kp)
*   没有眼泪，使用 Formik 处理表单，第二部分，我们在这里

在本文中，我们将涵盖:

*   **模式验证**使用 Yup，有一种替代方法可以验证输入元素，那就是在 Yup 中声明一个模式，然后简单地将它分配给 Formik 组件上的一个属性
*   **异步**验证
*   **内置组件**，使用一些 Formiks 内置组件让一切变得不那么冗长

## 资源

我已经为这两篇文章做了回购，所以如果你卡住了，看看这里[表格演示回购](https://github.com/softchris/formik-example/)

## 内置组件

到目前为止，我们一直在使用常规的 HTML 元素，如`form`和`input`来构建我们的`form`，并且我们已经连接到了事件，如`onSubmit`、`onChange`和`onBlur`。但是我们实际上可以少打很多字。向以下组件问好:

*   **形成**，这取代了一个普通的`form`元素
*   **字段**，这将替换任何类型的`input`元素
*   **错误消息**，这并没有真正取代你已经有的任何控件，但它是一个很棒的组件，给定属性`name`就能显示你的错误消息

让我们先看一个简单的表单，然后用上面提到的组件重写它:

```
import { Formik } from 'formik';
import React from 'react';

const FormikExample = () => (
  <Formik
    initialValues={{ name: '' }}
    validation={values => {
      let errors = {};
      if(!values.name) {
        errors.name = 'Name is required';
      }
      return errors;
    }}
    onSubmit={values ={
      console.log('submitted');
    }}
  >
  {({ handleSubmit, handleChange, values, errors }) => (
   <form onSubmit={handleSubmit}>
    <input name="name" onChange={handleChange} value={values.name} />
    {errors.name && 
    <span>{errors.name}</span>
    }
   </form>
  )
  }
  </Formik>
) 
```

Enter fullscreen mode Exit fullscreen mode

好了，上面我们看到了一个最小的实现看起来像是经典的方式，那就是使用 HTML 元素，比如`form`和`input`。

现在让我们使用 Formiks 内置控件来清理这个问题:

```
import { Formik, Form, Field, ErrorMessage } from 'formik';
import React from 'react';

const FormikExample = () => (
  <Formik
    initialValues={{ name: '' }}
    validation={values => {
      let errors = {};
      if(!values.name) {
        errors.name = 'Name is required';
      }
      return errors;
    }}
    onSubmit={values ={
      console.log('submitted');
    }}
  >
  {({ handleSubmit, errors }) => (
   <Form onSubmit={handleSubmit}>
    <Field type="text" name="name" />
    <ErrorMessage name="name"/>
    }
   </Form>
  )
  }
  </Formik>
) 
```

Enter fullscreen mode Exit fullscreen mode

不是超级印象深刻？让我们列出不再需要键入的内容:

*   `onChange`从每个`input`元素中消失
*   `input`元素由`Field`组件代替
*   `form`元素由`Form`组件代替
*   条件`{errors.name &&`消失，同时`ErrorMessage`组件处理该位

不够？想象一下，你有 10 个字段，至少有 10 行代码消失了，通常看起来更整洁。现在到我们的下一个改进，我们可以用一个`schema`代替我们的`validation()`函数，接下来。

## 用 Yup 进行模式验证

好了，我们已经介绍了如何通过使用内置控件`Form`、`Field`和`ErrorMessage`来真正清理我们的标记。下一步是将我们的`validation`酒店换成`validationSchema`酒店，从而取得更大的进步。要做到这一点，我们需要使用库定义一个模式。那么模式看起来像什么呢:

```
import * as Yup from 'yup'

const schema = Yup.object().shape({
    firstName: Yup.string()
      .min(2, 'Too Short!')
      .max(50, 'Too Long!')
      .required('Required'),
    lastName: Yup.string()
      .min(2, 'Too Short!')
      .max(50, 'Too Long!')
      .required('Required'),
    email: Yup.string()
      .email('Invalid email')
      .required('Required'),
  }); 
```

Enter fullscreen mode Exit fullscreen mode

上面的模式定义了三个不同的字段`firstName`、`lastName`和`email`，并给出了它们应该遵守的每个属性:

*   **名字**，这应该是由最少 2 个字符和最多 50 个字符组成的字符串，也是必需的
*   **lastName** ，这也是一个具有相同最小/最大要求的字符串，也是必需的
*   **email** ，这只是一个必需的字符串

如你所见，上面的内容可读性很强，通过像这样定义你的数据，你不必键入大量的`if`结构来检查是否所有的属性都得到满足。

现在让我们把它用在我们的`Formik`元素中，就像这样:

```
<Formik validationSchema={schema}> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，这就是以一种真正有表现力的方式定义表单数据所需的全部内容，这难道不会给你一种温暖而模糊的感觉吗？:)

[![](img/7abc1ac86fe9aadceb5e4d9d413cc945.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LvMlGusl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/inn336qns6kslgfg1nun.gif)

## 异步验证

好了，现在我们的最后一个主题，异步验证。那么是什么情况呢？嗯，有时你有数据，你不能真正告诉客户端，只有输入的值是否正确。假设您有一个表单，您想在其中查找某个公司或某个网页域是否已被占用？此时，您很可能需要呼叫一个端点，而该端点不会立即返回答案。

好了，我们已经设置了场景，我们如何在 Formik 中解决这个问题？嗯，`validation`属性也能够接受承诺。真的吗，你觉得呢？这么容易吗？这个解决方案在我看来有点非正统，让我告诉你我的意思:

```
<Formik
  validate={values => {
    console.log('validating async');
    let errors = {};
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        errors.companyName = 'not cool';
        resolve('done');
      },3000);
      }).then(() => {
        if(Object.keys(errors).length) {
          throw errors;
        }
      });
    }}
>
// define the rest here
</Formik> 
```

Enter fullscreen mode Exit fullscreen mode

查看我们的`validate`实现，我们看到我们创建了一个承诺，它在内部运行一个`setTimout`来模拟它到达一个端点，这个端点需要时间来获得一个答案。此时，我们将一个`errors.companyName`设置为错误文本:

```
setTimeout(() => {
  errors.companyName = 'not cool';
  resolve('done');
},3000); 
```

Enter fullscreen mode Exit fullscreen mode

在更真实的场景中，我们可能会调用一个函数，根据函数的回答，我们可能会分配`errors.companyName`。下面我给你看看我的意思:

```
isCompanyNameUnique(values.companyName).then(isUnique => {
  if(!isUnique) {
    errors.companyName = `companyName is not unique, please select another one`
  }
  resolve('done')
}) 
```

Enter fullscreen mode Exit fullscreen mode

在我们的代码中发生的下一件事是我们调用`then()`，这发生在我们调用`resolve()`的时候。这里发生了非常有趣的事情，我们检查`errors`是否有任何可能已经被设置的属性，如果有，我们抛出一个错误，用我们的`errors`对象作为参数，就像这样:

```
.then(() => {
  if(Object.keys(errors).length) {
    throw errors;
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我不知道你怎么想，但对我来说，这看起来有点奇怪。我本以为给`validation`一个承诺意味着这个承诺的`reject()`会是一种更直观的方式，就像这样:

```
// this to me would have been more intuitive, but observe, this is NOT how it works, so DONT copy this text but refer to the above code instead

validation={ values => 
  console.log('validating async');
  let errors = {};
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      errors.companyName = 'not cool';
      reject(errors);
    },3000);
  })
}} 
```

Enter fullscreen mode Exit fullscreen mode

### 字段级异步

到目前为止，我们已经展示了如何在表单级别进行异步验证，但是如果你想一想，你真的想要这样吗？最有可能的情况是，您有多个字段，其中一些字段在客户端验证就足够了，而只有少数字段需要异步验证。在这种情况下，对每个字段应用验证是有意义的。这很容易通过键入以下内容来实现:

```
<Field name="username" validate={this.validate} > 
```

Enter fullscreen mode Exit fullscreen mode

如果您对字段进行异步验证，这可能是首选。至于其他字段，您可以验证客户端，在`Formik`组件`validationSchema`中定义这些字段并使用`Yup`模式可能是个好主意，正如我们上面所描述的。

### 告诫的话语

如果我们有异步验证，请确保您的验证不要运行得太频繁，尤其是在验证需要时间的情况下。您不希望每次键入一个键时都触发 3 秒钟的验证，您最多希望它在用户离开该字段开始在另一个字段中键入时触发，我们称之为`blur`事件。所以确保你像这样设置你的`Formik`组件:

```
<Formik
  validateOnBlur={true} 
  validateOnChange={false} > 
```

Enter fullscreen mode Exit fullscreen mode

这就是你想要的，将`validateOnBlur`设置为`true`就是你想要的，尽管从技术上讲这是默认的`true`。你想通过`validateOnChange`明确下一个。你想关闭这个，或者设置为`false`。

## 总结

我们已经着手覆盖内置组件，如`Form`、`Field`和`ErrorMessage`，最终结果是我们清理了大量代码。

此外，我们展示了如何通过使用`Yup`库定义一个模式来消除我们的验证功能。

最后，我们讨论了异步验证，并讨论了需要考虑的事项，如何时进行验证，以及最好对表单中的少数异步字段进行字段级验证，并对其余字段使用模式验证。

就这样，我们的文章到此结束。我希望这一部分和前一部分给了你新的希望，在 React 中处理表单并不需要那么痛苦

[![](img/d633c75228c188774197cd6178f8e95a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wTGkQ7dr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wunzt3wvfpyx1dk1lfz9.gif)