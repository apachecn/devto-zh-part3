# #MonadicMonday 编译:五月

> 原文：<https://dev.to/ybogomolov/monadicmonday-compilation-may-5d3c>

最近，我在 Twitter 上发起了一个名为#monadicmonday 的小活动——每周一我都会发布一个帖子，介绍一些有用的、易于立即使用的 FP 内容。每个月我都会为那些喜欢读长文章的人做一个为期 4 周的汇编。

# 第五集:无标签决赛

欢迎收看#monadicmonday 第五集！今天我们将看看著名的无标签 Final 模式及其在 TypeScript 中的实现。

如果你没有听说过无标签最终风格(也称为“最终无标签解释器”)，这里有一个简短的回顾。

这个模式最初是由 Oleg Kiselyov 等人在他的论文“最终无标记，部分求值:简单类型语言的无标记阶段解释器”中提出的:[http://okmij.org/ftp/tagless-final/JFP.pdf](http://okmij.org/ftp/tagless-final/JFP.pdf)

TF 风格是一种在类型化函数语言中嵌入领域特定语言(DSL)的通用方法。它有效地将 eDSL 语句从它们的解释器中分离出来，允许构建一个健壮的可扩展应用程序。

TF 最显著的特性之一——它允许为同一个 eSDL 构建不同的解释器，因此您可以在优化支持缓存的解释器上运行您的生产代码，而您的测试可以使用简单的身份解释器来运行。

还要注意的是，函数式 Scala 影响者之一@jdegoes 反对 TF 风格，支持使用水平效果旋转的略微不同的方法:[https://skillsmatter.com/skillscasts/13247-scala-matters](https://skillsmatter.com/skillscasts/13247-scala-matters)

在我看来，TF 风格对于像 TypeScript 这样的函数式语言来说是一个有效的选择。所以我们来构建一个这种风格的小程序吧！

我们的程序将做简单的事情:它将作为一个用户询问随机上限，并返回一个在范围[0，bound]内的随机数。

在 TypeScript 中，我们没有对高级类型的适当支持，所以我们需要使用一些 hacks。所以我们从定义一个“HKT”同义词开始，它将遵循函子和单子接口:

```
import { Type, URIS } from 'fp-ts/lib/HKT';

export interface ProgramSyntax<F extends URIS, A> {
  map: <B>(f: (a: A) => B) => _<F, B>;
  chain: <B>(f: (a: A) => _<F, B>) => _<F, B>;
}

export type _<F extends URIS, A> = Type<F, A> & ProgramSyntax<F, A>; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将定义一些代数——类型为`A => F A`的运算集合，它们将成为我们 eDSL 的构建模块——即，我们可以用来构建最终程序的表达式。

```
export interface Program<F extends URIS> {
  // Exit the program with some message or code
  terminate: <A>(a: A) => _<F, A>;
}

export interface Console<F extends URIS> {
  // Print a line on some kind of console:
  print: (message: string) => _<F, void>;
  // Read a line from console:
  read: _<F, string>;
}

export interface Random<F extends URIS> {
  // Get next number in [0, upper) bounds:
  nextInt: (upper: number) => _<F, number>;
}

export type Main<F extends URIS> = Program<F> & Console<F> & Random<F>; 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们有了这些表达能力，我们就可以表达我们的程序了。我想到以下逻辑流程:

1.  App 求上限
2.  应用程序读取用户输入
3.  如果输入不是数字:
    1.  应用程序打印警告
    2.  App 询问用户是否要继续:
        1.  如果‘y’->重新开始
        2.  如果“否”->打印“再见”并终止
4.  如果输入是一个数字:
    1.  App 生成一个随机 int
    2.  应用程序打印此号码
    3.  App 询问用户是否要继续:
        1.  如果‘y’->重新开始
        2.  如果“否”->打印“再见”并终止

我想把程序的逻辑拆分成小函数来展示 TF 风格的核心思想:你可以只“召唤”那些你真正用到的功能部分！

```
const generateRandom = <F extends URIS>(F: Program<F> & Random<F>) =>
  (upper: number): _<F, number> => F.nextInt(upper);

const getUpperStr = <F extends URIS>(F: Program<F> & Console<F>): _<F, string> =>
  F.print('Enter random upper bound:')
    .chain(() => F.read);

const checkContinue = <F extends URIS>(F: Program<F> & Console<F>): _<F, boolean> =>
  F.print(`Do you want to continue?`)
    .chain(() => F.read)
    .chain((answer) => {
      switch (answer.toLowerCase()) {
        case 'y':
          return F.terminate(true);
        case 'n':
          return F.terminate(false);
        default:
          return checkContinue(F);
      }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到将`F`实例一遍又一遍地传递给各种函数的一些“负担”。这是我们打字稿必须付出的代价。在 Scala 等其他语言中，这样的实例是隐式传递的，因此代码更加简洁。

有了这些构件，我们终于可以表达我们的主程序了:

```
const main = <F extends URIS>(F: Main<F>): _<F, void> =>
  getUpperStr(F).chain(
    (upper) => parse(upper).foldL(
      () => F.print(`"${upper}" is not an integer`),
      (upperN) => generateRandom(F)(upperN).chain(
        (rand) => F.print(`Your random is: ${rand}`),
      ),
    ).chain(() => checkContinue(F).chain(
      (answer) => answer ?
        main(F) :
        F.print('Good-bye').chain(
          () => F.terminate(undefined),
        ),
    )),
  );

// Helper: a pure function to parse a string to number
const parse = (s: string): Option<number> => {
  const i = Number(s);
  return isNaN(i) || i % 1 !== 0 ? none : some(i);
}; 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果你试着运行这段代码，你会得到…什么也没有。为什么？因为我们的`main`只是一个表达式——它是需要解释的数据！接下来是 TF 风格的另一半:口译员。

因为我们正在构建一个控制台应用程序，所以让我们使用`Task` typeclass 来表达异步操作。以下是使用任务的 out 代数的实例:

```
import { log } from 'fp-ts/lib/Console';
import { randomInt } from 'fp-ts/lib/Random';
import { fromIO, Task, task, URI as TaskURI } from 'fp-ts/lib/Task';
import { createInterface } from 'readline';

const programTask: Program<TaskURI> = {
  terminate: task.of,
};

const randomTask: Random<TaskURI> = {
  nextInt: (upper) => fromIO(randomInt(0, upper)),
};

const consoleTask: Console<TaskURI> = {
  read: new Task(
    () => new Promise((resolve) => {
      const rl = createInterface({
        input: process.stdin,
        output: process.stdout,
      });
      rl.question('> ', (answer) => {
        rl.close();
        resolve(answer);
      });
    }),
  ),
  print: (message: string): Task<void> => fromIO(log(message)),
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以获得我们的`mainTask`并运行它:

```
const mainTask = main({
  ...programTask,
  ...randomTask,
  ...consoleTask,
});

mainTask.run(); 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个程序会话的例子:

```
 yuriy@MBP  ts-node ./src/monday/tagless.ts
Enter random upper bound:
> aaaa
"aaaa" is not an integer
Do you want to continue?
> y
Enter random upper bound:
> 23
Your random is: 4
Do you want to continue?
> n
Good-bye 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们的旅程还没有结束。我们可以为`Identity`类型定义一组解释器，这些解释器什么也不做，使用硬编码的值——但是允许我们在一组固定的值上调试程序的逻辑:

```
import { identity, URI as IdURI } from 'fp-ts/lib/Identity';
const programId: Program<IdURI> = {
  terminate: identity.of,
};

const randomId: Random<IdURI> = {
  nextInt: (upper) => identity.of(upper),
};

const consoleId: Console<IdURI> = {
  print: (_message) => identity.of(undefined),
  read: identity.of('42'),
};

console.log(
  generateRandom({ ...programId, ...randomId })(
    +getUpperStr({ ...programId, ...consoleId }).fold((x) => x),
  ).toString(),
); // => new Identity(42) 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以使用一个带有输入、输出和随机元组的`State`单子——并用它来创建完全同步的测试！或者，我们可以定义另一组代数实例，将输出“呈现”为 HTML，并从对话框窗口读取输入。或者我们可以引入某种网络交互——比如说，不把我们的数据记录到终端上，而是把它放在 LogStash 中。或者在卡夫卡那里。或者在任何数据库中。

我对无标签期末考试的简短介绍到此结束。

# 第六集:打字稿里的 ZIO

欢迎收看#monadicmonday 第六集！今天我将回答我的一个追随者的问题:“你能分享一个 ZIO 在 Typescript 中的例子吗？”。

对于那些不熟悉 ZIO 是什么的人，让我简单解释一下。
ZIO 是一个 trifunctor(即，它接受 3 个类型参数并可以映射它们)，它封装了以下思想:
`ZIO[-R, +E, +A]`描述了一个计算，它可以从`R`类型的环境中读取，可以因类型`E`的错误而失败，或者因类型`A`的值而成功。

总之，ZIO 与 a `Reader[R, Task[Either[E, A]]]`相同，但它在一个高性能的容器中烘焙所有效果——与大多数 Haskell 开发人员熟悉的使用一元变形器的方式相反。你可以在@jdegoes 的博客文章中读到更多关于这样的设计决定:[http://degoes.net/articles/rotating-effects](http://degoes.net/articles/rotating-effects)

您可能想知道为什么 ZIO 包含了一个`Reader`而不是一个`State`，以及如何使用它实现一个可变的环境。我建议阅读@snoyberg: [的精彩文章 https://www . FP complete . com/blog/2017/06/readert-design-pattern](https://www.fpcomplete.com/blog/2017/06/readert-design-pattern)。它是用 Haskell 编写的，但是说实话:Haskell 现在是一种“事实上的”FP 语言，所以你应该熟悉它简单的语法。
简而言之，Reader 让你编写并发程序，并保证共享资源的运行时不变性，这是并发编程的基石，而 State/Writer 没有这样的保证。

让我们切换到我的读者更熟悉的 TypeScript 语法，并尝试在其中复制 ZIO。我将调用我的 execise `TIO`(显然 T 代表 TypeScript)来避免名字冲突。

我们从类型类定义开始:

```
declare module 'fp-ts/lib/HKT' {
  interface URI2HKT3<U, L, A> {
    // We need to have our R, E, A parameters 
    // called U, L, A in order for this HKT pattern to work.
    // It is required that all interface augmentations 
    // have the same parameters:
    TIO: TIO<U, L, A>;
  }
}

export const URI = 'TIO';
export type URI = typeof URI; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们必须定义我们的基类，`TIO`。为了让它快速工作，我应该手动编写`Reader<R, Task<Either<E, A>>>`的实现。这是一项相当繁琐的工作，所以我仍然会使用 monad transformers 来实现相同的界面:

```
import { Either } from 'fp-ts/lib/Either';
import * as readerT from 'fp-ts/lib/ReaderT';
import * as taskEither from 'fp-ts/lib/TaskEither';

import TaskEither = taskEither.TaskEither;

const readerTTaskEither = readerT.getReaderT2v(taskEither.taskEither);

export class TIO<R, E, A> {
  readonly _R!: R;
  readonly _E!: E;
  readonly _A!: A;
  readonly _URI!: URI;

  constructor(readonly value: (e: R) => TaskEither<E, A>) { }

  run(e: R): Promise<Either<E, A>> {
    return this.value(e).run();
  }

  chain<R1 extends R, E1, B>(f: (a: A) => TIO<R1, E1, B>): TIO<R1, E1, B>;
  chain<E1, B>(f: (a: A) => TIO<R, E1, B>): TIO<R, E1, B>;
  chain<B>(f: (a: A) => TIO<R, E, B>): TIO<R, E, B> {
    return new TIO(readerTTaskEither.chain(this.value, (a) => f(a).value));
  }

  // Some methods are skipped for readability
}

export const of = <R, E, A>(value: (e: R) => TaskEither<E, A>) => new TIO(value);
export const fromIO = <R, E, A>(a: io.IO<A>) => new TIO<R, E, A>(() => taskEither.fromIO(a));

export const tio = {
  of,
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，ZIO 在其核心定义了一些同义词:

```
export type UIO<A> = TIO<any, never, A>;
export type Task<A> = TIO<any, Error, A>;
export type IO<E, A> = TIO<any, E, A>; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以表达我在 MonadicMonday 的上一期中用作演示的相同程序——该程序要求用户提供随机生成的上限，打印结果随机数，并询问用户是否想继续。为了展示 ZIO 作为一种模式的威力，我将稍微改变一下逻辑:RNG 将从环境中读取较低的界限，而不是使用硬编码的“1”:

```
export interface MainEnvironment {
  lowerBound: number;
}

export const parse = (s: string): Option<number> => {
  const i = Number(s);
  return isNaN(i) || i % 1 !== 0 ? none : some(i);
};

// Take a note: `ask` just returns it's environment, 
// so we can `chain` it and gain access to the runtime value:
export const generateRandom = (upper: number): TIO<MainEnvironment, never, number> =>
  ask().chain(({ lowerBound }) => fromIO(randomInt(lowerBound, upper))); 
```

Enter fullscreen mode Exit fullscreen mode

今天，这个程序的表达能力将会更强，因为我们不必携带第一个参数来决定我们程序的解释器的运行时实现。我们仍然保留了 FP 风格程序的一个主要好处——作为数据的算法——但是我们失去了用简单的无标签最终方法测试效果的能力。

```
import { ask, fromIO, Task, TIO, tio } from './tio';

export const terminate = tio.of;
export const print = (message: string): Task<void> => fromIO(log(message));
export const getUpperStr: Task<string> = print('Enter random upper bound:').chain(() => read);
export const checkContinue: Task<boolean> = print(`Do you want to continue?`)
  .chain(() => read)
  .chain((answer) => {
    switch (answer.toLowerCase()) {
      case 'y':
        return terminate(true);
      case 'n':
        return terminate(false);
      default:
        return checkContinue;
    }
  });

export const main: TIO<MainEnvironment, never, void> = getUpperStr.chain(
  (upper) => parse(upper).foldL(
    () => print(`"${upper}" is not an integer`),
    (upperN) => generateRandom(upperN).chain(
      (rand) => print(`Your random is: ${rand}`),
    ),
  ).chain(() => checkContinue.chain(
    (answer) => answer ? main : print('Good-bye').chain(() => terminate(undefined)),
  )),
); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过提供一个正确的环境来运行我们的程序:

```
import { main } from './main';

main.run({ lowerBound: 33 }); 
```

Enter fullscreen mode Exit fullscreen mode

Scala 的 ZIO 可以测试:【http://degoes.net/articles/testable-zio】T2。但在我看来，用 TF 风格测试程序要简单得多。在这两种方法中，您都需要传递模拟对象，但是使用 TF，您将它传递(并携带)给每个方法，而使用 ZIO，您需要编写通过`ZIO.access` / `ZIO.accessM`显式访问环境效果的程序。

在这一集里，我不会展示 ZIO 风格的测试方法。John @jdegoes 对此描述得非常好，所以请去看看他的文章。

作为总结，我们先简单对比一下 TF 和 ZIO:

*无标签决赛*
优点:

*   对效果的显式抽象；
*   与 MTL/自由单子风格相比，性能更高；
*   良好的可组合性；
*   易于测试；
*   是一种模式，而不是最终的实现——因此在许多语言中以 TF 风格编写是可能的；缺点:
*   非常陡峭的学习曲线；
*   程序的每一位都需要 TF 风格；
*   在真实的应用程序中，效果堆栈可能非常大；

*ZIO*
优点:

*   高性能(详见约翰博客)；
*   新开发人员更容易接近(更浅的学习曲线)；
*   相当好的可组合性；缺点:
*   更难测试(效果必须在读者的环境中烘焙)；
*   Still 更像是一个端到端的实现，而不是 Scala 独有的模式；

我想最好将 TF 与 ReaderTaskEither 模式进行比较(可通过 MTL 风格实现)。有很多关于这个话题的演讲和文章，所以我不会在这里重复。相反，我想给你们看一个比较 TF 与 MTL 和 Bifunctor IO 的精彩演讲:[https://www.youtube.com/watch?v=QM86Ab3lL20](https://www.youtube.com/watch?v=QM86Ab3lL20)

像往常一样，你可以在我的 GitHub 上找到这一集的代码示例:[https://github.com/YBogomolov/monadic-mondays](https://github.com/YBogomolov/monadic-mondays)

# 第七集:功能光学

欢迎收看#monadicmonday 第七集！今天我们将谈论一些功能光学:透镜，棱镜，折叠和遍历。

lenses 的历史始于 2009 年的“迭代器模式的本质”一文，它引发了大量的级联出版物，导致了当前 Haskell 的`lens`包和不同语言的各种实现。在这一集里，我会使用@GiulioCanti 的一个很棒的`monocle-ts`包，但是你也可以使用任何你觉得合适的光学库。概念是一样的。

你可以在 https://github.com/ekmett/lens/wiki/History-of-Lenses 找到更多历史参考资料

在以函数式风格编写时，我们通常使用不可变的数据结构。它为我们的代码提供了难以置信的保证，但也带来了一个缺点:很难更新(重建)深度嵌套的数据结构。因此，光学到 resque！

Functional optics 为您提供了一种处理复杂的不可变数据结构(深度嵌套、递归等)的方法，而无需手动维护该结构。当前一代的光学库严重依赖于 profunctor 编码，但是我们将使用一种更简单的编码。

考虑一个命令式的例子:

```
// We have an organizational data structure like this:
interface Organization {
  title: string;
  employees: Array< {
    name: string;
    age: number;
    car?: {
      model: string;
      plateNum: string;
    };
    partner?: {
      name: string;
      age: number;
    };
    projects: Array<{
      title: string;
      code: string;
      start: Date;
      end: Date;
    }>
  }>
}

// Say, you're changing internal project codes from lowercase to uppercase + prefix. 
// How would you do this imperatively?
function changeCodes(o: Organization): Organization {
  const newOrg: Organization = {
    ...o,
    employees: o.employees.map((emp) => ({
      ...emp,
      // Note: we need to preserve absense of `projects`, 
      // if it was absent in the first place:
      ...(emp.projects ? {
        projects: emp.projects.map((p) => ({
          ...p,
          code: 'MY-' + p.code.toLocaleUpperCase(),
        })),
      } : {}),
    })),
  };

  return newOrg;
} 
```

Enter fullscreen mode Exit fullscreen mode

我应该说这种方式容易出错(例如，很容易犯错误并强行为所有员工创建`projects`字段——即使他们没有这个字段),繁琐且不可组合。然而，有一种方法可以轻松解决这个问题。

> 注意，加分是给那些记得第三集，并说你可以在这里使用一个名为“catamorphism”的递归方案(或简单地称为`fold`)的人。但是今天我们通过光学来看看这个问题。

光学背后的直觉直接来自物理学:每个光学实体允许你“聚焦”在数据结构的某些部分，并提供与它们交互的接口。

先说一个`Iso`。“Iso”代表“同构”，通常可以认为是一对“获取”和“重建”值的函数(与“获取”相反)。请注意，一般来说，任何两个类型之间的同构都是不确定的:

```
interface Iso<S, A> {
  get: (s: S) => A;
  reverseGet: (a: A) => S;
} 
```

Enter fullscreen mode Exit fullscreen mode

我能想到的一个例子是一对两个“1/x”函数。给定一个`y`值，“get”应用`x -> 1/x`并留给你`1/y`，而“reverseGet”再次应用`x -> 1/x`函数从`1/y`重建`y`。
另一个例子可能是 JS 中的一对“toLocaleLowerCase”和“toLocaleUpperCase”函数——但是当且仅当我们同意 Iso 的输入必须已经是大写的。`Iso`的法则如下:

```
reverseGet . get ≅ id 
```

Enter fullscreen mode Exit fullscreen mode

这样看起来似乎没什么用，因为一般来说你无法从任何一个`A`获得任何一个`B`。但它提升了下一个光学元件—`Lens`。

`Lens<S, A>`是一对函数，用来“获取”和“设置”结构中的值。请注意，与`Iso`不同，`Lens`意味着结构本身已经定义好了，所以如果缺少中间层次，就不能“设置”一个深度嵌套的层次。镜头非常适合处理产品类型(元组&对象)。

```
interface Lens<S, A> {
  get: (s: S) => A;
  set: (a: A) => (s: S) => S;
} 
```

Enter fullscreen mode Exit fullscreen mode

考虑一个例子:

```
interface Foo {
  bar: string;
  baz: {
    quux: number;
    fizz: {
      buzz: boolean;
    };
  };
}

const aFoo: Foo = { bar: 'bar', baz: { quux: 1, fizz: { buzz: true } } };

const buzzLens = Lens.fromPath<Foo>()(['baz', 'fizz', 'buzz']);

buzzLens.get(aFoo); // => true
buzzLens.set(false)(aFoo); // => { bar: 'bar', baz: { quux: 1, fizz: { buzz: false } } } 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要处理可能丢失的字段，您将需要使用`Prism`或`Optional`。它们都是用来处理 sum 类型的:数组，选项，或者其他，它们是用一对函数定义的:

```
interface Prism<S, A> {
  getOption: (s: S) => Option<A>;
  reverseGet: (a: A) => S;
}

interface Optional<S, A> {
  getOption: (s: S) => Option<A>;
  set: (a: A) => (s: S) => S;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您来自命令式编程背景，您可以将 Lens、Optional & Prism 视为重载属性访问器的方法。

另一种有用的光学元件。给定数据类型`M`的幺半群实例，它允许将其值折叠(缩减)为类型`M` :

```
interface Fold<S, A> {
  foldMap: <M>(M: Monoid<M>) => 
    (f: (a: A) => M) => (s: S) => M;
} 
```

Enter fullscreen mode Exit fullscreen mode

是一个可以来回移动的光学系统(真令人惊讶！)数据结构并执行以下操作:

```
interface Traversal<S, A> {
  modifyF: <F>(F: Applicative<F>): (f: (a: A) => HKT<F, A>) => (s: S) => HKT<F, S>;
  modify(f: (a: A) => A): (s: S) => S;
  set(a: A): (s: S) => S;
  // `filter` allows narrowing a Traversal:
  filter(predicate: Predicate<A>): Traversal<S, A>;
} 
```

Enter fullscreen mode Exit fullscreen mode

考虑一个例子:

```
import { Tree, tree } from 'fp-ts/lib/Tree';

interface User {
  name: string;
  age: number;
}

const hierarchy = new Tree<User>({ name: 'Boss', age: 42 }, [
  new Tree({ name: 'Manager1', age: 35 }, [
    tree.of({ name: 'Emp1', age: 45 }),
    tree.of({ name: 'Emp2', age: 64 }),
  ]),
  new Tree({ name: 'Manager2', age: 52 }, [
    tree.of({ name: 'Emp3', age: 27 }),
    tree.of({ name: 'Emp4', age: 32 }),
    tree.of({ name: 'Emp5', age: 39 }),
  ]),
]);

const hierarchyTraversal = fromTraversable(tree)<User>();
console.dir(hierarchyTraversal.modify(
  (u) => ({ ...u, name: u.name.toLocaleUpperCase() }),
)(hierarchy), { depth: null }); // =>
// Tree {
//   value: { name: 'BOSS', age: 42 },
//   forest:
//    [ Tree {
//        value: { name: 'MANAGER1', age: 35 },
//        forest:
//         [ Tree { value: { name: 'EMP1', age: 45 }, forest: [] },
//           Tree { value: { name: 'EMP2', age: 64 }, forest: [] } ] },
//      Tree {
//        value: { name: 'MANAGER2', age: 52 },
//        forest:
//         [ Tree { value: { name: 'EMP3', age: 27 }, forest: [] },
//           Tree { value: { name: 'EMP4', age: 32 }, forest: [] },
//           Tree { value: { name: 'EMP5', age: 39 }, forest: [] } ] } ] } 
```

Enter fullscreen mode Exit fullscreen mode

光学最好的一点就是容易互相构图。你可以从一个镜头开始，用一个棱镜构图以放大，然后用一个遍历构图以遍历内部结构，等等。

让我们看另一个例子:给定一个组织结构，计算其成员的平均年龄。

我们从 or 数据类型定义和示例数据集开始:

```
import { Tree, tree } from 'fp-ts/lib/Tree';

interface User {
  name: string;
  age: number;
}

const hierarchy = new Tree<User>({ name: 'Boss', age: 42 }, [
  new Tree({ name: 'Manager1', age: 35 }, [
    tree.of({ name: 'Emp1', age: 45 }),
    tree.of({ name: 'Emp2', age: 64 }),
  ]),
  new Tree({ name: 'Manager2', age: 52 }, [
    tree.of({ name: 'Emp3', age: 27 }),
    tree.of({ name: 'Emp4', age: 32 }),
    tree.of({ name: 'Emp5', age: 39 }),
  ]),
]); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以写出解决方案:

```
import { identity } from 'fp-ts/lib/function';
import { getTupleMonoid, monoidSum } from 'fp-ts/lib/Monoid';
import { fromFoldable, Lens } from 'monocle-ts';

const ageTupleLens = new Lens<User, [number, number]>(
  (u) => [u.age, 1],
  ([age]) => (u) => ({ ...u, age }),
);

const hierarchyFold = fromFoldable(tree)<User>().composeLens(ageTupleLens);

const tupleMonoid = getTupleMonoid(monoidSum, monoidSum);

// Note that we get an answer in a single traverse of the tree:
const [agesSum, agesCount] = hierarchyFold.foldMap(tupleMonoid)(identity)(hierarchy);
console.log(agesSum / agesCount); // => 42 
```

Enter fullscreen mode Exit fullscreen mode

如果你想深入了解光学的分类观点，你应该阅读@BartoszMilewski 的精彩文章:[https://BartoszMilewski . com/2017/07/07/profunctor-optics-the-categorial-view/](https://bartoszmilewski.com/2017/07/07/profunctor-optics-the-categorical-view/)
然而，这不是入门级作品，所以要准备好复杂的分类主题，如 profunctor、adjust s 和 Tambara 模块。

# 第八集:自由单子

欢迎收看#monadicmonday 第八集！今天我们将讨论你要求的主题:自由单子。

自由单子是一种描述 eDSL 并为其构建表达式树和解释器的方式。这听起来很像我们在 ep.5 中讨论过的无标签期末考试，但是自由单子有它自己的特点。

首先，为什么取这个名字——“自由”单子？“免费”的意思是“免于评价”，就像在“自由”中，而不是在“免费啤酒”中。自由单子是一种允许你从任何函子构建单子的结构。对我们程序员来说更重要的是，它还允许我们以栈安全的方式运行它。

明确地说，自由单子是健忘函子的左伴随。健忘函子取一个单子，然后“忘记”它的`of`和`chain`部分，只保留`map`。它的左伴随有相反的箭头，所以自由单子是一个结构:
–带一个函子；

*   添加尖头部分(`of`)；
*   添加一元行为(`chain`)。

使用自由单子，我们将我们的计算表示为 AST，一些表达式是终端命令，其他的有子表达式作为`next`部分。

和往常一样，我会用一个小例子来说明今天的话题。让我们写一个简单的程序，从一个文本文件中读取 2 行，并把它们放到控制台上。

使用自由单子编写的程序只是计算的描述，而不是计算本身。我们的程序是价值观，我们需要解释它们来把事情真正做好。

我们的域模型将只是带有句柄的`File`和`isOpen`属性:

```
import fs from 'fs';

// Data structure we'll be working with:
interface File {
  handle: fs.promises.FileHandle;
  isOpen: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们从一个 HKT `OpsF<A>`开始，这将是我们的自由构建。它允许我们为我们的领域构建表达式树:

```
// Some boilerplate for higher-kinded types:
const OpsFURI = 'Ops';
type OpsFURI = typeof OpsFURI;

declare module 'fp-ts/lib/HKT' {
  interface URI2HKT<A> {
    Ops: OpsF<A>;
  }
}

// Our eDSL – building blocks of our small application:
type OpsF<A> = OpenFile<A> | ReadLine<A> | Log<A> | CloseFile<A>;

class OpenFile<A> {
  readonly _tag: 'OpenFile' = 'OpenFile';
  readonly _A!: A;
  readonly _URI!: OpsFURI;
  constructor(readonly name: string, readonly next: (file: File) => A) { }
}

class ReadLine<A> {
  readonly _tag: 'ReadLine' = 'ReadLine';
  readonly _A!: A;
  readonly _URI!: OpsFURI;
  constructor(readonly file: File, readonly next: (a: [string, File]) => A) { }
}

class Log<A> {
  readonly _tag: 'Log' = 'Log';
  readonly _A!: A;
  readonly _URI!: OpsFURI;
  constructor(readonly message: string, readonly next: () => A) { }
}

class CloseFile<A> {
  readonly _tag: 'CloseFile' = 'CloseFile';
  readonly _A!: A;
  readonly _URI!: OpsFURI;
  constructor(readonly file: File, readonly next: (file: File) => A) { }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们希望有一种方法来使用这些构建模块，最好是用 monadic 接口增强。所以我们需要将我们的纯数据类型`OpsF`提升到一个自由的单子世界。幸运的是，我们有一个专门的函数——`liftF`:

```
import { liftF } from 'fp-ts/lib/Free';

// Helpers for building eDSL expressions.
// Note that in general we have a possibility to fiddle with values
// as they are getting returned in `next` part:
const open = (name: string) => liftF(new OpenFile(name, identity));
const readLine = (file: File) => liftF(new ReadLine(file, identity));
const log = (message: string) => liftF(new Log(message, () => void 0));
const closeFile = (file: File) => liftF(new CloseFile(file, identity)); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以构建表达我们领域的逻辑的程序:

```
// This is our program – just a value, nothing REALLY happens here,
// we just declare our intentions:
const program = open('./src/episode-8/file.csv')
  .chain((file) => log(`File is open: ${file.isOpen}`).chain(() => readLine(file)))
  .chain(([line, file]) => log(line).chain(() => readLine(file)))
  .chain(([line, file]) => log(line).chain(() => closeFile(file)))
  .chain((file) => log(`File is open: ${file.isOpen}`));
// => program :: Free<"Ops", void> 
```

Enter fullscreen mode Exit fullscreen mode

似乎非常接近无标签的最终风格——我们已经创建了一个嵌入式 DSL，并使用它的语句来表达我们的意图。我们将程序作为数据，下一步我们将构建一些解释器来运行实际的代码。

然而，正如我之前所说的，有一个转折。无标签 final 围绕 Final 代数(即类型为`A => F<A>`的操作集)旋转，而自由单子方法使用递归数据类型`Free<F<A>>`构建表达式树，作为初始代数。

你可以在这里读到关于自由单子的更专业的分类描述:[https://www . paolocapriotti . com/blog/2013/11/20/free-monads-part-1](https://www.paolocapriotti.com/blog/2013/11/20/free-monads-part-1)(第 2 部分和第 3 部分也是可用的)。

当然，我不能推荐@BartoszMilewski 的一篇令人敬畏的博客:[https://BartoszMilewski . com/2018/08/20/recursion-schemes-for-higher-代数/](https://bartoszmilewski.com/2018/08/20/recursion-schemes-for-higher-algebras/) 。必读！

无论如何，让我们继续为我们的程序定义解释器。像往常一样，我将定义其中的两个:一个用于`Identity`单子，另一个用于`Task` :

```
import { Identity, identity as id } from 'fp-ts/lib/Identity';

const exaustive = (x: never): never => x;

// For sake of brevity I will use a "global state" here instead of `State` monad:
let position = 0;
const lines = ['first line', 'second line'];

const identityInterpreter = <A>(fa: OpsF<A>): Identity<A> => {
  switch (fa._tag) {
    case 'OpenFile':
      // I'm stubbing the `handle` with null here, as my Identity interpreter won't be using it:
      return id.of(fa.next({ isOpen: true, handle: null as unknown as fs.promises.FileHandle }));

    case 'ReadLine':
      return id.of(fa.next([lines[position++], fa.file]));

    case 'Log':
      return id.of(fa.next());

    case 'CloseFile':
      return id.of(fa.next({ isOpen: false, handle: null as unknown as fs.promises.FileHandle }));
  }

  // A little trick: if you add a call to `exaustive`,
  // the compiler will force you to use all possible `_tag` values in switch!
  return exaustive(fa);
}; 
```

Enter fullscreen mode Exit fullscreen mode

`Task`的解释器将使用节点的文件系统 API:
做一些实际的工作

```
import { delay, Task, task } from 'fp-ts/lib/Task';
import fs from 'fs';

const taskInterpreter = <A>(fa: OpsF<A>): Task<A> => {
  switch (fa._tag) {
    case 'OpenFile':
      return new Task(
        () => fs.promises.open(fa.name, 'r')
          .then((handle) => fa.next({ isOpen: true, handle })),
      );

    case 'ReadLine':
      console.timeLog('TASK', 'Read line');
      return new Task(
        () => fa.file.handle.read(Buffer.from(new ArrayBuffer(24), 0, 24), 0, 24)
          .then(({ buffer }) => buffer.toString()),
      ).chain((line) => task.of(fa.next([line, fa.file])));

    case 'Log':
      console.log('>>>>>', fa.message);
      // Let's pretend that we're logging to DB here, hence the
      return delay(500, undefined).chain(() => {
        console.timeLog('TASK', 'Log');
        return task.of(fa.next());
      });

    case 'CloseFile':
      return new Task(() => fa.file.handle.close()).chain(() => {
        console.timeLog('TASK', 'Close file');
        return task.of(fa.next({ ...fa.file, isOpen: false }));
      });
  }

  return exaustive(fa);
}; 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用`foldFree`函数运行我们的解释器时，我们将看到下面的输出(我已经添加了一些工具，所以您可以看到实际的持续时间):

```
console.log('Id interpreter');
const resId = foldFree(id)(identityInterpreter, program); // => Identity<void>
console.log(resId.value);

console.log('\nTask interpreter');
const resTask = foldFree(task)(taskInterpreter, program); // => Task<void>
console.time('TASK');
resTask.run().then(() => console.timeEnd('TASK'));

/*
> ts-node ./src/episode-8/free.ts

Id interpreter
undefined

Task interpreter
>>>>> File is open: true
TASK: 506.466ms Log
TASK: 506.756ms Read line
>>>>> this is our first line!

TASK: 1013.263ms Log
TASK: 1013.459ms Read line
>>>>> this is the second line

TASK: 1518.309ms Log
TASK: 1518.753ms Close file
>>>>> File is open: false
TASK: 2021.198ms Log
TASK: 2021.623ms
*/ 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们谈谈我前面提到的堆栈安全。当你为自由单子编写解释器时，很容易导致堆栈溢出，因为你正在(分解)可能无限的递归数据结构。为了帮助解决这个问题，使用了一种叫做“蹦床”的普通技术。基本上，蹦床意味着在一个循环中执行递归调用——例如，为编译器做尾部调用优化。蹦床函数的一般定义是这样的:

```
// Enter Trampoline!
type Trampoline<T> = T | (() => Trampoline<T>);

function trampoline<T>(firstResult: Trampoline<T>) {
  let result = firstResult;
  while (result instanceof Function) {
    result = result();
  }
  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看另一个例子。让我们手写简单的`IO` monad 并用它表达无限递归循环:

```
// Free IO monad – representing generic computations:
const IOFURI = 'StackOps';
type IOFURI = typeof IOFURI;

type IO<A> = Return<A> | Suspend<A> | FlatMap<A>;

class Return<A> {
  readonly _tag: 'Return' = 'Return';
  readonly _A!: A;
  readonly _URI!: IOFURI;
  constructor(readonly a: A) { }
}

class Suspend<A> {
  readonly _tag: 'Suspend' = 'Suspend';
  readonly _A!: A;
  readonly _URI!: IOFURI;
  constructor(readonly resume: () => A) { }
}

class FlatMap<A> {
  readonly _tag: 'FlatMap' = 'FlatMap';
  readonly _A!: A;
  readonly _URI!: IOFURI;
  constructor(readonly fa: IO<A>, readonly f: (a: A) => IO<A>) { }
}

// Runs given `IO` forever:
const forever = <A>(a: IO<A>): IO<A> => new FlatMap(a, () => forever(a));

// Should log current timestamp infinitely:
const program = forever(new Suspend(() => console.log(Date.now()))); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以创建一个`run`解释器的简单实现，如下所示:

```
const run = <A>(fa: IO<A>): A => {
  switch (fa._tag) {
    case 'Return':
      return fa.a;
    case 'Suspend':
      return fa.resume();
    case 'FlatMap': {
      const x = fa.fa;
      const f = fa.f;
      switch (x._tag) {
        case 'Return':
          return run(f(x.a));
        case 'Suspend':
          return run(f(x.resume()));
        default:
        case 'FlatMap':
          const y = x.fa;
          const g = x.f;
          return run(new FlatMap(y, (a) => new FlatMap(g(a), f)));
      }
    }
  }

  return exaustive(fa);
}; 
```

Enter fullscreen mode Exit fullscreen mode

然而，当您尝试运行这个示例时，它会失败:

```
 RangeError: Maximum call stack size exceeded
    at RegExp.test (<anonymous>)
    at WriteStream.getColorDepth (internal/tty.js:128:22)
    at Console.(anonymous function) (console.js:183:16)
    at Console.(anonymous function) (console.js:190:40)
    at Console.log (console.js:202:31)
    at Suspend [as resume] (/Users/yuriy/Projects/monadic-mondays/src/episode-8/stack.ts:95:51)
    at run (/Users/yuriy/Projects/monadic-mondays/src/episode-8/stack.ts:80:27)
    at run (/Users/yuriy/Projects/monadic-mondays/src/episode-8/stack.ts:80:18)
    at run (/Users/yuriy/Projects/monadic-mondays/src/episode-8/stack.ts:80:18)
    at run (/Users/yuriy/Projects/monadic-mondays/src/episode-8/stack.ts:80:18) 
```

Enter fullscreen mode Exit fullscreen mode

所以我们需要使用蹦床来安全地展开这个递归的 Uroboros。我们稍微改变了一下`run`的签名，将递归调用返回为 thunks:

```
const run = <A>(fa: IO<A>): Trampoline<A> => {
  switch (fa._tag) {
    case 'Return':
      return fa.a;
    case 'Suspend':
      return fa.resume();
    case 'FlatMap': {
      const x = fa.fa;
      const f = fa.f;
      switch (x._tag) {
        case 'Return':
          return () => run(f(x.a));
        case 'Suspend':
          return () => run(f(x.resume()));
        default:
        case 'FlatMap':
          const y = x.fa;
          const g = x.f;
          return () => run(new FlatMap(y, (a) => new FlatMap(g(a), f)));
      }
    }
  }

  return exaustive(fa);
};

// Now this won't fall and provide an infinite stream of Unix timestamps:
trampoline(run(program)); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想更深入地钻研蹦床，我推荐阅读这两篇伟大的论文:
[http://functorial.com/stack-safety-for-free/index.pdf](http://functorial.com/stack-safety-for-free/index.pdf)
[http://blog.higher-order.com/assets/trampolines.pdf](http://blog.higher-order.com/assets/trampolines.pdf)

正如你可能已经猜到的，我们手写的`IO`只是一个`Free IO a`单子的特化。我把使用`fp-ts`中的`Free`重写这个例子留给好奇的读者。

我应该承认，由于堆栈上的大量分配，自由单子通常被认为是有害的。如果你更仔细地检查代码，你会注意到 Free 有规律地重构它的数据结构，导致大量的分配。
你可以在马克·卡尔波夫的一篇文章中找到更多细节:[https://马克·卡尔波夫. com/post/free-monad-considered-habital . html](https://markkarpov.com/post/free-monad-considered-harmful.html)
所以一般来说，在我看来，最好使用 ZIO(如果你写的是 Scala)和 Tagless Final(否则)这样的其他堆栈节约技术。

这就结束了我对自由单子和一般自由结构的解释。像往常一样，所有代码示例都可以在[https://github.com/YBogomolov/monadic-mondays](https://github.com/YBogomolov/monadic-mondays)获得

# 第九集:类型级编程

欢迎收看#monadicmonday 第九集！今天，我想回到本系列的根本，为您提供一些关于 TypeScript 中类型级编程的“入门”知识。

让我们从基础开始。类型级编程意味着我们使用类型来表达语句，并要求编译器检查它们。在 TypeScript 中，我们使用条件类型来推断我们的语句是`never`(底层类型)还是某种有意义的类型。

第一个有用的操作是类型级的`If`操作符，可以这样表示:

```
type If<T, U, True, False> = [T] extends [U] ? True : False; 
```

Enter fullscreen mode Exit fullscreen mode

让我们为它写一些测试吧！首先，我们需要一种运行类型级断言的方法。我们可以使用微软的专门工具`dtslint`，但是它的设置有点麻烦，所以让我们继续使用简单的工具，比如`jest`。

我们从一个`assertType`函数开始，只有当它的参数不是`never` :
时，它才会被编译

```
// Type-level assertions which is possible to compile 
// only if parameter is inferred to a non-bottom type:
const assertType = <T>(expect: [T] extends [never] ? never : T): T => expect; 
```

Enter fullscreen mode Exit fullscreen mode

我们对`If`的测试将如下所示:

```
it('If<T, Eq, True, False>', () => {
  //                If<  A,       B,      T,    F  >;
  type Assertion1 = If<string, boolean, never, true>;
  expect(assertType<Assertion1>(true)).toBeTruthy();

  type Assertion2 = If<string, string, true, never>;
  expect(assertType<Assertion2>(true)).toBeTruthy();
}); 
```

Enter fullscreen mode Exit fullscreen mode

实际上，我发现自己大多使用针对`never`的检查，所以让我们定义`IfDef`类型操作符并为其编写测试:

```
type IfDef<T, True, False> = If<T, never, False, True>;

it('IfDef<T, True, False>', () => {
  type Assertion1 = IfDef<string, true, never>;
  expect(assertType<Assertion1>(true)).toBeTruthy();

  type Assertion2 = IfDef<string | never, true, never>; // a + 0 = a
  expect(assertType<Assertion2>(true)).toBeTruthy();

  type Assertion3 = IfDef<string & never, never, true>; // a * 0 = 0
  expect(assertType<Assertion3>(true)).toBeTruthy();
}); 
```

Enter fullscreen mode Exit fullscreen mode

另一个有用的类型操作是`OrElse`，如果第一个参数是`never` :
，它将返回到第二个参数

```
type OrElse<MaybeNever, Fallback> = IfDef<MaybeNever, MaybeNever, Fallback>;

it('OrElse<A, B>', () => {
  type Assertion1 = OrElse<string, true>;
  expect(assertType<Assertion1>('some string')).toEqual('some string');

  type Assertion2 = OrElse<never, true>;
  expect(assertType<Assertion2>(true)).toBeTruthy();
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们定义一些更有趣的东西。比方说，让我们检查两个类型是否相交:

```
type Intersect<A extends {}, B extends {}> =
  Pick<A, Exclude<keyof A, Exclude<keyof A, keyof B>>> extends { [x: string]: never } & { [x: number]: never } ?
  never :
  Pick<A, Exclude<keyof A, Exclude<keyof A, keyof B>>>;

it('Intersect<A, B>', () => {
  interface A { foo: string; }
  interface B { bar: number; }
  interface C { foo: string; baz: boolean; }

  type AnB = Intersect<A, B>;
  type Assertion1 = IfDef<AnB, never, true>;
  expect(assertType<Assertion1>(true)).toBeTruthy();

  type AnC = Intersect<A, C>;
  type Assertion2 = If<AnC, { foo: string }, true, never>;
  expect(assertType<Assertion2>(true)).toBeTruthy();
}); 
```

Enter fullscreen mode Exit fullscreen mode

我经常使用的另一个很棒的类型操作是`AtLeastOne`。它将一个局部类型转换为一个类型的并集，该并集包含整个集合中的一个必需字段:

```
type AtLeastOne<T, Keys extends keyof T = keyof T> = Partial<T> & { [K in Keys]: Required<Pick<T, K>> }[Keys];

it('AtLeastOne<T>', () => {
  interface T {
    foo?: string;
    bar?: number;
    baz?: boolean;
  }

  type ALOT = AtLeastOne<T>;
  type Assertion1 = If<ALOT, { foo: string } | { bar: number } | { baz: boolean }, true, never>;
  expect(assertType<Assertion1>(true)).toBeTruthy();
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们来禁止 type 被加宽:

```
type Exact<S extends {}, T extends S> = S & Record<Exclude<keyof T, keyof S>, never>;

it('Exact<S, T>', () => {
  interface A { foo: string; }
  interface B { foo: string; baz: boolean; }

  type Assertion1 = If<Exact<A, B>, A, true, never>;
  type Assertion2 = If<Exact<A, B>['baz'], never, true, never>;

  expect(assertType<Assertion1>(true)).toBeTruthy();
  expect(assertType<Assertion2>(true)).toBeTruthy();
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我将向您展示如何在您的代码中使用这些类型级操作。例如，你正在编写一个函数，要求它的参数永远不相交:

```
const neverIntersect = <
  A extends {},
  B extends {},
  // Typelevel law: A and B should not intersect
  NeverIntersect = IfDef<Intersect<A, B>, never, {}>
>(a: A & NeverIntersect, b: B & NeverIntersect): A & B & NeverIntersect => ({ ...a, ...b });

it('neverIntersect', () => {
  interface A { foo: string; }
  interface B { bar: number; }

  const a1: A = { foo: 'foo' };
  const b1: B = { bar: 42 };

  const c1 = neverIntersect<A, B>(a1, b1);
  expect(c1).toStrictEqual({ foo: 'foo', bar: 42 });
  // Won't compile with: 
  // Argument of type 'A' is not assignable to parameter of type 'never'.ts(2345)
  // const c2 = neverIntersect<A, A>(a1, a1); 
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来点类型级的乐趣吧！正如你(可能)知道的，TypeScript 的类型系统接近于图灵完备:
[【https://github.com/Microsoft/TypeScript/issues/14833】](https://github.com/Microsoft/TypeScript/issues/14833)
所以我们可以用它来玩一玩，看看它能把我们引向何方:)

由于 [TS@2.0](mailto:TS@2.0) 的推理机发生了很多变化，所以很难表达递归类型。然而，我们仍然可以，例如，描述一个类型级的列表并反转它。或者定义一个类型级别的阿砣数。让我们开始吧！

首先，我们需要一些类型级的布尔值——它们是类型级检查的基础:

```
type False = 'f';
type True = 't';
type Bool = False | True; 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们来写一个异构链表的定义，要么为空，要么由头尾组成:

```
type HNil = { isNil: True; };
// I will be using `any` for head type, because making 
// HCons generic makes code really messy and cumbersome to read:
type HCons = { isNil: False; head: any; tail: HList; };
type HList = HNil | HCons; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一些“函数”来创建和操作我们的列表:

```
type Cons<Head, Tail extends HList> = { isNil: False, head: Head, tail: Tail };
type Head<Xs extends HCons> = Xs['head'];
type Tail<Xs extends HCons> = Xs['tail'];
type IsNil<Xs extends HList> = Xs['isNil']; 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，我们终于可以写`Reverse`型了。为了反转一个列表，我们把它的头部附加到一个反转的尾部:

```
type Reverse<Xs extends HList> = Rev<Xs, HNil>;
type Rev<Xs extends HList, T extends HList> = {
  f: Xs extends HCons ? Rev<Tail<Xs>, Cons<Head<Xs>, T>> : HNil;
  t: T;
}[IsNil<Xs>];

type ABC = Cons<'a', Cons<'b', Cons<'c', HNil>>>;
type CBA = Reverse<ABC>; // => HCons<'c', HCons<'b', HCons<'a', HNil>>> 
```

Enter fullscreen mode Exit fullscreen mode

使用完全相同的技术，您可以定义类型级别的阿砣数:

```
type Zero = { isZero: True };
type Nat = Zero | { isZero: False, pred: Nat };

type Succ<N extends Nat> = { isZero: False, pred: N };
type Pred<N extends Succ<Nat>> = N['pred'];
type IsZero<N extends Nat> = N['isZero'];

type _0 = Zero;
type _1 = Succ<_0>;
type _2 = Succ<_1>;
type _3 = Succ<_2>; 
```

Enter fullscreen mode Exit fullscreen mode

甚至把这两种类型结合起来:

```
type UpTo3 = Cons<_0, Cons<_1, Cons<_2, Cons<_3, HNil>>>>;
type From3 = Reverse<UpTo3>; // => HCons<_3, HCons<_2, HCons<_1, HCons<_0, HNil>>>> 
```

Enter fullscreen mode Exit fullscreen mode

好奇的读者可能会发现熟悉的模式——例如，`Nat`的定义几乎与我们在第三集中使用的递归方案相同:[https://Twitter . com/YuriyBogomolov/status/1117723946005213185](https://twitter.com/YuriyBogomolov/status/1117723946005213185)T3】

```
// Compare:
type Zero = { isZero: True };
type Nat = Zero | { isZero: False, pred: Nat };

// From ep.3:
export class Zero<A> {
  public value: never;
  readonly _tag: 'Zero' = 'Zero';
  readonly '_A': A;
  readonly '_URI': URI;
  private constructor() { }
}

export class Succ<A> {
  readonly _tag: 'Succ' = 'Succ';
  readonly '_A': A;
  readonly '_URI': URI;
  constructor(public value: A) { }
}

export type NatF<A> = Zero<A> | Succ<A>; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在 TypeScript 中有适当的递归类型，我们就可以在类型级别定义递归方案。然而，我几乎没有看到这种可能性的日常实际应用。

最后一点，我想引用一个模块，它有一些简洁的(实际上很有用！)来自@GiulioCanti 的类型级操作:[https://github.com/gcanti/typelevel-ts](https://github.com/gcanti/typelevel-ts)

我在准备这一集时使用的另一个很好的资源是@ryotakameoka 的以下日文博客:
[https://ryota-ka.hatenablog.com/entry/2017/12/21/000000](https://ryota-ka.hatenablog.com/entry/2017/12/21/000000)

像往常一样，所有代码示例都可以在[https://github.com/YBogomolov/monadic-mondays](https://github.com/YBogomolov/monadic-mondays)获得