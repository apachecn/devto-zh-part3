# 角度单位测试

> 原文：<https://dev.to/prestonjlamb/unit-testing-in-angular-56bn>

几个月前，我在 Angular 文章中写了一篇[单元测试介绍。在这篇文章中，我承诺我会写更多的单元测试，并且在我有了更多的经验之后，会写另一篇文章和更多的细节。所以，我来了！几个月后，我为一个内部角度库写了很多很多测试。该库混合了服务、管道、防护、组件和指令。有孤立测试、浅层测试和深层测试。我学到了很多，并且不得不在 Twitter](https://dev.to/blog/intro-to-testing-in-angular) 和 StackOverflow 上问了很多关于[单元测试的问题来弄清楚一切。对我来说幸运的是，我从社区得到了很多帮助，我真的很感谢所有帮助我的人！现在，希望这也能帮助其他人。我们将检查测试几个有角度的。我们走吧！](https://twitter.com/search?q=%40plambweb%20testing)

## [测试实用程序](#testing-utilities)

在 Angular 应用程序中，拥有充满函数的实用程序文件来操作整个应用程序中的数据并不少见。测试这些实用程序以确保它们是可预测的，并在每次使用时给我们正确的输出，这一点很重要。即使这些函数不是精确的*角度*，我们仍然可以用测试其他角度的方法来测试它们。让我们在我们的`array.util.ts`文件中使用下面的函数:

```
export function stringArrayContainsPartialStringMatch(arr: string[], strMatch: string) {
    return arr.filter(item => item.includes(strMatch)).length > 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数接受一个字符串数组和一个要匹配的字符串。如果数组中的任何项目包含变量`strMatch`的任何部分，则返回 true。需要注意的是，数组不需要包含整个字符串。下面是这个实用函数的一些测试:

```
it('should return true if the value is included in the string array', () => {
    const arr = ['value 1'];
    const str = 'value 1';
    const includedInArray = stringArrayContainsPartialStringMatch(arr, str);

    expect(includedInArray).toBe(true);
});

it('should return true if the value is included in the string array', () => {
    const arr = ['value 1'];
    const str = 'val';
    const includedInArray = stringArrayContainsPartialStringMatch(arr, str);

    expect(includedInArray).toBe(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以做这些测试的逆测试，以确保当值不在数组中时，函数返回 false。出于一致性的考虑，这些测试可以放在一个`*.spec.ts`文件中，就像 Angular CLI 为指令、管道等创建的一样。

## [测试管道](#testing-pipes)

测试角管道可能是一个更好的起点，因为管道有相当简单的输入和输出。让我们以下面的管道为例:

```
export class StatusDisplayPipe implements PipeTransform {
    transform(id: number): string {
        if (typeof id !== 'number') {
            throw new Error('The ID passed in needs to be a number.');
        }

        switch (id) {
            case 2:
                return 'Sent';
            case 3:
                return 'Delivered';
            default:
                return 'Pending';
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

管道接收项目的状态 ID，并返回项目的状态是挂起、已发送还是已交付。测试这个不会涉及太多；这将是一个孤立的测试，我们可以这样测试它:

```
describe('StatusDisplayPipe', () => {
    it('should return Sent for an ID of 2', () => {
        const pipe = new StatusDisplayPipe();
        const statusDisplay = pipe.transform(2);

        expect(statusDisplay).toBe('Sent');
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们一行一行地分解它。我们首先使用关键字`new`创建管道的一个实例。接下来，我们调用管道上的`transform`方法，并将我们想要测试的 ID 传递给它。然后，我们使用`expect`语句来检查管道的返回值是否是我们所期望的。

我们可以通过检查其他条件来继续这个管道，比如 ID 为 1，或者当我们没有向管道传递一个数字时会发生什么。现在，由于使用了 TypeScript，并且因为我们键入了对`transform`方法的输入，如果我们没有为 ID 传入一个数字，我们可能会在 IDE 中看到一个错误，但是在我看来仍然值得测试它。

这几乎就是测试一个管道所需要的全部！不算太坏，对吧？

## [检测服务](#testing-services)

在许多情况下，测试服务与管道非常相似。我们将能够通过创建服务的实例，然后在其上调用方法，来单独测试它们。它可能比管道更复杂，例如，因为它可能包含一个`Subject`，但它仍然相当简单。有些服务确实有依赖关系，需要我们在创建服务实例时传递这些依赖关系。我们将研究这样的服务，因为它比没有任何依赖关系的服务更复杂。

重要的是要记住，我们不想测试这些依赖性；我们假设他们正在其他地方接受测试。这就是使用 Jasmine 来模拟这些服务派上用场的地方。以下面的服务为例:

```
export class ConfigurationService {
    constructor(private _http: HttpClient) {}

    loadConfiguration() {
        return this._http
            .get('https://my-test-config-url.com')
            .toPromise()
            .then((configData: any) => {
                this.configData = configData;
            })
            .catch((err: any) => {
                this.internalConfigData = null;
            });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 该函数用于在应用程序使用`APP_INITIALIZER`令牌启动之前加载 Angular 应用程序的配置。这就是为什么有关于可观测的`.toPromise()`方法。

那么，我们如何测试呢？我们将首先创建一个模拟的`HttpClient`应用程序，然后创建一个服务实例。我们可以使用被模仿的`HttpClient`应用程序返回我们想要的数据，或者抛出一个错误，或者我们可能需要测试的任何东西。另一件要记住的事情是，服务中的许多功能是异步的。因此，我们需要使用来自`@angular/core/testing`的`fakeAsync`和`tick`方法，或者只使用来自同一个库中的`async`方法。我们来看一些例子:

```
describe('ConfigurationService', () => {
    const mockConfigObject = { apiUrl: 'https://apiurl.com' };
    let mockHttpService;
    let configurationService;

    beforeEach(() => {
        mockHttpService = jasmine.createSpyObj(['get']);
        configurationService = new ConfigurationService(mockHttpService);
    });

    it('should load a configuration object when the loadConfiguration method is called', () => {
        mockHttpService.get.and.returnValue(of(mockConfigObject));
        configurationService.loadConfiguration();
        tick();

        expect(Object.keys(configurationService.configData).length).toBe(Object.keys(mockConfigObject).length);
    });

    it('should handle the error when the loadConfiguration method is called and an error occurs', () => {
        mockHttpService.get.and.returnValue(throwError(new Error('test error')));
        configurationService.loadConfiguration();
        tick();

        expect(configurationService.configData).toBe(null);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们稍微分解一下这些测试。在`describe`的顶部，我们为服务设置了一些“全局”变量。`mockHttpService`和`configurationService`在`beforeEach`方法中初始化。我们使用`jasmine.createSpyObj`来创建一个模拟的`HttpClient`实例。我们告诉它我们将模拟`get`方法。如果我们需要来自`HttpClient`的其他函数，我们会将它们添加到那个数组中。

在两个单元测试的每一个中，当调用`get`方法时，我们告诉`mockHttpService`返回什么。在第一个例子中，我们告诉它返回我们的`mockConfigObject`作为一个可观测值。在第二个例子中，我们使用来自 RxJS 的`throwError`。同样在这两者中，我们称之为`loadConfiguration`方法。然后，我们检查服务的内部`configData`变量是否被设置为我们期望的值。

现在，我们的应用程序的真正服务可能做许多其他事情，比如有一个方法返回那个`configData`对象，或者对象的一个属性，或者任何数量的其他函数。所有这些都可以通过与上述功能相同的方式进行测试。如果服务需要更多的依赖项，您可以像我们创建`HttpClient`依赖项一样创建它们。

> 在为图书馆的一项服务编写测试时，我学到了一些东西，它来自 Joe Eames。默认情况下，CLI 使用导入和设置的`TestBed`创建`*.spec.ts`文件。但是很多时候你不需要那个。正如 Joe 所说，Angular 中的所有这些东西都只是类，你可以创建它们的实例。很多时候这就足够了，而且比使用`TestBed`更简单。我学到的是，当你需要的时候，你会知道你什么时候需要这个`TestBed`；在那之前，就像我们在这里做的那样。

## [检测指令](#testing-directives)

我们要测试的下一个角度元素是一个指令。在这个例子中，测试变得更加复杂。不过不用担心，刚开始只是势不可挡。有人向我演示了这一点，然后我能够在其他指令和组件上使用该示例。希望这能成为你前进的榜样。

我们在这里使用的指令将文本输入转换为提前输入，在指定的去抖时间后输出新值。指令如下:

```
export class TypeaheadInputDirective implements AfterContentInit {
    @Input() debounceTime: number = 300;
    @Output() valueChanged: EventEmitter<string> = new EventEmitter<string>();

    constructor(private searchInput: ElementRef) {}

    ngAfterContentInit() {
        this.setupTypeaheadObservable();
    }

    setUpTypeaheadObservable() {
        fromEvent(this.searchInput.nativeElement, 'keyup')
            .pipe(
                debounceTime(this.debounceTime),
                distinctUntilChanged(),
                tap(() => this.valueChanged.emit(this.searchInput.nativeElement.value)),
            )
            .subscribe();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

测试这个指令的目的是当某些东西被输入到`input`元素中时，该值被发出。所以让我们来看看测试是什么样的。这个会不一样；要测试在`input`中输入一个值意味着创建一个包含`input`元素和指令的`TestHostComponent`。我们将创建一个输入事件，然后检查是否输出了值。

```
@Component({
    selector: 'app-test-host',
    template: `
        <input typeaheadInput [debounceTime]="debounceTime" (valueChanged)="valueChanged($event)" type="text" />
    `,
})
class TestHostComponent {
    @ViewChild(TypeaheadInputDirective) typeaheadInputDirective: TypeaheadInputDirective;
    public debounceTime: number = 300;
    valueChanged(newValue: string) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是`TestHostComponent`。我们可以通过`@ViewChild`装饰器访问指令。然后我们使用`debounceTime`输入来控制它，以防我们想要测试当我们改变它时会发生什么。最后，我们有一个`valueChanged`函数来处理指令的输出。我们将在测试中使用 spy on 函数。现在对该指令进行一次实际测试:

```
describe('TypeaheadInputDirective', () => {
    let component: TestHostComponent;
    let fixture: ComponentFixture<TestHostComponent>;

    beforeEach(async(() => {
        TestBed.configureTestingModule({
            declarations: [TestHostComponent, TypeaheadInputDirective],
        }).compileComponents();
    }));

    beforeEach(() => {
        fixture = TestBed.createComponent(TestHostComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });

    it('should emit value after keyup and debounce time', fakeAsync(() => {
        spyOn(component, 'valueChanged');

        const input = fixture.debugElement.query(By.css('input'));
        input.nativeElement.value = 'Q';
        input.nativeElement.dispatchEvent(
            new KeyboardEvent('keyup', { bubbles: true, cancelable: true, key: 'Q', shiftKey: true }),
        );

        tick(component.debounceTime);

        expect(component.valueChanged).toHaveBeenCalledWith('Q');
    }));
}); 
```

Enter fullscreen mode Exit fullscreen mode

在两个`beforeEach`函数中，我们使用`TestBed`来创建测试夹具并访问组件。然后，在测试中，我们在我们的`valueChanged`函数上添加了`spyOn`。然后我们找到`input`元素并将值设置为‘Q’，然后分派`KeyboardEvent`。我们使用`tick`等待`debounceTime`通过，然后我们检查`valueChanged`函数是否已经用字符串`Q`调用。

> 正如我前面说过的，测试这个指令比其他测试更复杂。但是一旦我们知道了发生了什么，事情就不会太糟了。对于更复杂的组件和指令，我们可以在许多其他测试中使用相同的方法。但是请记住:我们应该从编写最简单的测试开始。这将使维护和编写测试变得更容易，也更有可能让我们继续编写它们。

## [检测组件](#testing-components)

我们要测试的下一个角度项目是一个组件。这将非常类似于我们刚刚测试的指令。但是，尽管它看起来几乎完全一样，我认为测试组件是值得的。

该组件的目的是显示我们希望向用户显示的警报列表。有一个相关的服务可以添加和删除警报，并使用`Subject`传递它们。这有点复杂，因为我们将使用一个`TemplateRef`来传入模板，这个模板是`ngFor`循环应该用于警告的。这样，实现应用程序就可以确定警报应该是什么样子。组件如下:

```
@Component({
    selector: 'alerts-display',
    template: '<ng-template ngFor let-alert [ngForOf]="alerts$ | async" [ngForTemplate]="alertTemplate"></ng-template>',
    styleUrls: ['./alerts-display.component.scss'],
})
export class AlertsDisplayComponent implements OnInit {
    public alerts$: Subject<Alert[]>;
    @ContentChild(TemplateRef)
    alertTemplate: TemplateRef<NgForOfContext<Alert>>;

    constructor(private _alertToaster: AlertToasterService) {}

    ngOnInit() {
        this.alerts$ = this._alertToaster.alerts$;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是组件的全部内容。我们想要测试的是，当`Subject`发出一个新值时，模板会更新并显示许多项。我们将能够在测试中模拟这一切。让我们在这次测试中再来看看我们的`TestHostComponent`:

```
@Component({
    selector: 'app-test-host',
    template: `
        <alerts-display>
            <ng-template let-alert>
                <p>{{ alert.message }}</p>
            </ng-template>
        </alerts-display>
    `,
})
class TestHostComponent {
    @ViewChild(AlertsDisplayComponent) alertsDisplayComponent: AlertsDisplayComponent;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个`TestHostComponent`中，我们将`<alerts-display>`组件放入模板中，并为`ngFor`循环提供模板。现在我们来看看测试本身:

```
describe('AlertsDisplayComponent', () => {
    let component: TestHostComponent;
    let fixture: ComponentFixture<TestHostComponent>;
    let mockAlertsToasterService: AlertToasterService;

    beforeEach(async(() => {
        mockAlertsToasterService = jasmine.createSpyObj(['toString']);
        mockAlertsToasterService.alerts$ = new Subject<Alert[]>();
        TestBed.configureTestingModule({
            declarations: [AlertsDisplayComponent, TestHostComponent],
            providers: [{ provide: AlertToasterService, useValue: mockAlertsToasterService }],
        }).compileComponents();
    }));

    beforeEach(() => {
        fixture = TestBed.createComponent(TestHostComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });

    it('should show an element for each item in the array list', fakeAsync(() => {
        mockAlertsToasterService.alerts$.next([{ message: 'test message', level: 'success' }]);

        tick();
        fixture.detectChanges();

        const pTags = fixture.debugElement.queryAll(By.css('p'));

        expect(pTags.length).toBe(1);
    }));
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分析一下这里的情况。我们将模拟`AlertToasterService`并访问`beforeEach`函数中的夹具和组件。然后在测试中，我们发出一组新的警报。这是在调用了`addAlert`函数后服务中将会发生的事情。然后所有订阅了`Subject`的地方都会得到新的列表并输出结果。我们放入一个`tick`来确保任何必要的时间已经过去，然后(这很重要)我们告诉`fixture`到`detectChanges`。我花了一段时间才记住这一部分，但如果你忘记了，模板就不会更新。之后，我们可以查询`fixture`来找到所有的`p`标签。现在，因为我们发出了一个只有一个警告项的数组，我们预计只有一个`p`标签可见。

同样，这比某些组件可能要复杂一些。也许在某些组件上，我们不想测试模板中的输出是什么。我们只想测试组件的一些功能。在这种情况下，只需像这样创建组件:

```
const component = new MyComponent(); 
```

Enter fullscreen mode Exit fullscreen mode

如果需要，我们仍然可以模拟服务，并将它们传递给构造函数，但这应该是我们的目标。但是，当您的测试需要更复杂的测试设置时，不要害怕。一开始看起来很吓人，但是做几次之后，你就会找到窍门了。

## [测试守卫](#testing-guards)

我考虑过是否应该包括这一部分，因为警卫本质上是专门的服务，但我认为如果我要花所有的时间来规划如何测试我们 Angular 应用程序的所有不同部分，我还不如专门包括这一部分。所以让我们来看看一个守卫。就是这里:

```
@Injectable()
export class AuthenticationGuard implements CanActivate {
    constructor(private _authenticationService: AuthenticationService) {}

    canActivate(next: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<boolean> {
        const preventIfAuthorized: boolean = next.data['preventIfAuthorized'] as boolean;
        const redirectUrl: string = state.url && state.url !== '/' ? state.url : null;
        return preventIfAuthorized
            ? this._authenticationService.allowIfAuthorized(redirectUrl)
            : this._authenticationService.allowIfNotAuthorized(redirectUrl);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们只测试一个函数:函数`canActivate`。我们需要为这些测试模拟`AuthenticationService`和`ActivatedRouteSnapshot`和`RouterStateSnapshots`。让我们来看看这个后卫的测试:

```
describe('AuthenticationGuard', () => {
    let authenticationGuard: AuthenticationGuard;
    let mockAuthenticationService;
    let mockNext: Partial<ActivatedRouteSnapshot> = {
        data: {
            preventIfAuthorized: true,
        },
    };
    let mockState: Partial<RouterStateSnapshot> = {
        url: '/home',
    };

    beforeEach(() => {
        mockAuthenticationService = jasmine.createSpyObj(['allowIfAuthorized', 'allowIfNotAuthorized']);
        authenticationGuard = new AuthenticationGuard(mockAuthenticationService);
    });

    describe('Prevent Authorized Users To Routes', () => {
        beforeEach(() => {
            mockNext.data.preventIfAuthorized = true;
        });

        it('should return true to allow an authorized person to the route', async(() => {
            mockAuthenticationService.allowIfAuthorized.and.returnValue(of(true));
            authenticationGuard
                .canActivate(<ActivatedRouteSnapshot>mockNext, <RouterStateSnapshot>mockState)
                .subscribe((allow: boolean) => {
                    expect(allow).toBe(true);
                });
        }));

        it('should return false to not allow an authorized person to the route', async(() => {
            mockAuthenticationService.allowIfAuthorized.and.returnValue(of(false));
            authenticationGuard
                .canActivate(<ActivatedRouteSnapshot>mockNext, <RouterStateSnapshot>mockState)
                .subscribe((allow: boolean) => {
                    expect(allow).toBe(false);
                });
        }));
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们有一些我们将使用的模拟数据，比如用于`RouterStateSnapshot`等的。我们创建了模仿的`AuthenticationService`并创建了`AuthenticationGuard`的一个实例。然后，当`allowIfAuthorized`返回 true 和 false 时，我们测试`canActivate`函数。我们调用`canActivate`函数，订阅该值，然后检查该值以确保它是我们所期望的。为了运行这些测试，因为它们是异步的，我们不能忘记从`@angular/core/testing`导入并使用`async`。

## 结论

我希望如果你已经走了这么远，你已经学到了一些新的东西。我知道在过去的几周里，当我写这些测试的时候。我花了很长时间才开始为 Angular 编写单元测试，因为我感到不知所措。我不知道从哪里开始，不知道测试什么，也不知道如何编写测试。但是我绝对会说，我对我的 Angular 库的测试比我对任何其他应用程序的感觉都要自信得多。当我做出改变时，我马上就知道它是否破坏了什么。有那种程度的自信感觉真好。希望这篇文章能成为很多人的好参考。我知道这对我会是一个很好的参考！