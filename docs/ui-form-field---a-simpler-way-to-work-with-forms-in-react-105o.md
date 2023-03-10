# ui-form-field——React 中使用表单的一种更简单的方式

> 原文：<https://dev.to/ngduc/ui-form-field---a-simpler-way-to-work-with-forms-in-react-105o>

是我在 React 中解决复杂表单渲染的尝试。它在许多项目中被积极使用，得到了工程师的积极反馈。

特点:

*   渲染不同的表单布局:Bootstrap 4，语义 UI，Spectre 等等。(包括水平布局、内嵌字段)
*   与 formik 兼容。这是建立在 formik 之上的，可以和它一起使用(用于定制字段等。)
*   支持流行的和先进的字段类型:反应选择，多选，自动完成标签输入等。
*   简化复杂的用例，如:动态(条件)字段呈现，与表单中的其他组件混合，自定义字段。
*   在手机屏幕上运行良好。

```
import { FormContainer, Form, Field, Button } from 'ui-form-field';

<FormContainer onSubmit={this.onSubmit} render={props => (
  <Form use="bootstrap4">
    <Field label="Text" name="text" />
    <Field name="password" />
    <Field textarea name="textarea" />
    <Field select options={animals} name="select" />
    <Field radios options={genders} name="radio" />
    <Field checkboxes options={roles} name="checkboxes" />
    <Field checkbox name="singleCheckbox" />
    <Field singleSelect options={animals} name="singleSelect" />
    <Field multiSelect options={animals} name="multiSelect" />
    <Field tagSelect name="tags" options={tags} />
    <Field number name="number" />
    <Field date name="date" />
    <Field time name="time" />
    <Field toggle inline name="toggle" />
    <Field file label="File Upload" name="file1" />
    <Field file withPreview label="File Upload (with Preview)" name="file2" />
    <Field range name="range" />

    <Button type="submit"/>
    <Button>Cancel</Button>
  </Form>
)} /> 
```

结果:(完整的表单，带验证(带是)和错误消息)

[![Example](img/581b63091dacade04465e336c163e70c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_VS7zUfY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/ngduc/ui-form-field/raw/master/screenshot-types.png)

[Demo](https://codesandbox.io/s/on50k50wy)
[Github](https://github.com/ngduc/ui-form-field)

我感谢下面的任何反馈、建议或想法。谢谢！:)