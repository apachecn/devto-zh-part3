# 200 LOC 中的类型脚本依赖注入

> 原文：<https://dev.to/darcyrayner/typescript-dependency-injection-in-200-loc-12j7>

面向对象编程中最常见的模式之一是依赖注入，以及控制原则的、[反转(IOC)。IOC 容器通常是功能齐全的复杂野兽，甚至可以难倒经验丰富的程序员。它们接受具有依赖关系的类型的集合，当你需要某个东西的实例时，它们可以自动为你连接一个。](https://en.wikipedia.org/wiki/Inversion_of_control)

你可能已经在像 [Angular](https://nestjs.com/) 和 [NestJs](https://nestjs.com/) 这样的框架中看到过带有模块系统的类型脚本容器。或者你可能正在使用一个独立的容器，比如 [Inversify](http://inversify.io/) 。

揭开编程概念神秘面纱的最好方法之一是自己动手构建，因此本文将一步一步地构建一个最小的玩具容器。但是首先…

# 一堂快速历史课

回到 2014 年的框架大战期间，一些谷歌工程师遇到了一个问题。他们一直在开发 Angular 2，这时他们意识到构建它的语言 Typescript 有一个致命的缺陷。这是一种交易破坏者，所以他们做了谷歌工程师在这种情况下做的事情。他们发明了一种新语言。它叫做剧本。

[![Did I ever tell you the tragedy of AtScript the Short Lived](img/98732ff1d1642d0ff7e0af96ac6aed1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VYN0MPbD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1nrcrqwune7lkjj98gyn.jpg)

我不是在这里重复 AtScript 的历史。安德斯·海尔斯伯格(打字稿的创造者)，在这里给出了他的简短版本。就像 Anders 在他的演讲中提到的，当时的 Typescript 缺少 AtScript 想要解决的两个关键特性；装修工和倒影。它们是使国际奥委会打字稿成为可能的秘方。

## 装修工

如果您以前使用过 Typescript 容器，您可能会看到这样的内容:

```
@Injectable()
class SomeService {
    constructor(private anotherService: AnotherService) {}
} 
```

在顶部，我们有*可注射*装饰器。装饰者说这个类可以自动注入其依赖项。

一个[装饰器](https://www.typescriptlang.org/docs/handbook/decorators.html)是一个函数，它包装一个类、函数或方法，并给它添加行为。这对于定义与对象相关联的元数据非常有用。它还与 Typescript 中反射的工作方式联系在一起。

# 倒影

为了知道要连接哪些东西，我们需要能够在运行时检查类型。在进入 Typescript 之前，让我们看看 Javascript 是如何工作的。

```
const a = "hello there";
const b = 0b1;
console.log(typeof a); // "string";
console.log(typeof b); // "number"; 
```

虽然它并不完美，但 Javascript 确实支持一定程度的基本运行时反射。除了语言的基本类型(num、boolean、object、string、array 等)，类还携带运行时信息:

```
class Alpha {}
const a = new Alpha();
a instanceof Alpha; // true 
```

我们还可以检查类的`prototype`来获得方法列表。但这就是我们开始触及一些极限的地方。没有简单的方法来提取类属性或方法参数的名称。传统的纯 javascript 容器会使用类似于[的技巧，将函数或类转换成字符串，并手动解析该字符串以获得每个参数/属性的名称](https://stackoverflow.com/questions/1007981/how-to-get-function-parameter-names-values-dynamically)。容器将使用该名称来查找正确的依赖项。当然，如果您对代码运行一个精简器，这将会失败，因为所有这些参数名将会改变。这是 Angular 1 的一个常见问题，周围的工作涉及到大量的冗余。

所以，普通的 Javascript 在反射部分对我们帮助不大。为了解决这个问题，Typescript 使用一个名为 [reflect-metadata](https://www.npmjs.com/package/reflect-metadata) 的库来存储额外的类型信息。例如，分配给参数和属性的 Typescript 类型在运行时可用。它是通过“emitDecoratorMetadata”编译器选项启用的。

```
@SomeDecorator()
function someFunc(a: number, b: string){}
Reflect.getMetadata('design:types', someFunc); // Number, String 
```

不过有两个问题:

1.  类/函数必须有一个装饰器来保存元数据。
2.  只能记录类/枚举/基元类型。接口和联合类型以“对象”的形式出现。这是因为这些类型在编译后会完全消失，而类会保留下来。

总之，现在的背景就足够了。如果 Typescript decorators/reflect-metadata 仍然让你困惑，去看看[官方教程](https://www.typescriptlang.org/docs/handbook/decorators.html)。

# 代码

我们的容器将使用两个主要概念。令牌和提供者。令牌是我们的容器需要知道如何创建的东西的标识符，提供者描述如何创建它们。考虑到这一点，容器类的最小公共接口如下所示。

```
export class Container {
    addProvider<T>(provider: Provider<T>) {} // TODO
    inject<T>(type: Token<T>): T {} // TODO
} 
```

现在我们来定义一下我们的`Token`。标记可以引用一个类，或者在参数类型没有给出足够的关于注入什么的上下文的情况下，引用一个用装饰器附加到参数上的常量。

```
const API_URL_TOKEN = new InjectionToken('some-identifier');
const TWITTER_TOKEN = new InjectionToken('another-identifier');
class SomeClass {
    // Both AService, API_URL_TOKEN, and TWITTER_URL_TOKEN are all tokens.
    // We will define the Inject decorator later. 
    constructor(b: AService, @Inject(API_URL_TOKEN) apiURL: string, @Inject(TWITTER_URL_TOKEN) twitterUrl: string) {}
} 
```

我们对令牌的定义如下:

```
// We use this to refer to classes.
export interface Type<T> extends Function {
    // Has a constructor which takes any number of arguments. 
    // Can be an implicit constructor. 
    new (...args: any[]): T; 
}

export class InjectionToken {
    constructor(public injectionIdentifier: string) {}
}

// Our combined Token type
Token<T> = Type<T> | InjectionToken; 
```

接下来，让我们定义提供者。我们将实现三种不同的提供者类型。一个用于将现有值作为单例提供，一个用于通过工厂函数提供，一个用于仅提供要使用的类名。

```
// Every provider maps to a token.
export interface BaseProvider<T> {
    provide: Token<T>;
}

export interface ClassProvider<T> extends BaseProvider<T> {
    useClass: Type<T>;
}

export interface ValueProvider<T> extends BaseProvider<T> {
    useValue: T;
}

// To keep things simple, a factory is just a function which creates the type.
export type Factory<T> = () => T;

export interface FactoryProvider<T> extends BaseProvider<T> {
    useFactory: Factory<T>;
}

export type Provider<T> = ClassProvider<T> | ValueProvider<T> | FactoryProvider<T>; 
```

为了方便起见，让我们也加入一些类型的守卫。

```
export function isClassProvider<T>(provider: BaseProvider<T>): provider is ClassProvider<T> {
    return (provider as any).useClass !== undefined;
}
export function isValueProvider<T>(provider: BaseProvider<T>): provider is ValueProvider<T> {
    return (provider as any).useValue !== undefined;
}
export function isFactoryProvider<T>(provider: BaseProvider<T>): provider is FactoryProvider<T> {
    return (provider as any).useFactory !== undefined;
} 
```

这对我们的基础 API 来说相当不错。在准备实现容器之前，我们只需要定义两个装饰器。

```
// This class decorator adds a boolean property to the class
// metadata, marking it as 'injectable'. 
// It uses the reflect-metadata API.
const INJECTABLE_METADATA_KEY = Symbol('INJECTABLE_KEY');
export function Injectable() {
    return function(target: any) {
        // target in this case is the class being decorated. 
        Reflect.defineMetadata(INJECTABLE_METADATA_KEY, true, target);
        return target;
    };
}
// We also provide an easy way to query whether a class is
// injectable. Our container will reject classes which aren't
// marked as injectable.
export function isInjectable<T>(target: Type<T>) {
    return Reflect.getMetadata(INJECTABLE_METADATA_KEY, target) === true;
} 
```

我们定义了`Inject`装饰器，它将一个参数映射到另一个`Token`。

```
const INJECT_METADATA_KEY = Symbol('INJECT_KEY');
// This is a parameter decorator, it takes a token to map the parameter to.
export function Inject(token: Token<any>) {
    return function(target: any, _: string | symbol, index: number) {
        Reflect.defineMetadata(INJECT_METADATA_KEY, token, target, `index-${index}`);
        return target;
    };
}
export function getInjectionToken(target: any, index: number) {
    return Reflect.getMetadata(INJECT_METADATA_KEY, target, `index-${index}`) as Token<any> | undefined;
} 
```

# 容器

添加提供者的实现相当简单。你可以看到它只是一个简单的键值存储。providers 映射使用任何类型，但是我们知道`Token`和 Provider 总是匹配的，因为插入映射的唯一方法是使用`addProvider`方法。

```
class Container {
    private providers = new Map<Token<any>, Provider<any>>();

    addProvider<T>(provider: Provider<T>) {
        this.assertInjectableIfClassProvider(provider);
        this.providers.set(provider.provide, provider);
    }
    // ...
} 
```

我们使用`assertInjectableIfClassProvider`方法来确保提供给容器的所有类都被标记为`Injectable`，因此有元数据。这不是绝对必要的，但它将帮助我们在配置时发现问题。

```
class Container {
    // ...
    private assertInjectableIfClassProvider<T>(provider: Provider<T>) {
        if (isClassProvider(provider) && !isInjectable(provider.useClass)) {
            throw new Error(
            `Cannot provide ${this.getTokenName(provider.provide)} using class ${this.getTokenName(
                provider.useClass
            )}, ${this.getTokenName(provider.useClass)} isn't injectable`
            );
        }
    }

    // Returns a printable name for the token.
    private getTokenName<T>(token: Token<T>) {
        return token instanceof InjectionToken ? token.injectionIdentifier : token.name;
    }
    // ...
} 
```

接下来我们有我们的注入功能。第一个方法查找提供者，第二个方法确定提供者的类型，然后分别处理每种情况。

```
class Container {
    // ...
    inject<T>(type: Token<T>): T {
        let provider = this.providers.get(type);
        return this.injectWithProvider(type, provider);
    }

    private injectWithProvider<T>(type: Token<T>, provider?: Provider<T>): T {
        if (provider === undefined) {
            throw new Error(`No provider for type ${this.getTokenName(type)}`);
        }
        if (isClassProvider(provider)) {
            return this.injectClass(provider as ClassProvider<T>);
        } else if (isValueProvider(provider)) {
            return this.injectValue(provider as ValueProvider<T>);
        } else {
            // Factory provider by process of elimination
            return this.injectFactory(provider as FactoryProvider<T>);
        }
    }
    // ...
} 
```

价值和工厂提供者非常简单。一个是方法调用，一个只是返回值。类提供者稍微复杂一点，它需要为构造函数构造参数列表中的项，然后使用类引用调用构造函数。

```
class Container {
    // ...
    private injectValue<T>(valueProvider: ValueProvider<T>): T {
        return valueProvider.useValue;
    }

    private injectFactory<T>(valueProvider: FactoryProvider<T>): T {
        return valueProvider.useFactory();
    }

    private injectClass<T>(classProvider: ClassProvider<T>): T {
        const target = classProvider.useClass;
        const params = this.getInjectedParams(target);
        return Reflect.construct(target, params);
    }
    // ...
} 
```

构建参数列表的实现是事情变得棘手的地方。我们调用`reflect-metadata` API 来获取构造函数每个参数的类型列表。对于这些参数中的每一个，我们找到相关的令牌，然后递归地构造 is。

```
public class Container {
    // ...
    private getInjectedParams<T>(target: Type<T>) {
        const argTypes = Reflect.getMetadata(REFLECT_PARAMS, target) as (InjectableParam | undefined)[];
        if (argTypes === undefined) {
            return [];
        }
        return argTypes.map((argType, index) => {
            // The reflect-metadata API fails on circular dependencies,
            // and will return undefined for the argument instead.
            // We could handle this better, but for now let's just throw an error.
            if (argType === undefined) {
                throw new Error(
                    `Injection error. Recursive dependency detected in constructor for type ${
                    target.name
                    } with parameter at index ${index}`
                );
            }
            // Check if a 'Inject(INJECTION_TOKEN)' was added to the parameter.
            // This always takes priority over the parameter type.
            const overrideToken = getInjectionToken(target, index);
            const actualToken = overrideToken === undefined ? argType : overrideToken;
            let provider = this.providers.get(actualToken);
            return this.injectWithProvider(actualToken, provider);
        });
    }
} 
```

## 使用它

实现到此为止。这是使用我们的新容器后的样子。

```
 const API_TOKEN = new InjectionToken('api-token');

@Injectable()
class SomeService {
    constructor(@Inject(API_TOKEN)) {}
}

@Injectable()
class InjectableClass {
    constructor(public someService: SomeService) {}
}

const container = new Container();

container.addProvider({ provide: API_TOKEN, useValue: 'https://some-url.com' });
container.addProvider({ provide: SomeService, useClass: SomeService });
container.addProvider({ provide: InjectableClass, useClass: InjectableClass });

const instance = container.inject(InjectableClass); 
```

# 结论

虽然我们在这里建造的玩具容器相当简单，但它也很强大。你已经可以看到其他更先进的容器是如何建造的。包含测试和文档的工作演示库可以在[这里](https://github.com/darcy-rayner/toy-ioc)找到。如果你准备接受挑战，尝试一下，看看你能否用以下功能扩展它:

*   循环引用的早期检测(当您添加提供程序时)。
*   嵌套容器，增加了从子容器提供类型的能力，(类似于 Angular/NestJs 模块)。
*   具有注入参数的工厂。
*   在提供者中指定实例生命周期的范围(例如 singleton)。