# react-use-form:基于表单实用程序的类型安全 react 挂钩

> 原文：<https://dev.to/hachibeedi/react-use-form-type-safe-react-hooks-based-form-utility-4k5h>

在 React 中管理表单是一项艰难而又无聊的任务。此外，如果您更喜欢 TypeScript，那么您将遭受由于原生 DOM 输入限制而导致的类型不安全性。

有几个库可以解决这个问题。反应形式，还原形式，形式...现在我添加了一个新的名字叫马约加。

[https://github.com/hachibeeDI/mayoiga](https://github.com/hachibeeDI/mayoiga)

马约伊加的特点如下:

*   React 挂钩基础(使用表单来调用表单组件)
*   严格的类型检查(我喜欢 TypeScript！)
*   简单的 API，学习成本更低

## 如何使用

这是最小的工作示例。

```
import * as React from 'react';
import {FC} from 'react';

import {useForm, createFormScope} from 'mayoiga';
import {Input, NumberInput} from 'mayoiga/lib/forms';

const required = (target: string) => (target.length === 0 ? 'required' : undefined);

const between = (min: number, max: number) => (target: number) => {
  if (target < min) {
    return `more than ${min}`;
  }
  if (target > max) {
    return `less than ${max}`;
  }
};

const choice = function<T>(...candidates: Array<T>) {
  return (target: T) => (candidates.includes(target) ? undefined : 'You should choose from the candidates.');
};

const INITIAL_FORM_STATE = {
  name: '',
  age: 13,
  race: 'fish',
};

const {context, scope} = createFormScope<typeof INITIAL_FORM_STATE>();

const DemoForm = scope(props => {
  const {Form, Field} = useForm(context);
  return (
    <Form onSubmit={value => console.log(value)}>
      <Field name="name" component={Input} validations={[required]} />
      <Field name="age" component={NumberInput} validations={[between(5, 20)]} />
      <Field name="race" component={Input} validations={[choice('fish', 'squid', 'octopus')]} /> 
      <button disabled={!props.touched || Object.values(props.errors).some(e => !!e.length)}>submit</button>
    </Form>
  );
});

export default function DemoApp() {
  return <DemoForm initialState={INITIAL_FORM_STATE} onSubmit={value => alert(`submit ${JSON.stringify(value)}`)} />; } 
```

让我解释几行重要的内容。(这些仍在改进过程中，因此可能会更改)

### 范围

```
const {context, scope} = createFormScope<typeof INITIAL_FORM_STATE>(); 
```

`createFormScope`是创建一个允许在表单和字段之间共享表单状态的范围。
一个组件被包含在`scope`中将会有两个额外的道具`initialState`和`onSubmit`。你可以看到`<DemoApp />`这些价值观正在发挥作用。

### 表格

在表单层组件中，如果需要，还可以在`<Form />`组件上处理`onSubmit`。

`<Field />`需要`name`和`component`，可选`validations`。

字段的`name`不只是打成`string`而是打成`keyof State`。因此，如果您有类似于`<Field name="rage" ...`的输入错误，TypeScript 编译器会发出类似“rage 不是‘key of type of INITIAL _ FORM _ STATE’的类型”的警告。

`component`也是严格打字。组件应该遵循名为`InputProtocol<State, Name>`的签名，其定义如下:

```
export type InputProtocol<S, Name extends keyof S, DelegatedProps> = Omit<InputHTMLAttributes<any>, 'name' | 'onChange' | 'value'> & {
  name: Name;
  value: S[Name];
  errors: ReadonlyArray<string>;
  touched: boolean;
  onChange(name: Name, value: S[Name]): void;
}; 
```

我已经为原生 DOM 组件编写了一些胶水。您可以从`mayoiga/lib/forms`导入这些内容。

你也可以为任何组件编写 glue，即使该组件是复杂的，返回非原始值，如数组或对象。

#### 验证

`<Field />`的`validations`是一个`Validator`的数组。`Validator`的定义如下。

```
type Validator<S, Name extends keyof S> = (target: S[Name], record: S) => undefined | string; 
```

Validator 可以获得目标的值以及所有其他记录。您可以将字符串作为错误消息返回。

## 最后

Mayoiga 正在开发中，目前还没有文档(因为我的英语水平 XD ),尽管任何问题/问题/请求都是受欢迎的。:)

然而，我认为任何讨厌用样板代码管理每周输入的人都会对这个概念感兴趣。

谢谢！