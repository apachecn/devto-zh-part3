# 镜头:什么和如何

> 原文：<https://dev.to/misterwhat/lenses-the-what-and-how-2g9g>

在这篇文章中，我想向你展示什么是函数式编程中的透镜，你如何使用它们，最重要的是:你如何编写你自己的透镜实现。

TL；DR
**镜头是直接可组合的存取器。请继续阅读，了解它们是如何工作的，以及如何编写自己的程序。**

我在 Runkit 上为你创建了一个小笔记本，里面包含了所有的例子和第二个替代实现。因此，您可以在任何时候(在阅读本文之前、之中或之后)使用它。看到这里:【https://runkit.com/mister-what/lenses】T2

## [T1】简介](#intro)

先说一个问题的描述。假设您有下面的数据结构，它按地点和职位列出了雇员。

```
const locations = {
  berlin: {
    employees: {
      staff: {
        list: [
          {
            name: "Wiley Moen",
            phone: "688-031-5608",
            id: "cdfa-f2ae"
          },
          {
            name: "Sydni Keebler",
            phone: "129-526-0289",
            id: "e0ec-e480"
          }
        ]
      },
      managers: {
        list: [
          {
            name: "Cecilia Wisoky",
            phone: "148-188-6725",
            id: "9ebf-5a73"
          }
        ]
      },
      students: {
        list: [
          {
            name: "Kirsten Denesik",
            phone: "938-634-9476",
            id: "c816-2234"
          }
        ]
      }
    }
  },
  paris: {
    employees: {
      staff: {
        list: [
          {
            name: "Lucius Herman",
            phone: "264-660-0107",
            id: "c2fc-55da"
          }
        ]
      },
      managers: {
        list: [
          {
            name: "Miss Rickie Smith",
            phone: "734-742-5829",
            id: "2095-69a7"
          }
        ]
      }
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

当数据结构改变时(无论什么原因)，从应用程序的不同位置访问这种结构中的数据会给你带来很多重复，并可能导致难以发现的错误。因此，让我们探索解决这个问题的另一种方法:透镜

## 镜片

镜头用于以安全和不可改变的方式访问和操作数据。对象上的访问器(getter & setters)也是如此，并不花哨，也没有什么特别的。让镜头真正强大(也真正酷)的是它们可以直接组合。这意味着什么呢？如果你在生活中上过数学课，你知道，函数可以互相组合，也就是说，你有 [![f: \ X \rightarrow Y,\ g: \ Y \rightarrow Z](img/59228488590364684eeb84a7c4621075.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RW2pnMMy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://quicklatex.com/cache3/1c/ql_abe64b3f3052b7b9d13ab6ac23d1da1c_l3.png) ，那么你可以定义 f 与 g 的组合为 [![g \circ f: \ X \rightarrow Z](img/5da0035b7aa58a3850bf938053ab9366.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fMzx7yJq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://quicklatex.com/cache3/fa/ql_4401cbde8baab9b24245f3bd54640afa_l3.png) ，除了 [![(g \circ f)(x) = g(f(x))](img/5dcc9dc8f15c8cdfbb1e9887e52a20df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d6nm-Q5P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://quicklatex.com/cache3/dd/ql_93a3bfaa93f9f30ed7059eade7abfddd_l3.png) 没有别的意思。

那么我们如何用 Javascript 表达一个组合呢？就这么简单:

```
function compose(g, f) {
    return function(x) {
        return g(f(x));
    }
}

// or with fat-arrow functions:
const compose = (g, f) => x => g(f(x)); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用三种(或更多的方式)来定义更高阶的合成:

```
// recursive version
const compose = (...fns) => x =>
  fns.length
    ? compose(...fns.slice(0, -1))(
        fns[fns.length - 1](x)
      )
    : x;

// iterative version
const composeItr = (...fns) => x => {
  const functions = Array.from(
    fns
  ).reverse();
  /* `reverse` mutates the array,
    so we make a shallow copy of the functions array */
  let result = x;
  for (const f of functions) {
    result = f(result);
  }
  return result;
};

// with Array.prototype.reduce
const composeReduce = (...fns) => x =>
  fns.reduceRight(
    (result, f) => f(result),
    x
  );

// use it!
console.log(
  compose(
    x => `Hello ${x}`,
    x => `${x}!`
  )("World")
); // -> "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

我们现在知道如何构造函数。你可能已经注意到了，当组合函数的参数和返回值是同一类型时，函数组合效果最好。

让我们为一个位置的学生定义一个组合 getter:

```
const studentsAtLocation = compose(
    (students = {}) => students.list || [],
    (employees = {}) => employees.students,
    (location = {}) => location.employees
  );

