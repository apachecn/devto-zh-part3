# Angular 入门的主要 TypeScript 和 RxJS 概念

> 原文：<https://dev.to/angular/key-typescript-and-rxjs-concepts-for-getting-started-with-angular-38ah>

所以你刚刚接到一个 Angular 项目，并试图找出如何加快 Angular 和 TypeScript 和 RxJS 的速度。深呼吸，会没事的！这里有一个快速速成课程，介绍您需要了解的重要 TypeScript 和 RxJS 概念，以便尽快入门:

## 打字稿

Angular 是用 TypeScript 编写的，当使用它的类和接口时，您需要对 TypeScript 概念有一个基本的理解。

### 公有与私有修饰符

**它们是什么:**一种修改访问成员的方式和方法。默认情况下，未指定的是公共的。

**关注原因:**将成员和方法标记为私有会使其他类无法访问它们。

**值得注意:**在类构造函数中将成员标记为 public 是一种常见的模式，您将看到这是在类中设置该成员的简写。

```
class Person {
  constructor(public name: string) {
  }
}

//same as
class Person {
  public name;
  constructor(name: string) {
    this.name = name;
  }
} 
```

### 类型

**它们是什么:**一种定义数据单元类型的方法。类型有布尔值、数字、字符串、数组、元组、枚举、any、void、null、undefined、never。

**为什么关注:**分配类型有助于我们在编译时轻松避免错误代码。如果您试图给一个变量赋值，而该变量与它的指定类型不匹配，您的 TypeScript 将不会编译。

**值得注意:**当没有指定类型时，TypeScript 将尝试推断类型，从单个值到复杂对象。

```
let name: string;
name = 'Jennifer' //works!
name = 1337 // will error

let arrayOfStrings: string[];
let anotherArrayOfStrings: Array<string>;

arrayOfStrings.push('hi') //works
arrayOfStrings.push(27) //errors 
```

### 模板字符串

**它们是什么:**不，这不是反勾复制粘贴错误！模板字符串是使用反勾号在字符串中嵌入变量的一种快捷方式。

**您为何关注:**它们可以帮您节省按键时间！

**值得注意:**它们可以有多行。

```
let myName = 'Jennifer';
console.log(`Hello, ${myName}`);

let myHTMLTemplate = `<p>Hello, ${myName}</p>` 
```

### 界面

**它们是什么:**一种定义一个对象、函数或类应该是什么样子的契约的方法。

**为什么关注:**接口不仅可以极大地帮助开发，而且有许多有用的角度接口，知道如何实现它们将有助于您编写更少的重复代码。

**值得注意:**实现接口的类必须实现接口描述的所有需要的方法和属性。此外，接口不是从源代码中生成的，它们只是在编译时使用。

```
interface Person {
  firstName: string
  lastName: string
  age?: number
}

let person1: Person;
person1 = {
  firstName: 'Jennifer',
  lastName: 'Wadella'
} //works

let person2: Person;
person2 = {
  name: 'Jennifer'
} //errors 
```

### 功能

它们是什么:它们是函数，但是在 TypeScript 中，我们可以给函数一个返回类型，也可以设置参数的类型。

**您为什么关心**:如果您向函数传递不正确的类型，传递比函数指定的更多或更少的参数，或者试图从类型不正确的函数返回值，TypeScript 将不会编译。

**值得注意的**:功能参数可以用`?`标记为可选

```
function buildGreeting(name: string, phrase: string): string {
  return `${phrase}, ${name}`
} 
```

