# 角反应形式|基础

> 原文：<https://dev.to/gugadev/angular-reactive-forms--fundamentos-41o2>

active Forms 是一个模块，它为我们提供了一个角度，使我们能够以不变和反应灵敏的方式定义表单。通过此模块，我们可以在表单中构建控件，并将它们与模板的 HTML 标签相关联，而无需显式使用“`ngModel`”。与 Angular Forms 不同，“重新激活 Forms”使用 observables 和 Streams 来跟踪窗体数据，从而使我们能够通过使用 xJs 的运算符拦截和转换窗体数据。

要开始使用活性表单，我们需要导入并包括模块`ReactiveFormsModule` :

```
@NgModule({
  declarations: [
    SignupComponent
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
    InputModule,
    ButtonModule
  ],
  exports: [
    SignupComponent
  ]
})
export class SignupModule { } 
```

Enter fullscreen mode Exit fullscreen mode

## 反应形式的解剖学

一旦我们导入了模块，就可以在我们的组件和模板中使用它。我们使用活性表单的表单结构如下所示。

```
<form [formGroup]="myForm" (onSubmit)="doSomething()">
  <input formControlName="email" />
  <input type="password" formControlName="password" />
  <button type="submit">Registrarme</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

我们看到它有一些有趣的属性。第一个是`formGroup`。此属性表示:“此表单将由控制器内的`suForm`元素控制。

下面的属性是`formControlName`，我们用它来表示此控件将与我们在控制器中定义的字段相关联。

现在让我们看看控制器:

```
@Component({
  selector: 'app-myform',
  templateUrl: './myform.component.html',
  styleUrl: './myform.component.scss'
})
export class MyFormComponent implements OnInit {
  myForm: FormGroup

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.myForm = this.fb.group({
      email: new FormControl(''),
      password: new FormControl('')
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这个。如果你盯着看，我们在“`this.fb.group({ ... })`”内定义的每个元素都与我们在模板输入的“`formControlName`”属性中传递的值具有相同的名称。这是因为**我们正在将 HTML 元素与我们正在创建的 FormControl 对象**相关联，这样，我们就可以**通过该对象在输入中设置并获取值**。

## 处理数值

通过“`FormControl`”我们可以以编程和反应方式获取和设置 HTML 中控件的值。让我们看一个例子。

为了获得控制权的价值，我们只需获得控制权并使用`value` :

```
const email = this.myForm.get('email').value 
```

Enter fullscreen mode Exit fullscreen mode

并且为了建立数据，我们采用了`setValue`法:

```
this.myForm.get('email').setValue('ejemplo@hola.com') 
```

Enter fullscreen mode Exit fullscreen mode

## 字段验证

表单构建中最重要的一点是验证。正确验证我们的字段可保护我们免受恶意输入的侵扰，并提供更好的用户体验。验证反应性字段非常简单。让我们回到定义我们的领域。

```
this.myForm = this.fb.group({
  email: new FormControl(''),
  password: new FormControl('')
}) 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们要进行验证的地方。默认情况下，Angular 为大多数情况提供了验证。你可以在这里看到验证清单。让我们看看如何添加它们:

```
this.myForm = this.fb.group({
  email: new FormControl('', [
    // validaciones síncronas
    Validators.required,
    Validators.email
  ], [
    // validaciones asíncronas
  ]),
  password: new FormControl('')
}) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，要向控件添加验证，只需将第二个参数`FormControl`传递给构造函数，即验证函数的数组。这是我们必须添加验证的地方。

但是，还有另一种验证，称为异步验证，如其名称所示，既可以返回承诺，也可以返回可观察到的。

## 异步和个性化验证

此类验证只能返回异步结构，如承诺或可观察。让我们看一下自定义的异步验证:t0]

```
validateEmailNotTaken(ctrl: AbstractControl) {
  return (
    this
     .checkForExists(ctrl.value)
     .pipe(map(taken => taken ? { taken: true } : null))
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，这是相当简单的。在此示例中，我们验证您输入的电子邮件当前未被其他人使用。这是通过`HttpClient`方法完成的，该方法使用`HttpClient`返回带有布尔型的观测值，以确定是否存在。如果存在，我们返回一个拥有属性`taken` **的对象，可以从模板**访问该对象；如果不存在，我们只返回 null，表示没有错误。

在内部，此验证是按角度进行解析的，得到包含观测值的平面值。简单吗？

## 有用的性质

`FormGroup`和`FormControl`类有许多有用的性质。例如，`FormGroup`的属性之一是`valid`，它是一个布尔值，根据控件是否通过验证来告诉您表单是否有效。`FormControl`类具有诸如`dirty`等属性，用于了解控件是否已包含以前的值(删除后)、`touched`用于确定控件是否已被“触摸”(失去焦点后)以及`errors`等属性，用于返回带有验证错误的对象(对象键将是的名称

让我们看看如何在我们的模板中使用这些属性:

```
<form [formGroup]="myForm" (onSubmit)="doSomething()">
  <input formControlName="email" [ngClass]="{ error: email.dirty && email.errors }" />
  <span *ngIf="email.errors && email.errors.email">Ingrese un email válido</span>
  <span *ngIf="email.errors && email.errors.taken">El email ya ya sido registrado</span>
  <input type="password" formControlName="password" [ngClass]="{ error: password.dirty && password.errors }" />
  <span *ngIf="email.errors && email.required">Ingrese su contraseña</span>
  <button type="submit" [disabled]="myForm.invalid">Registrarme</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们的表格现在实时向我们显示我们的错误，另外，只要表格中有错误，“提交”按钮就会被禁用。让我们更详细地看看我们在这里做了什么。

在这一行中，我们说:"如果输入中已经有输入值，并且包含任何错误，请将" error "类添加到输入中"。

```
<input formControlName="email" [ngClass]="{ error: email.dirty && email.errors }" /> 
```

Enter fullscreen mode Exit fullscreen mode

在这另一行，我们说:“如果电子邮件已经注册，就显示这个范围”:“T0”

```
<span *ngIf="email.errors && email.errors.taken">El email ya ya sido registrado</span> 
```

Enter fullscreen mode Exit fullscreen mode

∞这样我们就有了一个经过验证的表格，并且有良好的用户体验！

* * *

## 结论

众所周知，验证表格非常重要，尤其是当我们面对具有技术技能的受众时。此外，请记住验证必须在客户端和服务器上进行。在这方面，Angular 帮助我们通过同步和异步验证来验证第一种类型，使我们能够使用安全、复杂和可用的表单。😉