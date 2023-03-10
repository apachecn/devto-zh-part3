# #MonadicMonday 编译:四月

> 原文：<https://dev.to/ybogomolov/monadicmonday-compilation-april-pjo>

最近，我在 Twitter 上发起了一个名为#monadicmonday 的小活动——每周一我都会发布一个帖子，介绍一些有用的、易于立即使用的 FP 内容。每个月我都会为那些喜欢读长文章的人做一个为期 4 周的汇编。

# 第 1 集:这些/Ior 单子

欢迎来到# monadicmonday 周一！让我们从一个 These/Ior 单子开始。

`These`是表示“包含-或”关系的单子:

`These(A, B) = A + B + A * B.`

这些代表了一个简单的想法:它可以保存类型 A 的值、类型 B 的值或者同时保存两者。

它可能在 FP 库中以其字面名称“These”(Haskell，TypeScript)的形式出现，或者以“Ior”的形式出现(比如在 Scala 的`cats`包中)。

看看@GiulioCanti 使用 fp-ts 包中的这些单子编写的 TypeScript 中的以下函数:

```
import { both, that, These, this_ } from 'fp-ts/lib/These';

// May warn if user is under 18, or panic if user has negative age
const greet = (name: string, age: number): These<Error, string> => {
  if (age <= 0) {
    return this_(new Error(`You age cannot be less than zero! You've entered: ${age}`));
  }

  const greeting = `Hello, ${name}!`;
  if (age < 18) {
    return both(new Error('You cannot use our service at that age!'), greeting);
  }

  return that(greeting);
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看例子。注意，我们需要为`fold`函数提供所有可能的“处理程序”,这样 monad 就可以简化为一个值。

```
// Ex. 1: Alice is 14 years old:
const howIsAlice = greet('Alice', 14);

// Ex.2: Bob is hAx0r who tries to pen-test us:
const howIsBob = greet('Bob', -42);

// Ex. 3: Charlie is just a normie:
const howIsCharlie = greet('Charlie', 25); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们把这段代码转换成一个完整的例子:

```
import { array } from 'fp-ts/lib/Array';
import { error, log, warn } from 'fp-ts/lib/Console';
import { io } from 'fp-ts/lib/IO';

array.sequence(io)([howIsAlice, howIsBob, howIsCharlie]
  .map((person) => person.fold(
    (err) => error(err.message),
    (greeting) => log(greeting),
    (err, greeting) => warn(err.message).chain(() => log(greeting)),
  ))).run(); 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们运行我们的示例！

```
You cannot use our service at that age!
Hello, Alice!
You age cannot be less than zero! You've entered: -42
Hello, Charlie! 
```

Enter fullscreen mode Exit fullscreen mode

# 第二集:戒指

欢迎收看#monadicmonday 第二集！今天我们将讨论代数数据类型，更具体地说，是关于半环和环。

首先，让我们回顾一下什么是半环。半环是配备了两种二元运算的集合，称为“加法”和“乘法”。这里的直觉是自然数——您可以用加法“+”和乘法“*”来定义 Nat 的半环

一个环用一个附加的“逆”运算扩展了一个半环(或者，严格地说，环意味着对于每个‘a’存在一个’-a’)。环和半环也必须有“零”和“一”元素，乘法应该分布在加法之上。

另一个重要特征是环和半环在乘法下形成幺半群。
概述:幺半群是用两种运算来定义的:

*   combine :: A×A → A，将两个幺半群合并在一起，
*   empty :: A，为“一”定义了一个实体。

让我们为 Option (Maybe)数据类型定义一个 ring 实例。我们可以用几种可能的方式来定义它，这取决于我们如何对待“无”值。我将这些实例称为“安全环”和“严格环”。

```
import { none, Option, some } from 'fp-ts/lib/Option';
import { getTupleRing, Ring } from 'fp-ts/lib/Ring';

const safeOptionRing: Ring<Option<number>> = {
  zero: none,
  one: some(1),
  add: (x, y) => x.fold(y, (_x) => y.fold(x, (_y) => some(_x + _y))),
  sub: (x, y) => x.fold(y, (_x) => y.fold(x, (_y) => some(_x - _y))),
  mul: (x, y) => x.fold(y, (_x) => y.fold(x, (_y) => some(_x * _y))),
};

const strictOptionRing: Ring<Option<number>> = {
  zero: none,
  one: some(1),
  add: (x, y) => x.fold(none, (_x) => y.fold(none, (_y) => some(_x + _y))),
  sub: (x, y) => x.fold(none, (_x) => y.fold(none, (_y) => some(_x - _y))),
  mul: (x, y) => x.fold(none, (_x) => y.fold(none, (_y) => some(_x * _y))),
}; 
```