这些概念将让你在 Angular 应用程序中快速开始使用 TypeScript，但要了解更多深度，请查看 [Bitovi Academy TypeScript 指南](https://www.bitovi.com/academy/learn-typescript.html)！

## RxJS

### 可观测量

**它们是什么:**一种发布/订阅模式，允许我们处理可能随时间变化的值，例如表单输入的值。

**关注原因:**许多 Angular APIs 依赖于 Observables，所以熟悉它们是必须的。不理解 observable 会让许多新的 Angular 开发人员停滞不前——例如，知道 observable 是声明性的，在您订阅它们之前不会执行发布值。

**值得注意:**Angular 的两个关键部分——http client 和 Router 都依赖于 Observables 和 RxJS。

```
import { Component, OnInit } from '@angular/core';
import { Observable } from 'rxjs';

@Component({
  selector: 'my-component',
  template: `<p>{{myDisplayValue}}</p>`,
  styleUrls: ['./app.component.less']
})
export class MyComponent implements OnInit {
  public myFakeObsData: Observable<string>;
  public mySubscription;
  public myDisplayValue;

  constructor() {
  }

  ngOnInit(): void {
    this.myFakeObsData = new Observable(observer => {
      setTimeout(() => {
        observer.next('hello');
      }, 1000);

      setTimeout(() => {
        observer.next('how are you');
      }, 2000);

      setTimeout(() => {
        observer.complete();
      }, 3000);
    });

    this.mySubscription = this.myFakeObsData.subscribe((val) => {
      this.myVal = val;
    });
  }
} 
```

### 订阅

**它们是什么:**如何获得可观察物体发出的数值。

**关注原因:**这是您获取数据的方式！如果你以前用过 promises/async/await，你会觉得很熟悉。

**值得注意的是:**除非在标记中使用异步管道，否则必须取消订阅以避免内存泄漏。

```
import { Component, OnInit } from '@angular/core';
import { ObsService } from '../obs-service.service';
import { Observable, Subscription } from 'rxjs';

@Component({
  selector: 'my-component',
  template: `<p>{{myDisplayValue}}</p>`,
  styleUrls: ['./obs1.component.less']
})
export class MyComponent implements OnInit {
  $mySubscription: Subscription;
  myDisplayValue: number;

  constructor(public obsService: ObsService) { }

  ngOnInit() {
     this.$mySubscription = this.obsService.getObs().subscribe((val) => {
      console.log('new value', val);
      this.myDisplayValue = val;
    });
  }

  ngOnDestroy() {
    if(this.$mySubscription) {
      this.$mySubscription.unsubscribe();
    }
  }
} 
```

使用异步管道:

```
import { Component, OnInit } from '@angular/core';
import { ObsService } from '../obs-service.service';

@Component({
  selector: 'app-obs-async',
  template: `<p>{{myDisplayValue | async}}</p>`,
  styleUrls: ['./obs-async.component.less']
})
export class ObsAsyncComponent implements OnInit {
  public myDisplayValue;
  constructor(public obsService: ObsService) { }

  ngOnInit() {
    this.myDisplayValue = this.obsService.getObs();
  }

} 
```

### 运算符

**它们是什么:**对可观测量发出的值进行运算的方法。把它们想象成溪流的洛达什。

**关注原因:**它们有助于数据的转换和映射。

**值得注意:**tap 操作符是一种无需创建新订阅即可控制日志值的方法。这对调试很有用。

```
import { Component, OnInit } from '@angular/core';
import { Observable } from 'rxjs';
import { map, tap } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: `<p>{{myVal | async}}</p>`,
  styleUrls: ['./app.component.less']
})
export class AppComponent implements OnInit {
  public myFakeObsData: Observable<string>;
  public mySubscription;
  public myVal;

  constructor() {
  }

  ngOnInit(): void {
    this.myFakeObsData = new Observable(observer => {
      setTimeout(() => {
        observer.next('hello');
      }, 1000);

      setTimeout(() => {
        observer.next('how are you');
      }, 2000);

      setTimeout(() => {
        observer.next('good I hope');
      }, 3000);

      setTimeout(() => {
        observer.complete();
      }, 4000);
    });

    const addName = map( (value) => value + ', Jennifer' );
    const addEnd = map( (value) => value + '!');

    let examplePipe1 = this.myFakeObsData.pipe( addName )
        .pipe( tap( (value) => console.log("val", value) ) );
    let examplePipe2 = examplePipe1.pipe( addEnd );

    this.myVal = examplePipe2;
  }
} 
```

这些概念将让你在 Angular 应用程序中快速开始使用 RxJS，但要了解更多深度，请查看 [Bitovi Academy RxJS 指南](https://www.bitovi.com/academy/learn-rxjs.html)！