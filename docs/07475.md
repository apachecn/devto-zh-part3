# Angular 和 RxJS:如何真正避免订阅

> 原文：<https://dev.to/flyingdot/angular-and-rxjs-how-to-actually-avoid-subscriptions-2bj9>

我们都知道——我们不应该直接订阅 observables。相反，我们应该使用异步管道。这一切都很有意义，但之后我们离开舒适的阅读椅，再次坐在办公桌前，开始编码…和订阅。有些情况并不那么明显。

## 我们的例子

我们正在展示顾客名单。

```
export class CustomerComponent implements OnInit {
    customers: Observable<Customer[]>;

    constructor(private customerService: CustomerService) {
    }

    ngOnInit() {
        this.customers = this.customerService.getAll();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

并且在模板

```
<li *ngFor="let customer of customers | async">{{ customer.name }}</li> 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那很简单。没有订阅这里，耶！Angular 通过其异步管道为我们处理订阅。

好了，现在让我们继续。我们想在名单上增加新客户。因此，我们引入了一个小的表单组件，并在提交新条目时向 CustomerComponent 发出一个事件。我们保存新的条目，然后重新加载整个列表。应该没那么难。

```
onCustomerCreated(customer: Customer) {
    this.customerService.create(customer).subscribe(() => 
        this.customers = this.customerService.getAll());
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，一个订阅，但这似乎是合理的，因为我们想等到创建操作完成。但是，因为我们替换了`customers`上的引用，所以这不起作用。好了，每个问题都可以用一个主题固定:

```
export class CustomerComponent implements OnInit {
    customers = new BehaviorSubject<Customer[]>([]);

    constructor(private customerService: CustomerService) {
    }

    ngOnInit() {
        this.loadCustomers()
    }

    onCustomerCreated(customer: Customer) {
        this.customerService.create(customer)
            .subscribe(() => this.loadCustomers());
    }

    private loadCustomers() {
        this.customerService.getAll().subscribe(
            loadedCustomers => this.customers.next(loadedCustomers));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这似乎很好。但是——我们又订阅了 load 动作，感觉不对。这是错误的。但是有一点我们是对的，我们可以用一个主题来解决它:

```
export class CustomerComponent implements OnInit {
    customers: Observable<Customer[]>;
    private readonly refreshTrigger = new BehaviorSubject({});

    constructor(private customerService: CustomerService) {
    }

    ngOnInit(): void {
        this.customers = this.refreshTrigger.pipe(
            switchMap(() => this.customerService.getAll()));
    }

    onCustomerCreated(customer: Customer): void {
        this.customerService.create(customer)
            .subscribe(() => this.refreshTrigger.next({}));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们不用主题作为来源。相反，我们使用它作为加载客户数据的触发器。就像这样，如果触发了刷新，我们可以使用相同的可观察实例作为源，并在其中推送新数据。我们仍然可以依靠异步管道来管理我们的订阅。