Enter fullscreen mode Exit fullscreen mode

给定几个不同的选项实例，我们可以观察我们的环的不同行为:

```
const a: Option<number> = some(42);
const b: Option<number> = none;
const c: Option<number> = some(21);

// safe only:
const R1 = getTupleRing(safeOptionRing, safeOptionRing, safeOptionRing);

console.log(R1.add([a, b, c], [b, a, c])); // => [ some(42), some(42), some(42) ]
console.log(R1.add([a, b, c], R1.one)); // => [ some(43), some(1), some(22) ]
console.log(R1.sub([a, b, c], R1.zero)); // => [ some(42), none, some(21) ]
console.log(R1.mul([a, b, c], [a, b, c])); // => [ some(1764), none, some(441) ] 
```

Enter fullscreen mode Exit fullscreen mode

严戒:

```
// strict only:
const R2 = getTupleRing(strictOptionRing, strictOptionRing, strictOptionRing);

console.log(R2.add([a, b, c], [b, a, c])); // => [ none, none, some(42) ]
console.log(R2.add([a, b, c], R2.one)); // => [ some(43), none, some(22) ]
console.log(R2.sub([a, b, c], R2.zero)); // => [ none, none, none ]
console.log(R2.mul([a, b, c], [a, b, c])); // => [ some(1764), none, some(441) ] 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以混合搭配一下:

```
// mix&match:
const R3 = getTupleRing(strictOptionRing, safeOptionRing, strictOptionRing);

