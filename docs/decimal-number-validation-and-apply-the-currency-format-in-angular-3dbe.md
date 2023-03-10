# 十进制数验证并应用货币格式

> 原文：<https://dev.to/ajayojha/decimal-number-validation-and-apply-the-currency-format-in-angular-3dbe>

我们将讨论如何验证十进制数，以及如何根据应用程序的区域设置格式化该数。

我们通常为数字验证创建一个自定义验证器，并创建自定义指令来格式化数字(120，875，256.25)。

但是在这里，我们没有创建任何定制的验证器/指令来满足需求。

我们使用 RxwebValidators 来验证十进制值以及格式化数字。
RxwebValidator 可通过[**@ rx web/reactive-form-validators**](https://npmjs.com/package/@rxweb/reactive-form-validators)包获得，这提供了很多验证器来满足应用需求，但我们只讨论数值验证器。

我们必须安装[**@ rx web/reactive-form-validators**](https://npmjs.com/package/@rxweb/reactive-form-validators)的软件包，以利用 RxwebValidators 的服务。

> npm 安装@ rx web/反应式表单验证器

一旦安装完毕，我们必须在根模块中注册“RxReactiveFormsModule”。

就这么办吧。

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

现在，我们必须按照标准方法创建 FormGroup，并在 amount FormControl 上应用数值验证器。

```
 this.userInfoFormGroup = this.formBuilder.group({
            amount:['', RxwebValidators.numeric()], 
        }); 
```

按照上面的代码，我们在 amount FormControl 上应用了数值验证器。这将验证数值，如果有人输入数值以外的值，那么 FormControl 将无效。

但是等等，我们需要验证十进制值并格式化数字。为此，我们必须在数值验证器中传递两个参数。

```
 this.userInfoFormGroup = this.formBuilder.group({
            amount:['', RxwebValidators.numeric({allowDecimal:true,isFormat:true})], 
        }); 
```

allowDecimal 属性允许用户输入十进制值，isFormat 用于根据应用程序区域设置格式化数值。一旦用户移动到另一个控件，将应用该格式。

下面是该组件的完整代码。

```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder } from "@angular/forms"
import { RxwebValidators } from '@rxweb/reactive-form-validators';

@Component({
    selector: 'app-numeric-add-validator',
    templateUrl: './numeric-add.component.html'
})
export class NumericAddValidatorComponent implements OnInit {
    userInfoFormGroup: FormGroup

    constructor(
        private formBuilder: FormBuilder )
    { }

    ngOnInit() {
        this.userInfoFormGroup = this.formBuilder.group({
            amount:['', RxwebValidators.numeric({allowDecimal:true,isFormat:true})], 
        });
    }
} 
```

全部完成！。

可以参考数值验证器的 [stackblitz 例子。](https://stackblitz.com/edit/rxweb-numeric-validation-with-decimal-value?file=src%2Fapp%2Fnumeric-add.component.ts)

根据角度反应形式的标准实践绑定 HTML。

如果您有任何问题/建议，请在下面分享您的意见。