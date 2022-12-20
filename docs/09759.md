# 角反应形式的文件验证

> 原文：<https://dev.to/ajayojha/file-validation-in-angular-reactive-form-4e4g>

在角度反应式表单中，文件验证是一项有点笨拙的任务。在这里，我们讨论如何优化代码，实现在将文件传递给服务器之前验证文件的目标。

让我们了解一下功能需求:

*   该表格应只接受 jpeg 和 gif 文件，文件的分辨率约为 100×100 像素。
*   File 对象应作为值存储在 FormControl 中。

让我们深入代码来满足需求。我们使用 RxwebValidator 的扩展和文件验证器。

由于 RxwebValidator 不是 Angular framework 的一部分，所以我们必须安装[**@ rxweb/reactive-form-validators**](https://npmjs.com/package/@rxweb/reactive-form-validators)包来使用 RxwebValidator 的服务。

> npm 安装@ rx web/反应式表单验证器

现在，我们必须在根模块中注册“RxReactiveFormsModule”。

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

让我们为 FormControl 创建一个 profile，并在其上设置图像和扩展验证器。

```
 this.userInfoFormGroup = this.formBuilder.group({
            profilePhoto:['', [ 
                                 RxwebValidators.image({maxHeight:100,maxWidth:100 }),
                                 RxwebValidators.extension({extensions:["jpeg","gif"]})
                                 ]       
                            ], 
        }); 
```

根据上述代码，我们使用图像验证器来限制用户上传图像的高度和宽度不超过 100 像素，允许的扩展名为“jpeg”和“gif”。

下面是组件的完整代码:

```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder } from "@angular/forms"
import { RxwebValidators } from '@rxweb/reactive-form-validators';

@Component({
    selector: 'app-image-add-validator',
    templateUrl: './image-add.component.html'
})
export class ImageAddValidatorComponent implements OnInit {
    userInfoFormGroup: FormGroup

    constructor(
        private formBuilder: FormBuilder )
    { }

    ngOnInit() {
        this.userInfoFormGroup = this.formBuilder.group({
            profilePhoto:['', [ 
                                 RxwebValidators.image({maxHeight:100,maxWidth:100 }),
                                 RxwebValidators.extension({extensions:["jpeg","gif"]})
                                 ]       
                            ], 
        });
    }
} 
```

正如我们也希望 file 对象应该作为一个值存储在 FormControl 中。为此，我们必须在输入文件控件上写属性( **[writeFile]="true"** )。
参见下面的 HTML 代码:

```
<div>
  <form  [formGroup]="userInfoFormGroup">
    <div class="form-group">
      <label>Profile Photo</label>
      <input [writeFile]="true" type="file" formControlName="profilePhoto" class="form-control"  />
    </div>

    <button [disabled]="!userInfoFormGroup.valid" class="btn btn-primary">Submit</button>
  </form>
</div> 
```

全部完成！。

请参考 [**的 stackblitz 例子**](https://stackblitz.com/edit/rxweb-image-and-extension-validator?file=src%2Fapp%2Fimage-add.component.ts) 。

正如我们在文件验证中所讨论的，每个应用程序都有一个场景，如下所述:

## 如何限制用户访问某些文件？

在这种情况下，您必须使用文件验证器并指定最少或/和最多的文件。这里是文件验证器的代码。

```
 this.userInfoFormGroup = this.formBuilder.group({
            files:['', RxwebValidators.file({minFiles:2, maxFiles:5 })], 
        }); 
```

请参考 [**文件验证器**](https://stackblitz.com/edit/angular-vvnwk4?file=src/app/file-add.component.ts) 的工作示例。

如果您有任何问题/建议，请在下面分享您的意见。