# 弹出组件

> 原文：<https://dev.to/dlomedes/propping-components-k0o>

最重要的是使用 props 将数据从父组件传递到子组件。还可以定制组件的交互/行为方式或外观/风格。

可以使用 props.children 调用组件标签中的内容，这用于将任何内容从文本放置到 jsx 甚至另一个组件，这使它成为另一个文件的子组件。

组件的三个部分
嵌套:comp 在另一个 comp
内创建一个新组件，但只导入一个 react 库，而不是渲染组件，使用 default 后跟组件名称导出它。

可重用性:多用途的灵活性。尽可能少/简单的组件
不要忘记在主 index.js 文件的顶部导入组件，并使用标签调用组件

配置:props 用于配置一个组件
使用 props 作为第一个自变量/参数？组件功能的。使用点符号像对象调用一样调用 prop。
prop 跟在组件名后面，像传递 style 或 className 一样，就好像它是 HTML/JSX 标签一样。

[https://thepractical dev . S3 . Amazon AWS . com/I/spgxnfggv 3 sh 8 xlfyrjt . png](https://thepracticaldev.s3.amazonaws.com/i/spgxnfggv3sh8xlfyrjt.png)

...justnotes