console.log(R3.add([a, b, c], [b, a, c])); // => [ none, some(42), some(42) ]
console.log(R3.add([a, b, c], R3.one)); // => [ some(43), some(1), some(22) ]
console.log(R3.sub([a, b, c], R3.zero)); // => [ none, none, none ]
console.log(R3.mul([a, b, c], [a, b, c])); // => [ some(1764), none, some(441) ] 
```

Enter fullscreen mode Exit fullscreen mode

如果您可以将一个结构识别为(半)环，您就可以放心地使用它的属性。例如:

*   语言集合形成半环，乘积作为串并，加法作为集合并；
*   布尔逻辑自然形成半环:(布尔，&&，||，真，假)。

有趣的事实:半环有时被称为“rig ”,因为没有负数的环就是一个“rig”

> 半环有时被定义为+上的幺半群和*上的半群，即 0 是强制的，而 1 不是。那么，Rig 只加 1，Rng 只加否定，Ring 两者都有。
> 
> via [λoλcat](https://twitter.com/katzenstrophe)

感谢@LukaJacobowitz 的精彩文章《半环的故事》，推荐阅读:[https://lukajcb . github . io/blog/functional/2018/11/02/A-Tale-of-semings . html](https://lukajcb.github.io/blog/functional/2018/11/02/a-tale-of-semirings.html)

# 第三集:递归方案

这一集我们将讨论非常复杂的话题——递归方案。抓紧了，因为旅途并不容易。

首先，我想强调的是，我仍然在探索这个话题，所以如果你发现了一个错误，请告诉我！

Mejer、Fokkinga 和 Petterson 的经典论文《用香蕉、透镜、信封和铁丝网进行函数式编程》推广了递归方案:[https://eprints . EEM cs . ut wente . nl/7281/01/d b-ut wente-40501 f46 . pdf](https://eprints.eemcs.utwente.nl/7281/01/db-utwente-40501F46.pdf)

递归方案是概括各种递归算法并使它们可组合和易于推理的强大方法。递归模式对于 FP 的重要性与面向对象的设计模式的重要性是一样的。我要感谢@importantshock 在博客“不确定性中的冒险”上所做的出色工作，这是我所知道的最彻底和详细的递归方案介绍:[https://blog.sumtypeofway.com](https://blog.sumtypeofway.com)

举几个例子，有太多的递归方案:

*   变形一层一层地分解一个结构——你可以把它想象成一个一般的`fold`操作；
*   变形一层一层地建立(递归)结构——你可能知道它的名字是`unfold`；
*   仿形和同形折叠和展开一个结构，同时保留原始结构的信息。这是通过传递递归调用来实现的，不仅传递计算值本身，还传递原始项；
*   histo-和 futumorphisms 是双态，它给你一个访问以前(histo-)和将来(futu-)递归计算结果的入口。它们对于解决动态编程问题非常有用，我们将在后面看到；
*   还有更多的:chrono-、dyna-、prepro-、postpro-等等，甚至是全能的左右对称前形态，这已经成为函数式程序员社区的半个笑话。

像往常一样，我将用 Haskell-esque 类型定义和带有 fp-ts 库的 TypeScript 来说明我的例子。
我们来解决一个问题:给定一个期望量 N，和一个无限供给 S = { S1，S2，..，Sm}的硬币，我们有多少种方法可以进行改变？
顺便说一句，这个问题在各种教程博客(像(链接:【https://www.geeksforgeeks.org/coin-change-dp-7/)geeksforgeeks . org/coin-change-DP……)中有非常详细的描述，所以我在这里就不描述解决方法了。相反，我将向您展示如何使用组织形态来实现它。

让我们回忆一下组织形态类型:

`histo :: Functor f => CVAlgebra f a -> Fix f -> a,`

在哪里

`CVAlgebra f a = f (Cofree f a) -> a` -一个价值过程代数，

`Cofree f a = Cofree { attribute :: a, hole :: f (Cofree f a) }` - cofree comonad

和

`Fix f = f (Fix f)` -函子 f 的不动点

嗯，我想这些部分需要一些额外的解释。价值过程代数允许我们访问当前递归项的额外负载(属性),以及更深地陷入它的漏洞。

Cofree comonad 是另一个一元星期一的好主题，所以今天我只想说这是一种用类型`a`的附加标记来“标记”递归项的方法。

Fix 是类型级的 Y 组合子(fixpoint combinator ),所以它只是表示一个无限序列的类型应用:

```
type Fix t = t (t (t (t (t ...)))) 
```

Enter fullscreen mode Exit fullscreen mode

例如，单链表可以这样描述:

```
type List a = Fix (L a)
data L a b = Nil | Cons a b 
```

Enter fullscreen mode Exit fullscreen mode

> 你可以认为列表是一个数学同构[https://twitter.com/xgrommx/status/880560506385252353](https://twitter.com/xgrommx/status/880560506385252353)
> 1)通过抽象理解(建立一个新的抽象)
> 2)数学同构
> 3)范畴理论(F-代数的范畴)
> 
> 丹尼斯·斯托扬诺夫

为了开始解决硬币兑换问题，让我们在 TypeScript 中定义我们的辅助类型。我们将需要:

*   `Fix`类型；
*   `Nat`键入代表阿砣的数字；
*   `Cofree`类型为我们的 comonad
*   Cofree 的实例`NatC`；
*   `CVAlgebra`；
*   以及用于模式匹配的`match`:

```
import { Lazy } from 'fp-ts/lib/function';
import { HKT, Type, URIS } from 'fp-ts/lib/HKT';

export class Fix<F> {
  constructor(public readonly value: HKT<F, Fix<F>>) { }
}

export function fix<F>(value: HKT<F, Fix<F>>): Fix<F> {
  return new Fix(value);
}

export function unfix<F>(term: Fix<F>): HKT<F, Fix<F>> {
  return term.value;
}

export class Cofree<F, A> {
  constructor(readonly attribute: A, readonly hole: HKT<F, Cofree<F, A>>) { }
}

export const cofree = <F, A>(a: A, h: HKT<F, Cofree<F, A>>) => new Cofree(a, h);
export const attribute = <F, A>(c: Cofree<F, A>): A => c.attribute;

export function hole<F extends URIS, A>(c: Cofree<F, A>): Type<F, Cofree<F, A>>;
export function hole<F, A>(c: Cofree<F, A>): HKT<F, Cofree<F, A>> { return c.hole; }

export type Algebra<F, A> = (fa: HKT<F, A>) => A;
export type CVAlgebra<F, A> = (fa: HKT<F, Cofree<F, A>>) => A;

