# 创建了 React/Redux 组件生成器

> 原文：<https://dev.to/glekner/created-a-reactredux-component-generator---------9cp>

这对我很有帮助，所以我想我也可以分享一下。生成器使用域驱动的文件结构和预先编写的模板创建连接的 React 组件。所有的模板都可以替换成你自己的！感谢任何反馈！

```
$ tree
.
├── Component.fixtures.js
├── Component.js
├── ComponentActions.js
├── ComponentConstants.js
├── ComponentHelper.js
├── ComponentReducer.js
├── ComponentSelectors.js
├── __tests__
│   ├── Component.test.js
│   ├── ComponentActions.test.js
│   ├── ComponentIntegration.test.js
│   ├── ComponentReducer.test.js
│   └── ComponentSelectors.test.js
├── component.scss
└── index.js 
```

Enter fullscreen mode Exit fullscreen mode

[https://github.com/glekner/generator-react-domain](https://github.com/glekner/generator-react-domain)