const locationWithName = locationName => (
  locations = {}
) => locations[locationName];

const getBerlinStudents = compose(
  studentsAtLocation,
  locationWithName("berlin")
);

const getParisStudents = compose(
  studentsAtLocation,
  locationWithName("paris")
);

console.log(
  getBerlinStudents(locations)
); // [ { name: 'Kirsten Denesik', ... ]

console.log(
  getParisStudents(locations)
); // [] 
```

Enter fullscreen mode Exit fullscreen mode

如果你还懂我的话，你可能已经注意到，getter 函数是以相反的顺序提供的。我们将通过使用接受一个 getter 作为参数并返回一个 getter 的函数来解决这个问题。这种模式(传递一个函数并返回一个函数)将允许我们通过传递一个接受值并返回一个 getter/setter 对的函数，基本上由 getter/setter 对组成。让我们来看看，这可能是什么样子:

```
const createComposableGetterSetter = (
  getter, // (1)
  // -- getter(targetData: TargetData): Value
  setter // (4)
  // -- setter(value: Value, targetData: TargetData) => TargetData
) => toGetterAndSetter => targetData => { // (2)
  const getterSetter = toGetterAndSetter(
    getter(targetData)
  ); // (3)
  /**
   * toGetterAndSetter is called with
   * "data" as argument
   * and returns a GetterSetter object:
   * @typedef {
   *  {
   *    get: function(): *,
   *    set: function(newData: *): GetterSetter
   *  }
   * } GetterSetter
   *
   */
  return getterSetter.set(
    setter(
      getterSetter.get(),
      targetData
    )
  ); // (5)
}; 
```

Enter fullscreen mode Exit fullscreen mode

即使这“只是”一个双线函数体，也需要一些时间来理解这里发生了什么，所以我会一步步解释:

1.  在使用 getter 和 setter 函数作为参数调用`createComposableGetterSetter`之后，我们得到 actutal `composableGetterSetter`。
2.  我们的`composableGetterSetter`将得到一个`toGetterAndSetter`函数，它将一些数据作为输入，并使用一个`get`和一个`set`方法返回一个对象。我们返回一个函数，它期望目标数据作为它唯一的参数。
3.  我们通过用来自 **(2)** 的目标数据调用 **(1)** ，并将返回值传递给`toGetterAndSetter`函数，来构造 GetterSetter 对象。
4.  我们使用 GetterSetter objects `set()`方法调用 Setter **(4)** 的返回值，该返回值带有构造的 GetterSetter 对象的值(我们调用`getterSetter.get()`来简单地检索该值)和 targetData(我们期望 setter 将返回一个新版本的`targetData`，其焦点值设置为来自`getterSetter.get()`的返回值)。
5.  我们返回从 **(5)** 中的`getterSetter.set(...)`返回的值(也是一个 GetterSetter 对象)。

### toGetterAndSetter

我们现在已经定义了我们的`createComposableGetterSetter`函数。我们仍然需要定义我们的`toGetterAndSetter`函数，我们将使用它来从目标获取数据或者在目标上设置数据。先来定义一下我们的`toSetAccessors`:

```
const toSetAccessors = data => ({
  get: () => data,
  set: newData => toSetAccessors(newData)
}); 
```

Enter fullscreen mode Exit fullscreen mode

所以 simple function 为我们构造了一个对象，只要我们想在目标对象上设置数据，就可以使用这个对象。每当用新数据调用它的`set`方法时，它将为自己创建一个保存新数据的新实例，并返回这个实例。

接下来是`toGetAccessors`功能:

```
const toGetAccessors = data => ({
  get: () => data,
  set() {
    return this;
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

GetAccessor 对象应该只允许检索其数据。当试图设置新数据时，它将简单地返回自己的实例。这使得创建后无法更改。

### 使用 ComposableGetterSetters(镜头)

我们现在将创建三个 ComposableGetterSetters 也就是透镜——来看看它们是如何工作的，以及使用它们来检索值或更改数据(以不可变的方式)需要什么。

#### 制造镜片

我们将创建一个聚焦于属性“paris”的镜头，一个聚焦于属性“employees”的镜头，第三个聚焦于属性“students”的镜头。
*我们将在 getters 中使用默认值(以避免异常)并在 setters 中使用对象传播来保持不变性。*T3】

```
const parisLens = createComposableGetterSetter(
  obj => (obj || {}).paris,
  (value, obj) => ({
    ...obj,
    paris: value
  })
);

const employeesLens = createComposableGetterSetter(
  obj => (obj || {}).employees,
  (value, obj) => ({
    ...obj,
    employees: value
  })
);

const studentsLens = createComposableGetterSetter(
  obj => (obj || {}).students,
  (value, obj) => ({
    ...obj,
    students: value
  })
); 
```

Enter fullscreen mode Exit fullscreen mode

我们注意到这里有些重复，所以让我们重构一下:

```
const lensProp = propName =>
  createComposableGetterSetter(
    obj => (obj || {})[propName],
    (value, obj) => ({
      ...obj,
      [propName]: value
    })
  );

// we can now create lenses for props like this:

const parisLens = lensProp("paris");

const employeesLens = lensProp(
  "employees"
);

const studentsLens = lensProp(
  "students"
);

const listLens = lensProp("list"); // needed to get the list of students 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以开始合成(和使用)我们的镜头了:

```
const parisStudentListLens = compose(
  parisLens,
  employeesLens,
  studentsLens,
  listLens
);

const parisStudentList = parisStudentListLens(
  toGetAccessors
)(locations).get();

console.log(parisStudentList);
// -> undefined, since there is no list of students for paris defined.

const locationsWithStudentListForParis = parisStudentListLens(
  _list => toSetAccessors([])
  // ignore current list and replace it with an empty array
)(locations).get();

console.log(
  locationsWithStudentListForParis
);// -> { ..., paris: { employees:{ ..., students: { list: [] } } } } 
```

Enter fullscreen mode Exit fullscreen mode

由于使用起来非常冗长，让我们定义一些助手:

```
const view = (lens, targetData) =>
  lens(toGetAccessors)(
    targetData
  ).get();

const over = (
  lens,
  overFn /* like the `mapf` callback in `Array.prototype.map(mapf)`.
    i.e.: You get a value and return a new value. */,
  targetData
) =>
  lens(data =>
    toSetAccessors(overFn(data))
  )(targetData).get();

const set = (lens, value, targetData) =>
  over(
    lens,
    () =>
      value /* we use `over` with a `overFn` function, 
        that just returns the value argument */,
    targetData
  ); 
```

Enter fullscreen mode Exit fullscreen mode

让我们试着使用我们的助手:

```
// using get, set, over:

const locationsWithStudentListForParis = set(
  parisStudentListLens,
  [],
  locations
);

const locationsWithOneStudentInParis = over(
  parisStudentListLens,
  (list = []) => [
    ...list,
    { name: "You", setVia: "Lens" }
  ],
  locations
);

const locationsWithTwoStudentInParis = over(
  parisStudentListLens,
  (list = []) => [
    ...list,
    { name: "Me", setVia: "Lens" }
  ],
  locationsWithOneStudentInParis
);

// logging the results:

console.log(
  view(parisStudentListLens, locations)
); // -> undefined

console.log(
  view(
    parisStudentListLens,
    locationsWithStudentListForParis
  )
); // -> []

console.log(
  view(
    parisStudentListLens,
    locationsWithTwoStudentInParis
  )
); // -> [ { name: 'You', setVia: 'Lens' }, { name: 'Me', setVia: 'Lens' } ]

console.log(
  view(
    parisStudentListLens,
    locationsWithOneStudentInParis
  )
); // -> [ { name: 'Me', setVia: 'Lens' } ]

console.log(
  locationsWithTwoStudentInParis
); // -> ... 
```

Enter fullscreen mode Exit fullscreen mode

这种方法使得升级深度嵌套的不可变数据结构变得轻而易举。为了更简单，你可以定义`lensIndex(index: number)`和`lensPath(path: Array<string|number>)`镜头创建助手。`lensIndex`然后被用来关注数组值。`lensPath`通过为您创建和预合成镜头`lensProp`和`lensIndex`镜头，创建一个聚焦于深层嵌套对象属性和数组索引的镜头。

### 镜头的更多应用领域

镜头非常适合各种值之间的转换，如货币、温度、单位(公制单位到英制单位，反之亦然)、净化用户输入、解析和字符串化 JSON 等等。

享受尝试和摆弄镜头的乐趣(不要错过查看 [Runkit 笔记本](https://runkit.com/mister-what/lenses))。如果你不明白我的一些胡言乱语，请随时提问！

我很乐意回答任何问题:)