export const match = <T extends string>(toMatch: T) => <B>(ops: Record<T, Lazy<B>>) => ops[toMatch](); 
```

Enter fullscreen mode Exit fullscreen mode

与简洁的 Haskell 对应物
相比，`Nat`类型的定义需要 TypeScript 中的一些样板文件

```
data Nat a = Zero | Succ a
  deriving Functor 
```

Enter fullscreen mode Exit fullscreen mode

```
import { Functor1 } from 'fp-ts/lib/Functor';

import { Fix, fix } from './Fix';

declare module 'fp-ts/lib/HKT' {
  interface URI2HKT<A> {
    Nat: NatF<A>;
  }
}

export const URI = 'Nat';
export type URI = typeof URI;

export class Zero<A> {
  static value = new Zero<any>();
  public value: never;
  readonly _tag: 'Zero' = 'Zero';
  readonly '_A': A;
  readonly '_URI': URI;
  private constructor() { }
  map<B>(_f: (a: A) => B): NatF<B> {
    return this as any;
  }
}

export class Succ<A> {
  readonly _tag: 'Succ' = 'Succ';
  readonly '_A': A;
  readonly '_URI': URI;
  constructor(public value: A) { }
  map<B>(f: (a: A) => B): NatF<B> {
    return new Succ(f(this.value));
  }
}

export type NatF<A> = Zero<A> | Succ<A>;
export type Nat = Fix<URI>;

export const zero = fix(Zero.value);
export const succ = (n: Nat): Nat => fix(new Succ(n));

