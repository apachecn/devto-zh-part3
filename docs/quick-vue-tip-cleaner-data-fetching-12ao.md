# 快速 Vue 提示:更干净的数据获取

> 原文：<https://dev.to/mccabiles/quick-vue-tip-cleaner-data-fetching-12ao>

# Quick Vue 提示:取数据

我们遇到的一个常见用例是需要使用 Vue 组件显示从 API 获取的数据。

例如，假设我们有一个名为`ViewAvenger.vue`的 Vue 组件，它根据 id 显示复仇者的信息。这样做的一种常见方式是向我们的组件传递一个`id` prop。实际的 API 调用由组件本身通过方法调用`getData`来处理。