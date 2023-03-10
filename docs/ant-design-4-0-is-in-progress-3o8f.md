# 蚂蚁设计 4.0 正在进行中！

> 原文：<https://dev.to/zombiej/ant-design-4-0-is-in-progress-3o8f>

## 重述

距离蚂蚁设计 3.0 发布的【2017 年 12 月已经过去了 16 个月。在 4289 次提交、138 次发布、7675 次发布中，我们修复了大量的 bug 并增加了许多新功能( [changelog](https://ant.design/changelog) ),这给 GitHub 带来了 25375 颗星。我们还发布了[蚂蚁设计专业版 4.0](https://medium.com/ant-design/ant-design-pro-v4-is-here-6f23098ae9d9) 。支持布局的打字稿、块和抽象。我们要感谢每一位贡献者，你们的贡献让蚂蚁设计越来越好。

同时，我们发布了蚂蚁设计 Pro 4.0。支持 TypeScript、Block 并提供布局组件。

此时此刻，我们在想:好吧，接下来呢？我们能做些什么让蚂蚁设计走得更远？**是时候为蚂蚁设计 4.0 做规划了**！🍻

以下是关于 4.0 的详细计划。我们可能会调整其中的一些，因为它仍在规划中。

### 🌓关于兼容性

我们将在 4.0 中删除不推荐使用的属性，这意味着不再支持不推荐使用的属性。如果你没有从最新的 3.x 得到任何警告，它会无缝升级。否则，4.0 发布后，3.0 将有半年的维护期。

我们知道做升级很麻烦。我们计划提供兼容的包来处理这个问题:

```
import Compatible from '@ant-design/compatible';

// It works, but will warning in console
const Demo = () => (
  <Compatible>
    <YourApp />
  </Compatible>
); 
```

该软件包将一直可用到 3.x 维护阶段结束。

#### 使用最新的 React API

我们长期以来一直提供 React 15 支持。但从社会反馈来看似乎没有必要(React 15 有近 0%的问题)，因为 React 有很强的兼容性。为了支持 React 15，我们非常谨慎地使用新的 API。在 4.0 之后，这将不再是一个问题:

*   为相关组件提供钩子 api
*   支持并发模式(仍在准备中，将在 4.0 中继续调整)
*   拥抱 React 17(哇！~)

#### 停止 IE9/10 支持

蚂蚁设计 3.0 花大力气[支持老 IE](https://github.com/ant-design/ant-design/issues?utf8=%E2%9C%93&q=is%3Aclosed+IE) 。但是根据行业统计，随着 Windows 的升级，IE9/10 的使用率越来越低。我们将在 4.0 中停止支持 IE 9/10(但仍支持 IE 11)，这意味着更新的浏览器功能支持将成为可能。

#### 其他兼容更新

*   从 2.x 版本升级到 3.x 版本
*   图标调整
*   提及已弃用

### 📦减少尺寸

#### 优化图标尺寸

我们用 svg 图标([为什么用 svg？](https://github.com/ant-design/ant-design/issues/10353))后`antd@3.9.0`。我们使用字符串名称来映射图标，不能按需加载。我们将所有的图标文件导入 antd，这样[会使包的大小变大](https://github.com/ant-design/ant-design/issues/12011)。我们将在 4.0 中处理这个问题。

旧图标使用方式将被**弃用**:

```
import { Icon, Button } from 'antd';

const Demo = () => (
  <div>
    <Icon type="smile" />
    <Button icon="smile" />
  </div>
); 
```

将在 4.0 中使用导入单个图标来代替:

```
// Directly import
import SmileOutline from 'antd/icons/SmileOutline';

// If tree-shaking supported
import { SmileOutline } from 'antd/icons';

const Demo = () => (
  <div>
    <SmileOutline />
    <Button icon={<SmileOutline />} />
  </div>
); 
```

你仍然可以用老方法使用兼容方法的方法。

#### 删除 Draft.js

我们在提及组件中使用 Draft.js 来确认弹出位置，但是只使用了其中的一小部分功能。这似乎超出了成本。我们计划在 4.0 中删除 Draft.js，并使用其他轻量级解决方案。同时，为了与 3.0 中的 origin reference 组件相区别，将引入一个新的组件 references 来避免 api 冲突。此外，它支持[兼容方式](https://yuque.antfin-inc.com/jilin.jjl/newhire/fndgiy/edit#e8233958) :

```
// Follow Code will not work
import { Mention } from 'antd';

const Demo = () => (
  <Mention />
); 
```

```
// Added `Mentions` in 4.0
import { Mentions } from 'antd';

const Demo = () => (
  <Mentions />
); 
```

### 🧭性能优化

对于来自社区的大型数据集，我们很少遇到性能问题。我们将确保在这里进行一些优化。

#### 虚拟滚动

虚拟滚动是进行性能优化的一种常见方式。我们计划在组件中支持这一点。在 4.0 版本中可能不会立即完成。会一步步融合。

#### 重构动画

我们过去做过一些动画黑客。它在大多数情况下工作良好。我们计划使用更多的纯反应方式，而不是黑客方式。本次更新将在沉默中进行，没有什么会影响到你。

### 🧩组件优化

我们在 3.0 中增加了许多组件，并将在 4.0 中继续。这些组件将来自业务场景、Ant Design Pro 和社会需求。新的组件流程将与 Ant Design 3.0 相同，我们将把相关的设计文件放在 PR 中，并在官方网站上收集。在每个次要版本中发布。

此外，我们计划重构一些关键组件，使其更易于使用。包括但不限于:

#### 表格

表单是最常用的组件之一。我们注意到社会上对 API 设计有很多评论。我们希望在 4.0 中简化 API:

*   表单将包括数据存储。再也不用`Form.create()`了。
*   Fom。项目将包括字段绑定。再也不用`getFieldDecorator`了。
*   形式。项将保留该值，但是当字段被移除时，验证器将被禁用。

```
const Demo = () => {
  const [form] = Form.useForm();

  const onFinish = () => {
    // Do something...
  };

  useEffect(() => {
    // Something like ajax call
    form.setFieldsValue({
      username: 'light',
    });
  }, []);

  return (
    <Form form={form} onFinish={onFinish}>
      <Form.Item name="username" rules={[{ required: true }]}>
        <Input />
      </Form.Item>
    </Form>
  );
} 
```

在现实世界中，我们会遇到很多表单之间的通信(通常是在细节配置中)。我们想让它变得更简单:

```
const { useForm, createContext } = Form;
const FormContext = createContext();

const Demo = () => {
  return (
    <FormContext>
      <YourForm1 />
      <YourForm2 />
    </FormContext>
  );
}; 
```

如果你想跟踪表单进度，你可以[看这里](https://github.com/react-component/form/pull/292)。

#### 表

过去，我们收到了大量的[表](https://github.com/ant-design/ant-design/issues?utf8=%E2%9C%93&q=is%3Aissue+table)反馈。我们知道扩展和滚动道具不能很好地一起工作。这一次，我们将集中精力解决这个问题。此外，我们将在表优化上做很多工作。简单的布局方式:

```
const Demo = () => {
  return (
    <Table
      header={{
        templateAreas: `
          name    address     address
          name    building    no
        `,
        cells: [
          { key: 'name', title: 'Name' },
          { key: 'address', title: 'Address' },
          { key: 'building', title: 'Building' },
          { key: 'no', title: 'No' },
        ],
      }}
    />
  );
}; 
```

我们还计划添加[摘要页脚](https://github.com/ant-design/ant-design/issues/5710)来支持摘要行。

#### 新日期选择器

当前日期选择器满足大多数要求。但是从[社区讨论](https://github.com/ant-design/ant-design/issues?utf8=%E2%9C%93&q=is%3Aissue+datepicker)中，我们可以挖掘的更多。我们将提供休眠期采摘者和护林采摘者([讨论](https://github.com/ant-design/ant-design/issues/4524#issuecomment-480576884))。此外，我们将更新日期选择器的设计风格，以增强用户体验。

### 🚀继续更新

除了以上内容，我们还计划继续更新。这些将在 4.0 中不断更新，以增强开发和用户体验。

#### 🧶提高可达性

Ant Design 3.0 对可访问性的支持有限，我们计划调整 dom 结构并添加更多 aria mark 以增强屏幕阅读器体验。此外，我们还准备优化键盘交互。

#### 🎯开发者 API 标准

我们发现一些 API 的命名风格与其他的没有什么不同。对于 Typescript 开发人员来说这不是问题，但是如果不这样就很难记住。因此，我们将创建一个命名标准文档，其中包括当前的 API 和相关的命名规则。在新功能中，将遵循命名规则以避免 API 命名冲突。此外，我们欢迎公众对此发表意见。

#### 💼反应严格模式

如果你试图用`<React.StrictMode>`包装 antd 组件，你会得到警告。我们已经用新的生命周期方法替换了一些组件。这项工作将在 4.0 中继续。

#### 💡改善开发人员体验

过去，我们发现有些问题反复出现。这些问题在某种程度上是用法问题。我们认为我们可以在这方面做些事情(实际上已经从 3.0 开始了)。在开发环境中，我们会警告意外状态(如对象失效、前缀/词缀调整导致 dom 结构改变等)。).我们认为，当遇到问题时，控制台是第一个需要关注的地方。提供适当的提示有助于发现问题。同时，我们会将其他问题汇总到每个组件文档的 FAQ 中。从维护的角度来看，我们不能帮助解决每个使用问题，但是它们确实存在，特别是对于新来的开发者。FAQ 可能有助于节省大量搜索时间。如果您对此感兴趣，欢迎与我们一起帮助增强开发者体验。

#### 🐱设计资源

蚂蚁设计不仅仅是一个组件库。设计是支撑力量。我们将同步更新设计资源(草图组件包、厨房工具、设计令牌等)。).我们还将调整当前的组件风格，以增强用户体验。

### 里程碑

这是我们的里程碑计划。我们将在 Github 上创建相关问题。也欢迎社会贡献者:

#### Q2

*   将相关 API 标记为已弃用，并从文档中删除。
*   低级组件警告更新。

#### Q3

*   创建 antd 4.0 分支并更新文档。
*   组件开发。

#### Q4

*   发布蚂蚁设计 4.0。

## 欢迎登机

在开发过程中，我们会随时通知您。以上内容可能不是最终版本。非常欢迎来自社区的想法/建议！让我们把蚂蚁设计 4.0 做得更好！