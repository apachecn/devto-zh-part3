# 选中 Jquery 复选框选择所有带有 jQuery 的复选框

> 原文：<https://dev.to/jackharner/select-all-checkboxes-with-jquery-31al>

[https://codepen.io/jackharner/embed/wZPeBw?height=600&default-tab=result&embed-version=2](https://codepen.io/jackharner/embed/wZPeBw?height=600&default-tab=result&embed-version=2)

一个快速的 jQuery 代码片段，用于选择复选框列表中的所有复选框。

```
$("#selectAll").click(function(){
        $("input[type=checkbox]").prop('checked', $(this).prop('checked'));

}); 
```

Enter fullscreen mode Exit fullscreen mode

基本上，单击全选复选框，将所有复选框的`checked`属性设置为全选复选框的`checked`属性的值。这样，您可以选择或取消选择所有。