# 通读项目:Formy

> 原文：<https://dev.to/copperwall/reading-through-a-project-formy-34o6>

有时候，拿一个软件项目，通读源代码是有帮助的。如果文档足够好或者界面足够简单，你可能不知道项目的大部分是如何工作的，但是有时候看得更深一点也不错。我在 iFixit 的几个项目中使用了一个名为 Formy 的 React 表单库。Formy 允许您使用 JavaScript 对象配置表单，并使用 React 组件将该配置应用于视图。文档中有很多[例子](https://github.com/iFixit/formy/tree/master/src/example)，这使得开始使用它变得非常容易，但是说实话，我并不真正理解它的大部分是如何工作的。这里是我尝试学习更多一点关于它。

## 从哪里开始

从模块的入口点开始寻找可能不是一个坏主意。在由 json 文档的主字段指定的 package.json 文件中。对于 Formy 来说，它是 *dist/Formy/Form.js* ，但是这个文件没有在 GitHub 中显示。dist 目录是将 src 目录中的每个文件转换成 ES5 目标的构建步骤的结果，所以可以肯定地说，我们可以将 *src/Formy/Form.js* 视为入口点。 *src/example* 和 *src/index.js* 目录和文件仅用于文档和开发，因此可以忽略。

## 出口

*Form.js* 负责导出库的用户可以访问的函数和数据。该文件指定了一个名为*表单*的默认导出，它是一个包含命名函数的对象。看起来*表单*没有任何状态或原型(除了对象原型)，所以它所保存的函数可以被视为静态函数，可以单独查看。

### 形态。成分

```
Form.Component = ({ id, name, onSubmit, children }) => (
   <form
      id={id}
      name={name}
      onSubmit={onSubmit}
   >
      {children}
   </form> );

Form.Component.propTypes = {
   id: PropTypes.string,
   name: PropTypes.string,
   onSubmit: PropTypes.func,
}; 
```

Enter fullscreen mode Exit fullscreen mode

组件是一个以 *id* 、*名字*、 *onSubmit* 和*子*为道具的功能性 React 组件。该功能组件的返回值是一个应用了这些属性的表单。包含在*表单中的任何子组件。组件*被传递给表单组件。这可能用于包含表单输入或提交按钮作为表单的子元素。

Component 似乎是 React 组件的一种通称。也许把它命名为 Form 更好，因为它包装了一个实际的 form JSX 标签。

### 形态。田

形式。字段是在一个单独的文件中定义的，所以我还不完全确定这是什么意思。为什么 FormField 在不同的文件中，而不是 Form。组件？这可能会让事情看起来更加一致。我们可以在浏览完 *Form.js* 之后再来讨论这个问题。

### form . customvalidityfactory

```
Form.customValidityFactory = (constraint, validationMessage = 'Invalid') => (...args) => (
   constraint(...args) ?  '' : validationMessage
); 
```

Enter fullscreen mode Exit fullscreen mode

接受约束和验证消息并返回函数的函数，该函数接受可变数量的参数，并将其参数应用于第一个函数中提供的约束函数，如果为 true，则返回空字符串，否则返回 validation message。如果自定义有效性工厂让有效性约束函数返回自己的错误消息，而不仅仅是空字符串 vs validationMessage，这可能会很酷。这会允许多条验证消息吗？

customValidityFactory 的最终结果是用字符串调用表单输入上的 *setCustomValidity* ，该字符串是调用传递给结果函数的参数上的约束函数的结果。但是，这发生在组件库中，而不是 Formy 本身。Formy 假设将一个 *customValidity* 属性传递给一个输入组件将会正确地处理这个问题，所以知道您是否想要包含自己的组件库以用于 Formy 是很重要的。

### 表单.字段

```
Form.fields = (globalProps = {}, fields) => Object.assign({},
   ...Object.entries(fields).map(([fieldKey, field]) => ({
      [fieldKey]: {
         ...Form.Field.defaultProps,
         ...{ name: fieldKey },
         ...globalProps,
         ...field,
      },
   })),
); 
```

Enter fullscreen mode Exit fullscreen mode

接受 globalProps 和字段定义对象的函数。当您想要使用同一个 onChange 处理程序时，全局属性非常有用。全局字段属性将应用于任何字段组件，除非被单个字段本身覆盖。设置一个全局属性 *onChange* 来更新任何表单域的状态是全局属性的一个很好的例子。这个函数的返回值是一个带有表单输入名称键的对象，这些键映射到一个带有表单输入属性的对象。

### [表单. getData](#formgetdata)

```
Form.getData = form => Object.assign({},
   ...Object.entries(Form.getProps(form).fields)
   .filter(([fieldKey, field]) => !field.disabled)
   .filter(([fieldKey, field]) =>
      !['checkbox', 'radio'].includes(field.type) || field.checked
   )
   .map(([fieldKey, field]) => ({ [fieldKey]: field.value })),
); 
```

Enter fullscreen mode Exit fullscreen mode

哇，这个函数有点密集。gist 看起来像是从每个表单字段的值中返回一个带有数据的对象，但不包括未选中的单选或复选框字段或禁用的字段。返回对象的形状是映射到该字段值的字段名键。如果您想从表单中获取输入数据以便提交，这尤其有用。

### Form.getProps

```
Form.getProps = form => Object.assign({},
   ...Object.entries(form)
   .filter(([formPropKey, formProp]) => formPropKey !== 'fields')
   .map(([formPropKey, formProp]) => ({
      [formPropKey]: formProp instanceof Function ? formProp(form) : formProp,
   })),
   {
      fields: Object.assign({}, ...Object.entries(form.fields).map(([fieldKey, field]) => ({
         [fieldKey]: Object.assign({}, ...Object.entries(field).map(([fieldPropKey, fieldProp]) => ({
            [fieldPropKey]: fieldProp instanceof Function ? fieldProp(form, fieldKey) : fieldProp,
         }))),
      }))),
   },
); 
```

Enter fullscreen mode Exit fullscreen mode

getProps 遍历所有非“字段”字段，如果值是一个函数，则用 form 调用它。这种属性的一个例子是 Form.onSubmitFactory 的返回值，它需要一个表单并返回一个实际表单上的事件处理程序。“fields”字段映射每个表单字段名称，对于每个表单字段属性，如果是函数，它会将表单和字段名称传递给函数值。Form.customValidityFactory 就是一个很好的例子，它采用一个约束函数，并返回一个采用 Form 和 fieldKey 的函数，由 Form.getProps 调用。

对于这里进行的所有 ES6+魔术，我们基本上是映射一个充满表单级属性的对象，并通过将它们与表单对象和字段键(如果是表单字段属性)一起应用来转换作为函数的属性。

哇，这里发生了很多事情。从示例来看，这似乎返回了一个可以传递给 Form 的道具列表。组件和形式。用户组件呈现方法中的字段。

这个函数(和 Form.getData)大量使用了 *Object.assign* 。 *Object.assign* 实际上是做什么的？

*Object.assign* 就像一个对象扩展操作符。第一个参数是目标对象，所有其他参数是将字段复制到目标对象的源。较新的源属性会覆盖较早的属性。看起来它的大多数用法使用一个空的目标对象和一个从全局到更具体的属性的源列表。 *Object.assign* 也可以接受一个对象数组的源，它将这些对象合并在一起，然后复制到目标对象中。

该项目的 babelrc 指定使用*transform-object-rest-spread*插件，所以也许那些* Object.assign *可以转换成使用 object spread 操作符。

### Form.onChangeFactory

```
Form.onChangeFactory = fn => (form, fieldKey) => updatedProps => fn({
   ...form,
   fields: {
      ...form.fields,
      [fieldKey]: {
         ...form.fields[fieldKey],
         ...updatedProps,
      },
   },
}); 
```

Enter fullscreen mode Exit fullscreen mode

一个采用处理函数 *fn* 的函数，该函数返回采用*表单*和*字段键*的函数，该函数返回采用 updatedProps 对象的函数，该对象将处理函数应用于以*表单*为基础的合并对象，一个被覆盖的*字段*键带有来自*表单的键。字段*带有被 updatedProps 对象覆盖的*字段键*键。

示例处理函数接收带有更新字段的新表单对象，并使用新表单状态调用 setState。有趣的是，为了让表单工作，你必须指定它。也许这是一个不错的默认。

### Form.onSubmitFactory

```
Form.onSubmitFactory = fn => form => ev => {
   ev.preventDefault();
   fn(Form.getData(form));
}; 
```

Enter fullscreen mode Exit fullscreen mode

一个采用处理函数 *fn* 的函数，该函数返回一个采用表单对象的函数，该函数返回一个采用事件的函数，我假设该事件是提交事件。该函数阻止提交事件的默认行为，调用表单对象上调用 *getData* 的结果的处理函数。这对于指定提交表单时要做什么很有用，比如发送 AJAX 请求或用表单数据创建一些动作。

调用 Form.onSubmitFactory 得到的函数用作表单状态下 onSubmit 键的值。表单。组件 Component 需要一个接受事件的 onSubmit 函数。为了将表单状态下的 onSubmit 函数转换为 onSubmit 函数 prop，请在表单状态下调用 From.getProps。这将向状态中的 onSubmit 函数提供表单状态，该函数接受表单并返回接受事件的函数。调用该函数的结果将。

### FormField.js

```
import React from 'react';
import FormFieldPropTypes from './FormFieldPropTypes';
import FormDefaultComponentLibrary from './FormDefaultComponentLibrary';

const FormField = ({componentLibrary, ...props}) => {
   const Component = componentLibrary[props.type];
   return <Component {...props} />; }

FormField.defaultProps = {
   checked: false,
   componentLibrary: FormDefaultComponentLibrary,
   type: 'text',
   value: '',
};

FormField.propTypes = FormFieldPropTypes;

export default FormField; 
```

Enter fullscreen mode Exit fullscreen mode

所以 FormField 实际上并没有那么复杂。FormField 是 functional React 组件，它接受组件库和类型属性以及附加属性。给定的类型属性被用作 componentLibrary 对象中的键来获取组件，返回值是该组件的 JSX 和给定给 FormField 的属性。

FormField 指定了一些默认属性，如*被检查的*、*组件库*、*类型*、*值*。 *Checked* 默认为 false， *componentLibrary* 默认为[工具箱](https://github.com/ifixit/toolbox)， *type* 默认为文本， *value* 默认为空字符串。对于违约来说并不奇怪。

FormField 的 propTypes 是从 FormFieldPropTypes.js 文件导入的。也许这是由组件库指定的更好的东西？我不确定。