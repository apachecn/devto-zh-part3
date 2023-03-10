# 用 RxwebValidator 比较 Angular 中的密码

> 原文：<https://dev.to/ajayojha/compare-password-in-angular-with-rxwebvalidator-552n>

这里，我们讨论如何将密码与 RxwebValidators 进行比较。

# 为什么是 RxwebValidator？

使用标准方法也可以比较密码。因为 angular 提供了管理跨字段验证的功能。但是我看到了 Angular 标准方法的一些设计问题。如果我参考[角度文档进行跨场验证](https://angular.io/guide/form-validation#cross-field-validation)。Angular 文档建议我必须将自定义验证器放在 FormGroup 级别，以便比较两个字段或其他一些操作。这种方法不太适合大型/复杂的表单。因为自定义验证程序会调用每个 FormControl 值更改、模糊等事件。

让我们看看下面的截图，自定义验证器也调用非验证字段，我从 Angular Doc 复制的代码。

[![](img/7ff4b1d8b76955c3f96f9a547cddc5ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D7hGwt_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6j99xge0pmhotijlls8d.gif)

根据上面的截图，我在“power”FormControl 中输入值，这个 Form control 与表单中的验证没有关系。但是，自定义验证程序在 FormControl 中输入值时仍在调用。

现在，我们谈论 RxwebValidator 来克服这个问题。

首先我们要安装包[**@ rx web/reactive-form-validators**](https://www.npmjs.com/package/@rxweb/reactive-form-validators)

> npm 安装@ rx web/反应式表单验证器

一旦安装完毕，我们必须在根模块中注册“RxReactiveFormsModule”。

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule,ReactiveFormsModule } from '@angular/forms';
import { AppComponent } from './app.component';

import {  RxReactiveFormsModule } from "@rxweb/reactive-form-validators"
@NgModule({
  imports:      [ BrowserModule, FormsModule,ReactiveFormsModule,RxReactiveFormsModule ],
  declarations: [ AppComponent],
  bootstrap:    [ AppComponent ]
})
export class AppModule { } 
```

现在让我们在 FormGroup 中创建两个 FormControls，并使用 RxwebValidator 的比较验证器。这两个表单控件是“password”和“confirmPassword”。

功能性要求是，如果我更改任何一个 FormControl 的值，验证应该会触发。

我们对“confirm password”form control 应用比较验证器。

```
this.userForm = this.formBuilder.group({
  'password': '',
  'confirmPassword':['',RxwebValidators.compare({fieldName:'password'})]
  }); 
```

因为我们已经在比较验证器中提供了“password”form control 的字段名，以便将该值与“password”form control 进行比较。

请参见下面的操作:

可以参考 [*stackblitz 比较密码*](https://stackblitz.com/edit/rxwebvalidator-compare-password?file=src/app/app.component.ts) 的例子。

全部完成！。

如果您有任何问题/建议，请在下面分享您的意见。