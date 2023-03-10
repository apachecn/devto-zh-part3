# React + MobX 的轻量级替代

> 原文：<https://dev.to/pxyup/lightweight-replacement-of-react--mobx-22gd>

[FastDom](https://github.com/PxyUp/FastDom)

React + MobX 的轻量级替代(希望在未来 Angular/Vue 中)，不使用虚拟 DOM 对比，而是只对改变的部分进行重新渲染。放弃 HTML 模板，支持他们在 JS 中的解释，给我们树摇是组件/模板，工作速度提高，因为解析模板的时间是零。

[演示链接](https://thepracticaldev.s3.amazonaws.com/i/kedjptyl326dy2xwqycj.png)
[stack blitz](https://stackblitz.com/edit/typescript-wgjbzf)演示

## 特性

1.  大小- **4.1 kB** 或 **1.38 kB** gzipped。
2.  库只在必要时重写更改。
3.  性能- **保证 60 fps** 。
4.  导入的函数和类的名字没有最后和*讨论*。
5.  对于组件和模板来说有一场树摇！！！。