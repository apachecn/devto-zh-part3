# 有角度的 9|8|7 单选按钮示例

> 原文：<https://dev.to/singhdigamber/angular-9-8-7-radio-buttons-example-b6i>

在本教程中，我将回答如何使用**角度单选按钮**。众所周知，Angular 有两种形式，模板驱动形式和反应形式。我将介绍如何在 Angular 应用程序中集成模板驱动表单中的单选按钮和反应式表单。

我们将从基于`NgModel`和`NgForm`指令的模板驱动表单开始。而反应式表单借助 FormBuilder 和 FormControl 类来管理表单元素。

#### 目录

1.  在模板驱动的表单中使用单选按钮
2.  模板驱动的单选按钮验证
3.  具有角度反应形式的单选按钮
4.  反应式表单单选按钮验证
5.  以角度设置单选按钮

## 在 Angular 中使用模板驱动表单中的单选按钮

在使用模板驱动表单中的单选按钮之前，我们需要在 angular app 中激活`FormsModule`服务。此服务允许您在 Angular 中使用模板驱动的表单。转到`app.module.ts`文件并粘贴以下代码。

```
import {FormsModule} from '@angular/forms';

@NgModule({
  imports: [
    BrowserModule,
    FormsModule
  ]
}) 
```

#### 以角度模板驱动的形式实现单选按钮

我们将在角度模板中创建单选按钮。我们将使用`ngModel`指令，该指令与`NgForm`指令通信。

```
<!-- Form starts -->
<form #myForm="ngForm" (submit)="templateForm(myForm.value)" novalidate>

   <div class="custom-control custom-radio">
      <input id="male" type="radio" class="custom-control-input" value="male" name="gender" ngModel>
      <label class="custom-control-label" for="male">Male</label>
   </div>

   <div class="custom-control custom-radio">
      <input id="female" type="radio" class="custom-control-input" value="female" name="gender" ngModel>
      <label class="custom-control-label" for="female">Female</label>
   </div>

   <button type="submit" class="btn btn-danger btn-lg btn-block">Find out gender</button>
</form><!-- Form ends --> 
```

#### 获取角度组件类中的单选按钮值

[点击此处阅读更多信息](https://www.positronx.io/angular-7-radio-buttons-example/)