# 老问题，新解决方案

> 原文：<https://dev.to/whiteand/old-problem-new-solution-39jh>

[![](img/0c7d19557275c9f76de24509a5cc2d4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xoio4JDg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w08v5u0obkl463sruief.jpg)

# 前端数据验证练习

> 如果你想知道如何在 Vue 中验证表单，这篇文章不适合你。你应该使用一些标准的 Vue 插件，比如 [vuelidate](https://monterail.github.io/vuelidate/)
> 
> 我的建议是:验证模式必须放在提交表单的组件中。

我们经常创建依赖于第三方数据的软件。API 调用、后端、父组件，...)，您需要准备好您获得的数据可以具有任何形状和内容。所以我们需要验证从其他地方获取的数据。

* * *

## 目录

*   [解决方案要求](#solution-requirements)
*   [解决方案](#solution)
*   [类型验证](#validation-of-types)
    *   [数字](#validation-of-numbers)
    *   [琴弦](#validation-of-strings)
    *   [其他类型](#validation-of-other-types)
    *   [替代品](#alternatives)
*   [自定义验证规则](#custom-validation-rules)
*   [深度验证](#deep-validation)
    *   [对象的深度验证](#deep-validation-of-object)
    *   [数组的深度验证](#deep-validation-of-array)
*   [修复无效数据](#fixing-of-invalid-data)
*   [跟踪](#tracking)
    *   [消息](#messages)
    *   [错误](#errors)
*   [其他可能性](#additional-possibilities)
*   [其他解决方案](#other-solutions)
*   [联系人](#contacts)

* * *

## 解决方案要求

几乎所有解决方案都有或多或少有用的解决方案。针对我们的问题，我们设定了要实现的目标:

*   类型验证(数字、对象、数组、字符串、空、未定义、...)
*   自定义验证规则；
*   深度验证；
*   无效数据的修复:
    *   设置默认值；
    *   省略无效。
*   跟踪:
    *   消息，
    *   错误；
*   清除代码
    *   易读的
    *   可更改的

* * *

## 解

作为我们可以用来实现这个目标的解决方案之一是 [`quartet`](https://www.npmjs.com/package/quartet) 库。

基于此验证定义的这些库:

> “验证”是证明某些数据可以接受使用。

从定义中我们看到，验证只有两种可能的结果:“数据可接受”和“数据不可接受”。在 javascript 中，我们以这样的方式表示这个值:

| 结果 | JS 值 |
| --- | --- |
| 数据可以接受 | `true` |
| 数据不可接受 | `false` |

让我们看看如何使用`quartet`来实现上述目标。

* * *

## 验证的类型

对于测试类型，我们可以使用默认的注册验证器和自定义函数。

```
// Import library
import quartet from 'quartet'
const v = quartet() 
```

Enter fullscreen mode Exit fullscreen mode

`v` -是一个将模式转换为验证函数的函数。这需要两个论据

1.  验证模式(必需)
2.  自定义错误(可选)

> **验证模式**是以下之一:
> 
> *   验证函数(返回`true`或`false`的函数)
> *   [注册验证函数的名称](https://github.com/whiteand/quartet#default-registered-validators)
> *   验证替代方案的模式数组。
> *   类似`{ key1: schemaForKey1, key2: schemaForKey2, ... }`的物体
> 
> **自定义错误**是任意 javascript 值(除了`undefined`)或者返回任意 javascript 值的函数。该值将被视为架构验证错误的解释。并将存储在`v.explanation`中。用法示例参见[跟踪](#tracking)部分。

* * *

### 数字的验证

```
const isNumber = v('number') // returns typeof value === 'number'
isNumber(0)         // true
isNumber(-1)        // true
isNumber(1)         // true
isNumber(1.2)       // true
isNumber(NaN)       // true
isNumber(Infinity)  // true
isNumber(-Infinity) // true

isNumber('1')             // false
isNumber(new Number(123)) // false 
```

Enter fullscreen mode Exit fullscreen mode

有限数的检查(无 NaN，无穷大，-无穷大)

```
// Lets put all values into array
// and find all values that are finite numbers
const numberLikeValues = [0, -1, 1, 1.2, NaN, Infinity, -Infinity, '1', new Number(123)]

// v('filter') is the same function as: value => Number.isFinite(value))
numberLikeValues.filter(v('finite')) // [0, -1, 1, 1.2] 
```

Enter fullscreen mode Exit fullscreen mode

整数的检查

```
// v('safe-integer') is the same function as: value => Number.isSafeInteger(value))
numberLikeValues.filter(v('safe-integer')) // [0, -1, 1] 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以检查数字符号:

```
 // v('positive') is the same function as: x => x > 0
numberLikeValues.filter(v.and('positive', 'finite')) // [1, 1.2] 
```

Enter fullscreen mode Exit fullscreen mode

> `v.and(schema, schema2, schema3, ...)`表示验证值必须与`schema`**`schema2`**`schema3`匹配，以此类推。****

```
// v('negative') is the same function as: x => x < 0
numberLikeValues.filter(v.and('negative', 'number')) // [-1, -Infinity]

// v('negative') is the same function as: x => x < 0
numberLikeValues.filter(v.and('non-positive', 'finite')) // [0, -1]
numberLikeValues.filter(v.and('non-negative', 'safe-integer')) // [0, 1] 
```

Enter fullscreen mode Exit fullscreen mode

还有一些返回数字验证函数的方法:

*   `v.min(minValue)`；
*   `v.max(maxValue)`；
*   `v.enum(value, value2, ...)`检查验证值是否是通过的值之一。

让我们用它们来测试评级值:

```
// v.min(minValue) for numbers is the same function as: x => x >= minValue
// v.max(minValue) for numbers is the same function as: x => x <= maxValue
const isRating = v.and('safe-integer', v.min(1), v.max(5))

isRating(1) // true
isRating(5) // true

isRating('2') // false
isRating(0) // false
isRating(6) // false 
```

Enter fullscreen mode Exit fullscreen mode

相同，但使用了`v.enum`和

```
// v.enum(...values) is the same function as: x => values.includes(x)
const isRating2 = v.enum(1,2,3,4,5)

isRating2(1) // true
isRating2(5) // true

isRating2('2') // false
isRating2(0) // false
isRating2(6) // false 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 字符串的验证

```
const stringLikeObjects = [
  '',
  '123',
  new String('123'),
  Number('string')
]

// lets find only strings
stringLikeObjects.filter(v('string')) // ['', '123'] 
```

Enter fullscreen mode Exit fullscreen mode

和数字一样，字符串也有额外的注册验证器:`'not-empty'` :

```
stringLikeObjects.filter(v.and('not-empty', 'string')) // ['123'] 
```

Enter fullscreen mode Exit fullscreen mode

还有一些创建字符串验证函数的方法:

*   v . regex(regular expression:RegExp)；
*   v . min(minLength:number)；
*   v.max(minLength: number)。

让我们用它们来检查密码(仅限愚蠢的密码)

```
const v = require('quartet')()

const isValidPassword = v.and(
  'string',                   // typeof x === 'string'
  v.min(8),                   // length >= 8
  v.max(24),                  // length <= 24
  v.regex(/^[a-zA-Z0-9]+$/),  // must contain only letters and digits
  v.regex(/[a-z]/),           // at least one small letter
  v.regex(/[A-Z]/),           // at least one big letter
  v.regex(/[0-9]/)            // at least one digit
)
console.log(isValidPassword('12345678'))         // false
console.log(isValidPassword('12345678Password')) // true 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 其他类型的验证

您可以在验证模式中使用下一个注册的验证函数来检查类型。

| 名字 | 情况 |
| --- | --- |
| 布尔型 | `x => typeof x === 'boolean'` |
| '空' | `x => x === null` |
| '未定义' | `x => x === undefined` |
| '零' | `x => x === null |
| '对象' | {% raw %} `x => typeof x === 'object'` |
| 反对！” | `x => typeof x === 'object' && x !== null` |
| '数组' | `x => Array.isArray(x)` |
| '符号' | `x => typeof x === 'symbol'` |
| '功能' | `x => typeof x === 'function'` |

* * *

## 替代品

有时需要验证不同类型的数据。

您可以使用替代方案的模式来获得这样的行为:

```
// It is works exactly as OR operator in JS,
// if some of alternatives - true, it will return true immediately
v(['number', 'string'])(1) // true
v(['number', 'string'])('1') // true

v(['number', 'string'])(null) // false
v(['number', 'string'])(new String(123)) // false

v(['number', 'string', 'object'])(null) // true
v(['number', 'string', 'object'])(new String(123)) // true 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 自定义验证规则

如前所述:验证函数是
有效模式之一。如果你想添加你自己的规则，你只需要使用你的验证函数作为模式。

```
const isPrime = n => {
  if (n < 2) return false
  if (n === 2 || n === 3) return true
  if (n % 2 === 0 || n % 3 === 0) return false
  for (let i = 5, j = 7; i * i <= n; i+=6, j+=6) {
    if (n % i === 0) return false
    if (n % j === 0) return false
  }
  return true
}
const isPrimeAndNotLessThan100 = v.and(
  'safe-integer',
  v.min(100),
  isPrime // validation function
)
isPrimeAndNotLessThan100(512) // false, 512 is NOT a prime number
isPrimeAndNotLessThan100(523) // true, 523 > 100, 523 is a prime number 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 深度验证

> 深度验证意味着对非原始数据结构的验证。只有当数据结构具有正确的类型并且它的所有部分都有效时，它才能被接受。

最流行的数据结构是对象和数组。

* * *

### 对象的深度验证

对于对象`quartet`的验证，使用对象模式。

> **对象模式**是这种结构的对象
> 
> ```
>  {
>   key1: schema1,
>   key2: schema2,
>   // ...
>   // And if you need to validate other properties of object
>   // You can use v.rest(schema) method (it returns an object that must be spreaded into object schema)
>  ...v.rest(schemaAppliedToOtherValues)
> } 
> ```

*举例:*

```
// `v` treats object as an object
const isWorkerValid = v({
  name: v.and('not-empty', 'string'),
  age: v.and('positive', 'safe-integer)',
  position: v.enum(
    'Frontend Developer',
    'Backend Developer',
    'QA',
    'Project manager',
    'Grandpa'
  ),
  salary: v.and('positive', 'finite'),
  project: v.enum(
    'Shoutout',
    'FMEvents',
    'Jobla.co'
  ),
  // Any field can be object too
  skills: {
    JS: 'boolean',
    HTML: 'boolean',
    CSS: 'boolean',
    ...v.rest('boolean') // other keys must be boolean too
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

让我们用这个验证函数
来验证一些对象

```
const worker = {
  name: 'Max',
  age: 31,
  position: 'Grandpa',
  salary: Math.random() * 3000,
  project: 'Jobla.co',
  skills: {
    JS: true,
    HTML: true,
    CSS: true,
    'C++ advanced': false,
    'GPU programming': false
  }
}
isWorkerValid(worker) // true 
```

Enter fullscreen mode Exit fullscreen mode

**字典对象**的验证还有其他方法:

*   `v.dictionaryOf(schema)` -检查对象的值；
*   `v.keys(schema)` -检查对象的关键点；
*   `v.rest(schema)` -如果存在其他属性-将使用模式对其进行验证。

*示例:字典对象*
的验证

```
 const lowLettersDict = {
  A: 'a',
  B: 'b',
  C: 'c'
}
const isValidLettersDict = v.and(
  v.keys(v.regex(/^[A-Z]$/)),
  v.dictionaryOf(v.regex(/^[a-z]$/))
)
console.log(isValidLettersDict(lowLettersDict)) 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用
自定义验证函数
检查键是否与值对应

```
// second parameter of all validation function is
// {
//   key: string|number,
//   parent: any
// }
// (if the parent is present)
function isValueValid (value, { key }) {
  return /^[A-Z]$/.test(key)        // upperCased key
    && /^[a-z]$/.test(value)        // lowerCased value
    && value === key.toLowerCase()  // correspond each other
}

const isValidLettersDict2 = v.dictionaryOf(isValueValid)

console.log(isValidLettersDict2(lowLettersDict)) // true
console.log(isValidLettersDict2({ A: 'b' })) // false, NOT CORRESPONDS
console.log(isValidLettersDict2({ b: 'b' })) // false, b is not UpperCased
console.log(isValidLettersDict2({ B: 'B' })) // false, B is not LowerCased 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 深度验证的数组

对于数组的深度验证，我们可以使用`v.arrayOf(schema)`方法。

```
const arr = [1,2,3,4]
const invalidArrOfNumbers = [1,2,'3','4']

const isArrayValid = v.arrayOf('number')

isArrayValid(arr) // true
isArrayValid(invalidArrOfNumbers) // false 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们可以将数组验证模式与对象模式结合起来

```
const isValidPointArray = v.arrayOf({
  x: 'finite',
  y: 'finite'
})
isValidPointArray([
  { x: 1, y: 2},
  { x: -1, y: 3},
  { x: 0, y: 0},
]) // true 
```

Enter fullscreen mode Exit fullscreen mode

另一种方式:具有数组属性的对象:

```
const student = {
  name: 'Valera',
  grades: ['A', 'B', 'C','A', 'D', 'F']
}
const isStudentValid = v({
  name: 'string',
  grades: v.arrayOf(v.enum('A', 'B', 'C', 'D', 'E', 'F'))
})

isStudentValid(student) // true 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 无效数据的修复:

如果我们可以修复一些验证错误。例如，我们可以用空的有效数据替换无效数据。此外，有时我们可以忽略无效数据。或者在稀有键中——我们应该尝试将无效数据转换为有效数据。

在`quartet`中，有完成此类任务的方法。主要方法是

*   `v.fix(invalidValue) => validValue`

此方法用于应用验证期间收集的所有修复。它不会改变`invalidValue`，但会返回应用了修复的新值。

方法`v.default(schema, defaultValue)`、`v.filter(schema)`和`v.addFix(schema, fixFunction)`是验证器的装饰器。这意味着它们返回新的验证函数，该函数与传递的模式完全一样，但是有收集修复的副作用。

| 装饰者 | 修正效果，调用`v.fix`后 |
| --- | --- |
| `v.default` | 用默认值替换值 |
| `v.filter` | 从父项中删除值 |
| `v.addFix` | 自定义 fixFunction 改变值的父项
,以修复错误 |

*举例:*

让我们创建几个具有不同效果的验证函数。

```
const arr = [1,2,3,4,'5','6','7']

// Replaces all not numbers with 0
const isArrayValid = v.arrayOf(
  v.default('number', 0)
)

// Removes all not numbers from parent(array)
const isArrayValidFilter = v.arrayOf(
  v.filter('number')
)

// This function will be called on value in the clone of invalid data
// So this mutations - are safe.
function castToNumber(invalidValue, { key, parent }) {
  parent[key] = Number(invalidValue)
}

// casts all not numbers into numbers
const isArrayValidFix = v.arrayOf(
  v.addFix('number', castToNumber)
) 
```

Enter fullscreen mode Exit fullscreen mode

让我们用它们来验证`arr` :

```
v.clearContext() // remove all fixes stored in `v`
isArrayValid(arr) // false
const validArr = v.fix(arr)
console.log(validArr) // [1,2,3,4,0,0,0]

v.clearContext() // remove previous fixes
isArrayValidFilter(arr) // false
const validArr2 = v.fix(arr) // [1,2,3,4]

v() // same as v.clearContext()
isArrayValidFix(arr) // false
const validArr3 = v.fix(arr) // [1,2,3,4,5,6,7]

// arr is not mutated
console.log(arr) // [1,2,3,4,'5','6','7'] 
```

Enter fullscreen mode Exit fullscreen mode

> **注意:**如果父级上有“固定效果”，则不会应用子级的“固定效果”(如果父级上的固定效果不是“过滤效果”)。
> 
> 这意味着我们应该使用这样的规则:如果有“修复效应”，它必须修复它必须修复的值的所有无效。
> *例如:*

```
const isObjectValid = v({
  arr: v.default( // will be applied
    v.arrayOf(
      v.filter('number') // will not be applied
    ),
    [] // if there will be any not number - all array will be replaced with []
  )
})
const invalidObj = {
  arr: [1,2,3,'4']
}
v()
isObjectValid(invalidObj)
const validObj = v.fix(invalidObj) // { arr: [] } 
```

Enter fullscreen mode Exit fullscreen mode

> 还有一个`v.hasFixes`方法:它返回`true` -如果已经收集了一些修复，并准备好应用。否则返回`false`。

* * *

## 追踪

有时，我们不仅需要检查一个值是否无效，还需要得到一个解释，并可能将这个解释发送给用户，或者日志记录器等。

在`quartet`中，我们用解释来解释它。

> **解释** -你想要的描述无效错误的任何 JS 值(未定义的除外)。

我们使用第二个参数`v`来增加存储解释的效果，它可以是:

*   解释；
*   返回解释的函数。

我们使用它们将错误信息和错误收集到`v.explanation`数组中。

* * *

### 消息

有时我们只需要向用户展示数据。和字符串解释的错误是非常有用的。

*举例:*

```
const isValidPerson = v.and(
  v('object!', 'Person data structure is not an object'),
  {
    name: v.and(
      // required, checks if parent has such property
      v('required', 'name field is absent'), 
      v('string', 'Person name is not a string'),
      v('not-empty', 'Person with empty name, really?')
    ),
    age: v.and(
      v('required', 'age field is absent'),
      v('safe-integer', 'Person age is not an integer number'),
      v(v.min(18), 'Person has is not an appropriate age'),
      v(v.max(140), `It was just a healthy food`)
    )
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

让我们用这个模式来验证几个人

```
v.clearContext() // or v()
isValidPerson(null) // false
console.log(v.explanation) // ['Person data structure is not an object']

v.clearContext()
isValidPerson({}) // false
console.log(v.explanation)
/*
* [
* 'Name field is absent',
* 'age field is absent'
* ]
*/
v() // same as v.clearContext()
isValidPerson({ name: '', age: 969 })
console.log(v.explanation)
/**
* [
*   'Person with empty name, really?',
*   'It was just a healthy food'
* ]
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们可以根据 invalidValue 及其父代来计算解释。

*举例:*

```
const isValidPerson = v.and(
  v('object!', 'Person data structure is not an object'),
  {
    name: v.and(
      v('required', 'name field is absent'),
      v('string', 'Person name is not a string'),
      v('not-empty', 'Person with empty name, really?')
    ),
    age: v.and(
      v('required', 'age field is absent'),
      v('safe-integer', 'Person age is not an integer number'),
      v(v.min(18), age => `Your age: ${age} is to small`),
      v(v.max(140), age => `Your age: ${age} is to big`)
    )
  }
)

v() // same as v.clearContext()
isValidPerson({ name: '', age: 969 })
console.log(v.explanation)
/**
* [
*   'Person with empty name, really?',
*   'Your age: 969 is to big'
* ]
*/ 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 错误

就像我们使用字符串一样，我们也可以用对象来解释。

```
// Util for calculating code errors.
// If you want you can create your own type of errors.
const invalidValueToError = code => invalidValue => ({
  invalidValue,
  code
}) 
```

Enter fullscreen mode Exit fullscreen mode

添加一些错误代码会很有用。我们可以用它们来获取发送给用户和其他人的消息。

```
// Error Codes
const CODE = {
  PERSON_IS_NOT_AN_OBJECT: 'PERSON_IS_NOT_AN_OBJECT',
  NAME_ABSENT: 'NAME_ABSENT',
  NAME_IS_NOT_STRING: 'NAME_IS_NOT_STRING',
  NAME_IS_EMPTY: 'NAME_IS_EMPTY',
  AGE_ABSENT: 'AGE_ABSENT',
  AGE_NOT_INTEGER: 'AGE_NOT_INTEGER',
  AGE_TO_SMALL: 'AGE_TO_SMALL',
  AGE_TO_BIG: 'AGE_TO_BIG'
} 
```

Enter fullscreen mode Exit fullscreen mode

添加了使用`invalidValueToError`函数的模式，该函数返回计算错误解释的函数。

```
const isValidPerson = v.and(
  v('object!', invalidValueToError(CODE.PERSON_IS_NOT_AN_OBJECT)),
  {
    name: v.and(
      v('required',  invalidValueToError(CODE.NAME_ABSENT)),
      v('string',    invalidValueToError(CODE.NAME_IS_NOT_STRING)),
      v('not-empty', invalidValueToError(CODE.NAME_IS_EMPTY))
    ),
    age: v.and(
      v('required',     invalidValueToError(CODE.AGE_ABSENT)),
      v('safe-integer', invalidValueToError(CODE.AGE_NOT_INTEGER)),
      v(v.min(18),      invalidValueToError(CODE.AGE_TO_SMALL)),
      v(v.max(140),     invalidValueToError(CODE.AGE_TO_BIG))
    )
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

让我们检查一些值，看看解释中存储了什么

*不是一个物体*

```
v()
isValidPerson(null)
console.log(v.explanation)
//[
//  {
//   invalidValue: null,
//   code: 'PERSON_IS_NOT_AN_OBJECT'
//  }
//] 
```

Enter fullscreen mode Exit fullscreen mode

*必填字段说明*

```
v()
isValidPerson({})
console.log(v.explanation)
//[
//  {
//   invalidValue: undefined,
//   code: 'NAME_ABSENT'
//  },
//  {
//   invalidValue: undefined,
//   code: 'NAME_ABSENT'
//  }
//] 
```

Enter fullscreen mode Exit fullscreen mode

*无效值*

```
v()
isValidPerson({ age: 963, name: '' })
console.log(v.explanation)
//[
//  {
//   invalidValue: '',
//   code: 'NAME_IS_EMPTY'
//  },
//  {
//   invalidValue: 963,
//   code: 'AGE_TO_BIG'
//  }
//] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 都在一起

很少，但是有可能同时使用解释和修复。
对于这样的目标，有`v.fromConfig`的方法。它接受验证的配置，并返回具有所有设置属性的验证函数。

*举例:*

这还是一样的

```
const invalidValueToError = code => invalidValue => ({
  invalidValue,
  code
})

// Error Codes
const CODE = {
  PERSON_IS_NOT_AN_OBJECT: 'PERSON_IS_NOT_AN_OBJECT',
  NAME_ABSENT: 'NAME_ABSENT',
  NAME_IS_NOT_STRING: 'NAME_IS_NOT_STRING',
  NAME_IS_EMPTY: 'NAME_IS_EMPTY',
  AGE_NOT_VALID: 'AGE_NOT_VALID'
} 
```

Enter fullscreen mode Exit fullscreen mode

添加使用`v.fromConfig`的

```
const isValidPerson = v.and(
  v.fromConfig({
    validator: 'object!',
    // explanation if not object
    explanation: invalidValueToError(CODE.PERSON_IS_NOT_AN_OBJECT), 
    // If not valid store default fix (calculate default value)
    default: () => ({ name: 'unknown' })
  }),
  {
    // if several configs are passed, validations will be combined with `v.and`
    name: v.fromConfig(
      { 
        validator: 'required',
        default: 'a',
        explanation: invalidValueToError(CODE.NAME_ABSENT)
      },
      {
        validator: 'string',
        default: 'b',
        explanation: invalidValueToError(CODE.NAME_IS_NOT_STRING)
      },
      {
        validator: 'not-empty',
        default: 'c',
        explanation: invalidValueToError(CODE.NAME_IS_EMPTY)
      }
    ),
    age: v.fromConfig(
      { 
        validator: 'safe-integer',
        filter: true,
        explanation: invalidValueToError(CODE.AGE_NOT_VALID)
      },
      {
        validator: v.min(18),
        default: 18,
        explanation: invalidValueToError(CODE.AGE_NOT_VALID)
      },
      {
        validator: v.max(140),
        default: 90,
        explanation: invalidValueToError(CODE.AGE_NOT_VALID)
      }
    )
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

*null object*

```
v()
const value = null
const test1 = isValidPerson(value)
const explanation = v.explanation
const fixedValue = v.fix(value)

console.log({
  value,        // null
  test1,        // false
  explanation,  // [{ invalidValue: null, code: 'PERSON_IS_NOT_AN_OBJECT' }]
  fixedValue    // { name: 'unknown' }
}) 
```

Enter fullscreen mode Exit fullscreen mode

*空物体*

```
v()
const value2 = {}
const test2 = isValidPerson({})
const explanation2 = v.explanation
const fixedValue2 = v.fix(value2)

console.log({
  value2,  // {}
  test2,   // false

  // [
  //  { invalidValue: undefined, code: 'NAME_ABSENT' },
  //  { invalidValue: undefined, code: 'AGE_NOT_VALID' }
  // ]
  explanation2, 
  fixedValue2   // { name: 'a' }
}) 
```

Enter fullscreen mode Exit fullscreen mode

*错误类型*

```
v()
const value3 = { age: '963', name: 1 }
const test3 = isValidPerson(value3)
const explanation3 = v.explanation
const fixedValue3 = v.fix(value3)

console.log({
  value3, // { age: '963', name: 1 }
  test3,  // false

  //[
  //  { invalidValue: 1,     code: 'NAME_IS_NOT_STRING' },
  //  { invalidValue: '963', code: 'AGE_NOT_VALID' }
  //]
  explanation3,
  fixedValue3    // { name: 'b' }
}) 
```

Enter fullscreen mode Exit fullscreen mode

*正确的类型，错误的价值观*

```
v()
const value4 = { age: 963, name: '' }
const test4 = isValidPerson(value4)
const explanation4 = v.explanation
const fixedValue4 = v.fix(value4)

console.log({
  value4,       // { age: 963, name: '' }
  test4,        // false

  //[
  // { invalidValue: 1,     code: 'NAME_IS_NOT_STRING' },
  // { invalidValue: '963', code: 'AGE_NOT_VALID' }
  //]
  explanation4,
  fixedValue4   // 
}) 
```

Enter fullscreen mode Exit fullscreen mode

*有效数据*

```
v()
const value5 = { age: 21, name: 'Maksym' }
const test5 = isValidPerson(value5)
const explanation5 = v.explanation
const fixedValue5 = v.fix(value5)

console.log({
  value4,       // { age: 21, name: 'Maksym' }
  test4,        // true
  explanation4, // []
  fixedValue4   // { age: 21, name: 'Maksym' }
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 清除代码

> 清晰的代码是你能容易理解和修改的代码

* * *

### 可读

有一些特性使代码更具可读性:

*   对象验证模式是与必须验证的对象具有相同结构的对象
*   验证函数的文本别名

* * *

### 可修改

有一些特性使代码更容易修改:

*   易读有时意味着容易修改。
*   方法名称和结构——更容易找到变化的地方
*   自定义验证功能——允许您进行任何类型的验证

* * *

## 附加可能性

还有几种额外的可能性:

| 方法 | 描述 |
| --- | --- |
| `v.example(schema, ...examples)` | 如果示例无效，它将抛出错误。它可用作 shema 的文件和测试。如果示例有效，则返回验证函数 |
| `v.validOr(schema, defaultValue)` | 返回采用`value`
的函数，如果`value`不是值，则用`defaultValue`替换 |
| `v.omitInvalidProps(objectSchema)` | 返回采用`value`的函数。如果值不是对象-返回不变。
如果`value`是 object -它测试所有出现在`objectSchema`中的道具，并删除所有无效的道具 |
| `v.throwError(schema, errorMessage)` | 返回采用`value`的函数。如果有效，
返回`value`。否则抛出错误。
可用于管道功能。 |

* * *

## 其他方案

有很多好的验证库，其中有`ajv`、`joi`、`yup`、`type-contract`。他们既美丽又强壮。如果你发现这个解决方案不适合你，你应该使用它们。

* * *

## 联系人

| 作者 | 安德鲁·贝莱茨基 |
| 位置 | Adraba 前端开发人员 |
| 电子邮件 | AndrewBeletskiy@gmail.com |
| 开源代码库 | [https://github.com/whiteand](https://github.com/whiteand) |****