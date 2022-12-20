# 角度 9|8|7 带有反应式表单的选择下拉列表示例

> 原文：<https://dev.to/singhdigamber/angular-9-8-7-select-dropdown-examples-with-reactive-forms-5cb0>

今天我要和大家分享如何使用 **Angular 7 Select Dropdown** 。我们将学习如何使用反应式窗体的角度选择下拉菜单。在本教程中，我将向您展示如何在您的应用程序中创建、实现和验证 select dropdown。

## 了解 SelectControlValueAccessor

`SelectControlValueAccessor`是非常有用的指令，它用于写入选择控制值以及监听选择控制的变化。该指令使用以下值访问器: **FormControlDirective** 、 **FormControlName** 和 **NgModel** 指令。

#### 目录

1.  选择下拉菜单的角度反应表单设置
2.  在角度中创建选择下拉列表
3.  角度 7 多选下拉列表
4.  角度选择下拉列表验证
5.  角度选择选项更改事件
6.  角度 7 选择下拉列表完整示例

## 选择下拉菜单的角度反应形式设置

在`app.module.ts`文件中注入 ReactiveFormsModule，以便在 Angular 应用程序中使用选择下拉菜单。

```
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [
    ReactiveFormsModule
  ]
}) 
```

#### `app.component.html`

```
<!-- Form starts -->
<form [formGroup]="registrationForm" (ngSubmit)="onSubmit()">

   <select class="custom-select">
      <option value="" disabled>Choose your city</option>
      <option>New York</option>
   </select>

   <button type="submit">Submit</button>
</form><!-- Form ends --> 
```

#### `app.component.ts`

```
import { Component } from '@angular/core';
import { FormBuilder, Validators } from "@angular/forms";

@Component({
  // ...
})

export class AppComponent {

  constructor(public fb: FormBuilder) { }

  /*########### Form ###########*/
  registrationForm = this.fb.group({
    cityName: ['']
  })

  onSubmit() {
    alert(JSON.stringify(this.registrationForm.value))
  }

} 
```

[点击此处阅读更多信息](https://www.positronx.io/angular-9-select-dropdown-examples-with-reactive-forms/)