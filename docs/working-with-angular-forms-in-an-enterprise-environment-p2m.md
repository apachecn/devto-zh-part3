# 在企业环境中使用角形

> 原文：<https://dev.to/angular/working-with-angular-forms-in-an-enterprise-environment-p2m>

在我们公司，我们一开始就在棱角分明的外形上苦苦挣扎。这是因为我们首先一头扎进去，没有谈论我们将如何在整个项目中使用它。这篇文章展示了我们目前如何使用角形来提高效率。

## 有点背景

任何过去参与过 caliber 项目的人都知道，这些应用程序很可能包含大量复杂的表单。我们也不例外，我们在医疗领域努力使客户管理更容易。我们每天都会接触到表单，简单的表单和更复杂的表单。

作为一个开始新项目的新团队，我们同意使用[反应式表单](https://angular.io/guide/reactive-forms)，除此之外，我们还没有就表单和表单验证达成一致。在几次冲刺之后，我们开始注意到我们写了很多(相同的)代码，都像 HTML 一样有棱角。与此同时，我们收到了一些设计反馈，并注意到我们不得不接触许多代码来使一切正确。这就是我们开始认为应该有更好的方式来处理表单的地方。

## 输入表单字段

我们开始编写输入表单字段，其中包含负责字段行为的所有编排代码。这些字段的第一次迭代包括将表单控件和表单组作为输入传递给这些控件。虽然这在开始时有效，但并不是很好。我们总是被提醒将表单组传递到表单域，因为这不是默认的“角度方式”。对于一些控件，我们最终在表单域组件中使用了一个内部表单，它必须与主组件保持同步，随之而来的是所有的问题和讨厌的代码。

经过一些迭代，我们了解了[控制值存取器](https://angular.io/api/forms/ControlValueAccessor) s，这与 [NgControl](https://angular.io/api/forms/NgControl) 一起打开了可能性。从 Angular 文档中我们可以看到一个 CVA 有以下 API:

```
interface ControlValueAccessor {
  /**
  * Writes a new value to the element.
  *
  * This method is called by the forms API to write to the view when programmatic changes from model to view are requested.
  */
  writeValue(obj: any): void

  /**
  * Registers a callback function that is called when the control's value changes in the UI.
  *
  * This method is called by the forms API on initialization to update the form model when values propagate from the view to the model.
  * When implementing the `registerOnChange` method in your own value accessor, save the given function so your class calls it at the appropriate time.
  */
  registerOnChange(fn: any): void

  /**
  * Registers a callback function is called by the forms API on initialization to update the form model on blur.
  *
  * When implementing `registerOnTouched` in your own value accessor, save the given function so your class calls it when the control should be considered blurred or "touched".
  */
  registerOnTouched(fn: any): void

  /**
  * Function that is called by the forms API when the control status changes to or from 'DISABLED'. Depending on the status, it enables or disables the appropriate DOM element.
  */
  setDisabledState(isDisabled: boolean)?: void
} 
```

Enter fullscreen mode Exit fullscreen mode

这种组合允许我们像以前一样使用自定义表单字段，但其中包含更多功能。代码看起来也干净了很多。为开发人员和我们的用户考虑一个标准化的行为和可视化，例如表单验证，以及将标签绑定到正确的输入字段。对于每种类型的控件，我们都创建了自己的实现，并以一个抽象类`BaseFormField`结束，其中包含我们在每个表单字段中需要的通用代码。

```
export abstract class BaseFormField implements ControlValueAccessor, DoCheck {
  @Input() label: string
  @Input() ariaLabel: string
  // giving the possibility to override the default error messages
  @Input() errorMessages: { [key: string]: string } = {}

  @Output() change = new EventEmitter<any>()

  // generate a unique id for each control
  id = generateControlId()

  value: any
  text: string
  disabled = false
  required = false

  onChange = (_value: any) => {}
  onTouched = () => {}

  constructor(@Optional() @Self() public controlDir: NgControl) {
    // bind the CVA to our control
    controlDir.valueAccessor = this
  }

  ngDoCheck() {
    if (this.controlDir.control instanceof FormControl) {
      // check if this field is required or not to display a 'required label'
      const validator =
        this.controlDir.control.validator &&
        this.controlDir.control.validator(new FormControl(''))
      this.required =
        Boolean(validator && validator.hasOwnProperty('required')) ||
        Boolean(validator && validator.hasOwnProperty('selectedCount'))
    }
  }

  get hasErrors() {
    return (
      this.controlDir.control &&
      this.controlDir.control.touched &&
      this.controlDir.control.errors
    )
  }

  // implementation of `ControlValueAccessor`
  writeValue(value: any): void {
    this.value = value
    if (typeof value === 'string') {
      this.text = value
    }

    this.onChange(this.value)
    this.change.emit(this.value)
  }

  registerOnChange(fn: any): void {
    this.onChange = fn
  }

  registerOnTouched(fn: () => void): void {
    this.onTouched = fn
  }

  setDisabledState(disabled: boolean): void {
    this.disabled = disabled
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们还使用这些表单域组件来实现跨表单域的通用行为:

*   我们将标签绑定到正确的表单域，我们通过为每个表单域生成一个惟一的 id 来实现这一点
*   当表单域是可选的时，我们将其附加到表单域的标签上
*   我们以通用的方式显示验证消息，在需要时可以选择覆盖默认的验证消息
*   当一个表单被禁用时，我们禁用表单域

复选框列表的实现如下:

```
@Component({
  selector: 'checkbox-list',
  template: `
    <div class="form-part" [class.field-error]="hasErrors">
      <label *ngIf="label"
        >{{ label }}<small *ngIf="!required"> (Optional)</small></label
      >
      <div class="checkbox-list" [ngClass]="alignment">
        <div class="checkbox-placeholder" *ngFor="let item of items">
          <mat-checkbox
            [checked]="isChecked(item.value)"
            (change)="change($event, item.value)"
            (blur)="onTouched()"
            [disabled]="disabled"
            >{{ item.label }}
          </mat-checkbox>
        </div>
      </div>
      <error-message-container
        [control]="controlDir.control"
        [errorMessages]="errorMessages"
      >
      </error-message-container>
    </div>
  `,
})
export class CheckboxListComponent extends BaseListField {
  @Input() items: Item[]
  @Input() alignment: 'horizontal' | 'vertical' = 'horizontal'

  isChecked(value: any) {
    return (
      this.controlDir.control &&
      this.controlDir.control.value &&
      this.controlDir.control.value.includes(value)
    )
  }

  change(event: MatCheckboxChange, value: any) {
    if (event.checked) {
      this.writeValue((this.value || []).concat(value))
    } else {
      this.writeValue(this.value.filter((v: any) => v !== value))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

复选框列表字段组件可以像普通输入字段一样使用:

```
<checkbox-list
  formControlName="allergies"
  label="Allergies"
  alignment="horizontal"
  [items]="allergies"
></checkbox-list> 
```

Enter fullscreen mode Exit fullscreen mode

## 表单指令

通过遵循上面的实践，它允许我们使用自定义指令来扩展这些控件。例如，如果我们想要填充一个单选列表或一个选择框，我们可以简单地为我们的项目赋值。

```
@Directive({
  selector: 'radio-list[relation-list]',
})
export class RadioRelationDirective {
  constructor(private host: RadioListComponent) {
    this.host.items = [
      { label: 'Partner', value: Relation.Partner },
      { label: 'Child', value: Relation.Child },
      { label: 'Parent', value: Relation.Parent },
    ]
  }
}

@Directive({
  selector: 'select-field[relation-list]',
})
export class SelectRelationDirective {
  constructor(private host: SelectFieldComponent) {
    this.host.items = [
      { label: 'Partner', value: Relation.Partner },
      { label: 'Child', value: Relation.Child },
      { label: 'Parent', value: Relation.Parent },
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 超充控制值存取器

CVA 允许我们创建通用的可重用组件，想象一个普通人的组件要求个人信息。在我们了解 CVA 氏症之前，我们多次实施了这些控制，包括所有的缺点。每当一个新的标签出现来添加一个新的字段，调整验证，或者改变一个表单字段的行为，我们就会忘记在另一个位置更新一个表单。通过使用 CVA，这是可以避免的。它允许我们定义表单模板，并定义带有内置验证的表单组。这没什么特别的，因为这也可以用默认组件来完成。不同之处在于父组件，在父组件中，我们可以通过在表单组中定义 CVA 来将其用作普通的表单字段。换句话说，我们现在可以创建一个表单的一部分，并把它作为一个普通的表单域来使用。例如，如果我们采用一个非常简单的表单来询问这个人的姓和名，那么实现如下:

```
@Component({
  selector: 'person-simple',
  template: `
    <div [formGroup]="form" class="form">
      <form-field
        formControlName="firstName"
        label="First name"
      ></new-form-field>
      <form-field
        formControlName="name"
        label="Name"
      ></new-form-field>
    </div>
  `,
  providers: [
    {
      provide: NG_VALIDATORS,
      useExisting: forwardRef(() => PersonSimpleComponent),
      multi: true,
    },
    {
      provide: NG_VALUE_ACCESSOR,
      useExisting: forwardRef(() => PersonSimpleComponent),
      multi: true,
    },
  ],
})
export class PersonSimpleComponent
  implements OnDestroy, ControlValueAccessor, Validator {
  destroy = new Subject()
  form = this.fb.group({
    name: [null, [Validators.required, Validators.minLength(2)]],
    firstName: [null, [Validators.required, Validators.minLength(2)]],
  })
  onTouched = () => {}

  constructor(private fb: FormBuilder) {}

  ngOnDestroy() {
    this.destroy.next()
    this.destroy.complete()
  }

  writeValue(value: any) {
    if (value) {
      this.form.setValue(value, { emitEvent: false })
    }
  }

  registerOnChange(fn: any) {
    this.form.valueChanges.pipe(takeUntil(this.destroy)).subscribe(fn)
  }

  registerOnTouched(fn: any) {
    this.onTouched = fn
  }

  setDisabledState(disabled: boolean) {
    disabled ? this.form.disable() : this.form.enable()
  }

  validate(control: AbstractControl): ValidationErrors | null {
    if (control.touched) {
      ValidationUtils.touchAllFormElements(this.form)
    }

    if (this.form.valid) {
      return null
    }

    return {
      'person-error': {
        value: this.form.value,
      },
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们在父表单中使用这个组件::

```
<person-simple formControlName="person1"></person-simple>
<person-simple formControlName="person2"></person-simple> 
```

Enter fullscreen mode Exit fullscreen mode

这两个人在父表单组中被定义为表单控件:

```
form = this.fb.group({
  person1: [null],
  person2: [null],
}) 
```

Enter fullscreen mode Exit fullscreen mode

产生了下面的表单值:

```
{  "person1":  {  "name":  "Sarah",  "firstName":  "Smith"  },  "person2":  {  "name":  "John",  "firstName":  "Smith"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 表单验证

在前面的代码片段中已经可以看到关于验证的一瞥。在这里，我们再次感受到了每次必须实现表单验证时编写和维护相同代码的痛苦。这就是为什么我们创建了一个错误容器，它唯一的职责就是显示错误消息。

```
@Component({
  selector: 'error-message-container',
  template: `
    <div
      class="error-message"
      [style.visibility]="control.touched ? 'visible' : 'hidden'"
    >
      {{ control.errors | humanizeMessages: errorMessages }}
    </div>
  `,
})
export class ErrorMessageContainerComponent {
  @Input() control: FormControl
  @Input() errorMessages?: { [key: string]: string }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还有一个`humanizeFormMessages`管道来将错误映射到一条人类友好的消息。我们注入包含默认消息的`FormMessages`。如果默认行为没有例外，企业环境就不是企业环境，这就是为什么我们可以用特定于案例的消息覆盖默认消息。

```
@Pipe({ name: 'humanizeFormMessages' })
export class HumanizeFormMessagesPipe implements PipeTransform {
  constructor(@Inject(FormMessages) private messages) {}

  transform(
    validationErrors: ValidationErrors,
    overriddenMessages: { [key: string]: string }
  ) {
    if (!validationErrors) {
      return ''
    }

    // Allow the possibility to override messages
    const messages = {
      ...this.messages,
      ...overriddenMessages,
    }

    const messageKey = Object.keys(validationErrors)[0]
    const getMessage = messages[messageKey]
    const message = getMessage
      ? getMessage(validationErrors[messageKey])
      : 'Invalid field'
    return message
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用`FormGroupDirective`创造奇才

为了让大的向导表单更容易管理，我们把它们分成了多个小步骤。对于向导中的每一步，我们都创建它自己的独立形式。向导窗体是由所有这些小窗体缝合在一起组成的。这提高了可维护性和测试能力。通过这种松散耦合，对表单进行一些修改变得更加容易，您可以选择在不同的屏幕中重复使用步骤表单，例如在向导中使用表单，以及将表单作为独立的表单使用。

为了实现这一点，使用 [FormGroupDirective](https://angular.io/api/forms/FormGroupDirective) 作为 [ControlContainer](https://angular.io/api/forms/ControlContainer) ，并通过 [viewProviders](https://angular.io/api/core/Component#viewproviders) 提供它们(而不是通过`providers`)。我们现在可以在组件内部注入`FormGroupDirective`并将子表单附加到其父表单，在我们的例子中这是向导。

```
@Component({
  selector: 'child-form',
  templateUrl: './child-form.component.html',
  styleUrls: ['./child-form.component.scss'],
  viewProviders: [
    { provide: ControlContainer, useExisting: FormGroupDirective },
  ],
})
export class ChildFormComponent implements OnInit {
  form = this.fb.group({
    firstName: [null, [Validators.required]],
    lastName: [null, [Validators.required]],
  })

  constructor(
    private parentForm: FormGroupDirective,
    private fb: FormBuilder
  ) {}

  ngOnInit() {
    this.parentForm.form.addControl('person', this.form)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 检测表格

为了测试我们的表单，我们使用了[@ angular-extensions/testing-library](https://github.com/angular-extensions/testing-library)，这是一个围绕 [dom-testing-library](https://github.com/kentcdodds/dom-testing-library) 的 Angular 包装器。这使得我们无法测试实现细节，也无法按照用户使用表单的方式测试表单。

我们通过使用表单标签导航到表单字段，通过单击提交按钮提交表单。我们不关心表单组件的方法，我们关心的是用户看到了什么。

```
test('login form submits using the component syntax', async () => {
  const fakeUser = { username: 'jackiechan', password: 'hiya! 🥋' }
  const login = {
    emit: jest.fn(),
  }

  const { getByLabelText, getByText, input } = await createComponent(
    {
      component: LoginFormComponent,
      parameters: {
        login,
      },
    },
    {
      declarations: [LoginFormComponent],
      imports: [ReactiveFormsModule],
    }
  )

  input(getByLabelText('Username'), {
    target: {
      value: '',
    },
  })

  // If the text is not found the test will fail
  getByText('Username is required')

  input(getByLabelText('Username'), {
    target: {
      value: fakeUser.username,
    },
  })

  input(getByLabelText('Password'), {
    target: {
      value: fakeUser.password,
    },
  })

  submit(getByText('Create new account'))

  expect(login.emit).toHaveBeenCalledTimes(1)
  expect(login.emit).toHaveBeenCalledWith(fakeUser)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 帮助我们解决这个问题的一些资源

*   [Angular Forms–Angular connect 2017](https://www.youtube.com/watch?v=CD_t3m2WMM8&)作者[卡拉·埃里克森](https://twitter.com/karaforthewin)
*   [在以角度形式实现 ControlValueAccessor 时再也不要被弄糊涂了](https://blog.angularindepth.com/never-again-be-confused-when-implementing-controlvalueaccessor-in-angular-forms-93b9eee9ee83)作者 [Max Koretskyi](https://twitter.com/maxkoretskyi)
*   [通过](https://netbasal.com/make-your-angular-forms-error-messages-magically-appear-1e32350b7fa5) [Netanel Basal](https://twitter.com/NetanelBasal) 让你的角形错误信息神奇的出现
*   [Angular:嵌套模板驱动表单](https://medium.com/@a.yurich.zuev/angular-nested-template-driven-form-4a3de2042475)作者[阿列克谢·祖耶夫](https://twitter.com/yurzui)
*   [释放力量💪形式与角度的反应形式](https://blog.angularindepth.com/unleash-the-power-of-forms-with-angulars-reactive-forms-d6be5918f408)作者[西达尔特·阿杰拉](https://twitter.com/SiddAjmera)
*   [潜入无功形相](https://blog.angularindepth.com/dive-into-reactive-forms-cfc9adbb4467)由[无功狐](https://twitter.com/thekiba_io)
*   [控制值存取器——就像空间中的虫洞，对你的形式来说，只是更有用而已](https://www.youtube.com/watch?v=kVbLSN0AW-Y)作者[詹妮弗·瓦德拉](https://twitter.com/likeOMGitsFEDAY)