export const functorNat: Functor1<URI> = {
  URI,
  map<A, B>(nat: NatF<A>, f: (a: A) => B): NatF<B> {
    return nat.map(f);
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

`histo`本身的实现与来自[https://blog . sumtypeofway . com/recursion-schemes-part-iv-time-is-the-essence/:](https://blog.sumtypeofway.com/recursion-schemes-part-iv-time-is-of-the-essence/:)
的 Haskell 原始实现完全相同

```
export function histo<F extends URIS>(F: Functor1<F>): <A>(h: (fa: Type<F, Cofree<F, A>>) => A) => (term: Fix<F>) => A;
export function histo<F>(F: Functor<F>): <A>(h: CVAlgebra<F, A>) => (term: Fix<F>) => A {
  return <A>(h: CVAlgebra<F, A>) => {
    return function self(term): A {
      const worker = (t: Fix<F>): Cofree<F, A> => {
        const calc = F.map(unfix(t), worker);
        return cofree(h(calc), calc);
      };
      return attribute(worker(term));
    };
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

从解决方案开始，我们需要定义两个助手，它们将一个普通的 JavaScript `number`转换成`Nat`，反之亦然:

```
// NatC – cofree wrapper around our target solution type - list of "ways of change",
// i.e. all possible combinations of change for the given amount:
export type NatC = Cofree<URI, number[][]>;

// Convert plain JavaScript `number` into Peano number:
const expand = (amt: number): Nat => amt === 0 ? zero : succ(expand(amt - 1));

// Convert Peano number back to `number`:
const compress = (n: NatF<NatC>): number => match(n._tag)({
  Zero: constant(0),
  Succ: () => 1 + compress(hole(n.value)),
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们来定义一下`change`函数，它与他文章中描述的 one @importantshock 是不同的。我的实现存储的不仅仅是给定金额可以交换的多种方式——这些信息对于正确的解决方案来说是不够的。相反，我存储每个数量的精确解。这给了我额外的好处:我不仅可以打印许多路，还可以打印路本身！这就是为什么我的`Cofree`的`NatC`实例保存的不仅仅是一个数字，而是一个数字数组的数组。

```
const lookup = <A>(cache: Cofree<URI, A>, n: number): A =>
  n === 0 ? attribute(cache) : lookup(hole(cache).value, n - 1);

const COINS = [1, 5, 10, 25, 50];

const change = (amt: number): number[][] => {
  const go = (curr: NatF<NatC>) => match(curr._tag)({
    Zero: constant([[]]),
    Succ: () => {
      const given: number = compress(curr);
      const validCoins = COINS.filter((c) => c <= given);
      // This is what differs: we store not only the sum of ways, but the exact solution itself:
      const remaining: Array<[number, number]> = validCoins.map((c) => ([c, given - c]));
      const { right: zeroes, left: toProcess } = partition(remaining, (a) => a[1] === 0);
      const results: number[][][] = toProcess.map(
        ([coin, remainder]) => lookup(curr.value, given - 1 - remainder).reduce<number[][]>(
          (arr, coins) => (coins.every((c) => c <= coin) && arr.push([coin, ...coins]), arr),
          [],
        ),
      );
      return zeroes.map(([c]) => [c]).concat(...results);
    },
  });

  return histo(functorNat)(go)(expand(amt));
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是运行金额为 15、20 和 42 美分的`change`的结果。我们不仅得到有效的和，而且得到所有可能的变化方式:

[![](img/ac25e0442a67a39f5ef06fa0cfc8e355.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K29bSYjn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/turfo71q1he0ehf45cbu.png)

# 第四集:非此即彼。

在这一集里，我们将谈论任一单子叶植物的特殊变体。

我想展示的第一个是来自[@ dev experts](https://dev.to/devexperts):【https://github.com/devex-web-frontend/remote-data-ts】T2 的令人敬畏的远程数据

这个 monad 封装了网络请求的四种状态:

*   请求尚未开始；
*   请求正在运行；
*   请求失败；
*   请求成功。

这允许开发者总是处理网络请求的错误和未决状态——很明显，这在 web 开发人员中很常见。

使用 RemoteData，我通常以如下方式构建我的 Redux 存储:

*   任何来自网络的数据都被包装在 RD 中；
*   在 redux-saga I `yield`中，作为第一个操作挂起，然后——当请求完成时，要么失败，要么成功；
*   在 reducers 中，我只是按原样传递 RD，没有任何折叠；
*   在我的组件中，我尽可能晚地折叠 RD——通常是在我需要将它的数据呈现为 JSX 的时候。

它看起来像这样:

佐贺:

```
function* getDataSaga() {
  yield put({ type: 'GET_DATA_START', payload: pending });

  const result: RemoteData<Error, Data> = yield call(API().getData());

  yield put({ type: 'GET_DATA_FINISH', payload: result });
} 
```

Enter fullscreen mode Exit fullscreen mode

减速器:

```
const getDataReducer =
  (state = initial, { type, payload }: AnyActionWithPayload<Data>) => {
    switch (type) {
      case 'GET_DATA_START':
      case 'GET_DATA_FINISH': {
        return payload;
      }
      default: {
        return state;
      }
    }
  }; 
```

Enter fullscreen mode Exit fullscreen mode

组件

```
const mapDispatchToProps = (dispatch: Dispatch<ApplicationState>) => ({
  load: dispatch({ type: 'GET_DATA_INIT' }),
});

const MyComponent: React.FunctionComponent<MyComponentProps> = ({ dataRD, load }) => {
  const doLoad = () => {
    load();
  };

  return (
    <>
      {dataRD.foldL(
        () => <Button onClick={doLoad}>Load!</Button>,
        () => <CircularProgress variant='indeterminate' />,
        (error) => <Typography variant='h1'>{error.message}</Typography>,
        (data) => <User model={data.user} />,
      )}
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

RemoteData 的一个积极结果是在代码之外。我的合作开发人员“被迫”考虑挂起(=加载)和失败(=错误)，所以他们要求设计人员提供所有情况下系统行为的解释，这样系统就永远不会处于不一致的状态。

我想展示的另一个有用的 ADT 是验证。它看起来和行为都很相似，只有一点不同:它不是一个单子，而是一个应用函子。我推荐阅读@GiulioCanti 的精彩文章:[https://dev . to/gcanti/getting-started-with-FP-ts-any-vs-validation-5eja](https://dev.to/gcanti/getting-started-with-fp-ts-either-vs-validation-5eja)

另一个有用的单子`These`，在第一集描述过:[https://Twitter . com/YuriyBogomolov/status/1112712151062458370](https://twitter.com/YuriyBogomolov/status/1112712151062458370)。它不是 RemoteData 之类的任何一个的扩展，而是承载了类似的业务语义。

## 结论

这些是四月一月星期一的主题。请让我知道你对它的看法！我很乐意得到你的反馈，所以请回复我，电报回复 [@ybogomolov](https://t.me/ybogomolov) ，推特回复 [@YuriyBogomolov](https://twitter.com/YuriyBogomolov) ，或者通过【yuriy.bogomolov@gmail.com】回复。