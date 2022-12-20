# 修改 Ant 设计默认主题的简单方法

> 原文：<https://dev.to/agmm/simple-way-of-modifying-ant-design-default-theme-3g4h>

*自我提醒*

从安装 lessc 开始。

```
npm install less -g 
```

Enter fullscreen mode Exit fullscreen mode

移动到 antd 包中的“dist”目录。

创建一个包含以下内容的文件，并将其命名为' my-theme.less'

```
@import "./antd.less";   // Import Ant Design styles by less entry

@primary-color: #d228e9;                         // primary color for all components
@link-color: #1890ff;                            // link color
@success-color: #52c41a;                         // success state color
@warning-color: #faad14;                         // warning state color
@error-color: #f5222d;                           // error state color
@font-size-base: 40px;                           // major text font size
@heading-color: rgba(0, 0, 0, .85);              // heading text color
@text-color: rgba(0, 0, 0, .65);                 // major text color
@text-color-secondary : rgba(0, 0, 0, .45);      // secondary text color
@disabled-color : rgba(0, 0, 0, .25);            // disable state color
@border-radius-base: 4px;                        // major border radius
@border-color-base: #d9d9d9;                     // major border color
@box-shadow-base: 0 2px 8px rgba(0, 0, 0, .15);  // major shadow for layers 
```

Enter fullscreen mode Exit fullscreen mode

在终端中执行以下命令:

```
lessc --js my-theme.less result.css 
```

Enter fullscreen mode Exit fullscreen mode

在项目样式文件夹中编译它的例子:

```
lessc --js my-theme.less ../../../src/style/custom-antd.css 
```

Enter fullscreen mode Exit fullscreen mode

现在将生成的 CSS 导入到您的项目中。