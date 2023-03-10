# 角度测试简介

> 原文：<https://dev.to/prestonjlamb/intro-to-testing-in-angular-4o3m>

我最近一直在看乔·埃姆斯写的关于角度测试的复数视觉课程。我在这里实话实说:我从 2015 年末/2016 年初就开始用 Angular 了。我已经从 Angular 2 beta 到 Angular v7.0 了，我写了 0 个测试。哎哟...

但我认为我们总是可以改进的，所以这是我今年重点改进的地方。我希望说，到今年年底，我是一个经验丰富的角度测试者，不再忽视开发的这个关键部分。

Joe 向我们介绍了你可以为你的 Angular 应用编写的 3 种类型的测试:隔离测试、浅层集成测试和深层集成测试。让我们回顾一下其中的每一项，并展示一个示例。

## 孤立测试

一个独立的测试就像它听起来的那样:它测试一段与代码库的其他部分完全隔离的代码。举个例子，假设你有一个服务:

```
@Injectable()
export class ToasterService() {
    addMessage(msg: string) {
        this.messages.push({ msg })
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可能想要编写一个测试来确认`addMessage`函数工作正常。你不想确保它是从一个组件中被正确调用的，或者类似的事情；您只想确保在调用它时，返回正确的信息或者正确处理提供的参数。

我们可以这样写一个独立的测试:

```
import { ToasterService } from './toaster.service';

describe('ToasterService', () => {
    let service: ToasterService;

    it('should have no messages to start', () => {
        service = new ToasterService();
        expect(service.messages.length).toBe(0);
    });

    it('should add a message when add is called', () => {
        service = new ToasterService();

        service.add('message 1');

        expect(service.messages.length).toBe(1);
    });

    it('should remove all messages when clear is called', () => {
        service = new ToasterService();
        service.add('message 1');

        service.clear();

        expect(service.messages.length).toBe(0);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们查看消息是否像它们应该的那样被添加到数组中，和/或被删除，或者我们可能需要执行的任何其他操作。

## 浅显的试探

下一种测试是浅测试。这种类型的测试，至少部分地，将在组件级别测试功能。它不会测试子组件或服务。它仅限于单一组件中的实际内容。为此，我们需要使用模拟服务来伪造返回信息。

下面是一个浅层测试的完整例子:

```
describe('ToastersComponent (Shallow)', () => {
    let fixture: ComponentFixture;
    let mockToasterService;
    let MESSAGES;

    @Component({
        selector: 'app-toast',
        template: '',
    })
    class MockToastComponent {
        @Input() hero: Hero;
    }

    beforeEach(() => {
        MESSAGES = [
            { msg: 'Warning Message', level: 'warning' },
            { msg: 'Error Message', level: 'error' },
            { msg: 'Success Message', level: 'success' },
        ];
        mockToasterService = jasmine.createSpyObj(['getMessages', 'addMessage', 'deleteMessage']);

        TestBed.configureTestingModule({
            declarations: [ToastersComponent, MockToastComponent],
            providers: [{ provide: ToasterService, useValue: mockToasterService }],
        });
        fixture = TestBed.createComponent(ToastersComponent);
    });

    it('should correctly set the messages property from the service', () => {
        mockToasterService.getMessages.and.returnValue(of(MESSAGES));
        fixture.detectChanges();

        expect(fixture.componentInstance.messages.length).toBe(3);
    });

    it('should create one li for each toast', () => {
        mockToasterService.getMessages.and.returnValue(of(MESSAGES));
        fixture.detectChanges();

        expect(fixture.debugElement.queryAll(By.css('li')).length).toBe(3);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

作为这里发生的事情的简要概述，在每次测试之前，我们重置消息数组，准备测试模块和要使用的测试组件。然后我们测试消息是否从服务器返回，组件是否在屏幕上列出它们。

## 深度测试

本文的第三个也是最后一个是深度测试。该测试将测试组件的功能，并确保其子组件也能正确显示信息。

这里有一个例子:

```
describe('ToastersComponent (Deep)', () => {
    let fixture: ComponentFixture;
    let mockToasterService;
    let MESSAGES;

    beforeEach(() => {
        MESSAGES = [
            { id: 1, name: 'SpiderDude', strength: 8 },
            { id: 2, name: 'Wonderful Woman', strength: 24 },
            { id: 3, name: 'SuperDude', strength: 55 },
        ];
        mockToasterService = jasmine.createSpyObj(['getMessages', 'addMessage', 'deleteMessage']);

        TestBed.configureTestingModule({
            declarations: [ToastersComponent, ToastComponent],
            providers: [{ provide: ToasterService, useValue: mockToasterService }],
            schemas: [NO_ERRORS_SCHEMA],
        });
        fixture = TestBed.createComponent(ToastersComponent);
    });

    it('should render each toast as a toast component', () => {
        mockToasterService.getMessages.and.returnValue(of(MESSAGES));

        fixture.detectChanges();

        const toastComponentDEs = fixture.debugElement.queryAll(By.directive(ToastComponent));

        expect(toastComponentDEs.length).toBe(3);
        for (let i = 0; i < toastComponentDEs.length; i++) {
            expect(toastComponentDEs[i].componentInstance.toast).toEqual(MESSAGES[i]);
        }
    });

    it(`should call ToasterService.deleteMessage when the ToastComponent's delete button is clicked`, () => {
        spyOn(fixture.componentInstance, 'delete');
        mockToasterService.getMessages.and.returnValue(of(MESSAGES));

        fixture.detectChanges();

        const toastComponents = fixture.debugElement.queryAll(By.directive(ToastComponent));
        // Click the actual HTML button
        // toastComponents[0].query(By.css('button')).triggerEventHandler('click', { stopPropagation: () => {} });
        // raise the event manually
        // (toastComponents[0].componentInstance).delete.emit(undefined);
        // trigger the event using the debugElement
        toastComponents[0].triggerEventHandler('delete', null);

        expect(fixture.componentInstance.delete).toHaveBeenCalledWith(MESSAGES[0]);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这与浅层测试非常相似，除了我们深入到子组件并测试它们。我们可以测试实际点击按钮，或以编程方式触发事件，然后我们可以看到发生了什么。简而言之，我们可以测试组件的全部功能，以确保在进行更改时，应用程序不会中断。

## 结论

这三种类型的测试将涵盖您的角度应用的所有方面。您可能不需要对应用程序的每个部分都进行这三种类型的测试，但是这将为您提供在需要时进行测试的工具。

我很高兴将这些测试添加到我的应用程序中。对组件和服务进行测试所带来的安全和安心，将会使编写测试所花费的额外时间变得非常值得。

如果你有什么建议或窍门，我很乐意听听！在实际实现之后，我将写一篇后续文章，来谈谈 pit falls 和我在这个过程中学到的东西！