# 短角尖端:#1 反应式调试

> 原文：<https://dev.to/uberkeeg/short-angular-tips-1-reactive-form-debugging-2m6g>

在 angular 中调试一个反应式表单时，我发现使用下面的
在视图上输出表单很有用

```
<pre>Form values: {{ form?.value | json }}</pre> 
```

pre 标记保持 JSON 格式良好。