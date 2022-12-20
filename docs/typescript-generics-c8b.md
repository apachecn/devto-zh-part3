# 类型脚本泛型介绍

> 原文：<https://dev.to/micahriggan/typescript-generics-c8b>

Typescript 对于开发者体验来说是很棒的。

您可以获得关于代码有效性的实时反馈，并通过类型注释自我记录代码。此外，自动完成和跳转到定义功能正在改变游戏速度。

在 Typescript 中，您根据类型编写逻辑。有时您需要编写对多种类型都有效的逻辑。一个常见的例子是数组。你可以拥有任何类型的数组。

```
Array<string>
Array<number>
Array<Array<number>>
Array<{name: string}> 
```

这个概念被称为模板类型或泛型。下面是一个链表类型的例子

```
type LinkedNode<T> = { next: LinkedNode<T> | null, value: T }
type LinkedList<T> = { head: LinkedNode<T> } 
```

在这篇文章中，我想展示一些例子，对比使用“任何”和“模板”的代码安全性

## 例 1: Log Obj[key]

在本例中，logValue 接受一个对象和该对象的一个键。它记录与该对象上的键相关联的值。

```
function anyLogValue(obj: any, key: string) {
  console.log(obj[key]);
}

function genericLogValue<T>(obj: T, key: keyof T) {
  console.log(obj[key]);
}

function simpleTest() {
  const Micah = {name: 'Micah'};
  genericLogValue(Micah, "name")
  // genericLogValue(Micah, "age")
  // would not compile because "age" is not a key of Micah
  anyLogValue(Micah, "age");
}

simpleTest(); 
```

### 输出

```
Micah
undefined 
```

我们在这里得到的好处是，如果我们试图记录一个无效的键，我们将得到一个编译错误。

这可以防止打字错误和其他无效的键错误。我们只能指定一个有效的键作为 logValue 的第二个参数。

```
// Example Compile Error
logValue(Micah, "age"); 
```

## 示例 2:非类型安全表

在本例中，我们处理了两种类型的数据。人和车。我们有这个 BadTable 类来帮助我们处理这些类型的列表，就好像它们是一个列行数据结构一样。

看看你能否用 BadTable
找出你被允许犯的错误

```
interface Person {
  name: string;
  age: number;
}

interface Car {
  modelName: string;
  year: number;
}

class BadTable {
  rows = new Array<any>()

  addRow(row: any) {
    this.rows.push(row);
  }

  getColumn(col: keyof any) {
    return this.rows.map(r => r[col]);
  }
}
function testBadTable() {
  const people = new BadTable()
  people.addRow({name: 'Micah'});
  people.addRow({name: 'Micah', age: 25});

  const cars = new BadTable();
  cars.addRow({modelName: 'Toyota Something or Other', year: 2010});
  cars.addRow({modelNme: 'Honda Something or Other', year: 2010});
  console.log(cars.getColumn("modelName"));
  console.log(cars.getColumn("year"));
  console.log(cars.getColumn("age")); // [undefined, undefined]
}

testBadTable(); 
```

### 输出

```
[ 'Toyota Something or Other', undefined ]
[ 2010, 2010 ]
[ undefined, undefined ] 
```

## 例 3:迈向泛型

在前面的例子中，我们被允许犯一个打字错误，并试图获得一个不属于我们类型的键的列。

在这个例子中，我们将开始转向一个更强类型的表，它不是通用的。这将产生一些逻辑重复。

在归纳的过程中，我们需要识别重复的逻辑，即使涉及到类型

```
export interface Person {
  name: string;
  age: number;
}

export interface Car {
  modelName: string;
  year: number;
}

class PersonTable {
  rows = new Array<Person>();
  addRow(row: Person) {
    this.rows.push(row);
  }
  getColumn(col: keyof Person) {
    return this.rows.map(r => r[col]);
  }
}

class CarTable {
  rows = new Array<Car>();
  addRow(row: Car) {
    this.rows.push(row);
  }
  getColumn(col: keyof Car) {
    return this.rows.map(r => r[col]);
  }
}

function testTables() {
  const people = new PersonTable()
  //people.addRow({name: 'Micah'});
  people.addRow({name: 'Micah', age: 25});

  const cars = new CarTable();
  cars.addRow({modelName: 'Toyota Something or Other', year: 2010});
  //cars.addRow({modelNme: 'Honda Something or Other', year: 2010});
  cars.getColumn("modelName");
  cars.getColumn("year");
  //cars.getColumn("age");
}

testTables(); 
```

## 例 4:学习泛型

在前面的例子中，我们可以看到，为了保持类型安全，我们必须为每个类型实现一个新的表。

这种重复应该是一个我们可以概括的信号。

### 仿制药！

在 typescript 中，您可以创建一个泛型类，它允许您将类型作为变量传入！

```
class MyClass<TypeVar> {
  myProp: TypeVar;
}
type GenericType<TypeVar> = TypeVar & {modified: boolean}
function genericFn<TypeVar>(arg: TypeVar) {} 
```

现在我们有能力定义类型变量，让我们修改我们复制的类

### 通用表

```
import { Person, Car } from "./3-generalization-step-1";

class Table<RowType> {
  rows = new Array<RowType>();

  addRow(row: RowType) {
    this.rows.push(row);
  }

  getColumn(col: keyof RowType) {
    return this.rows.map(r => r[col]);
  }
}

function testTable() {
  const people = new Table<Person>()
  people.addRow({name: 'Micah', age: 25});

  const cars = new Table<Car>();
  cars.addRow({modelName: 'Toyota Something or Other', year: 2010});
  cars.getColumn("modelName");
  cars.getColumn("year");
}

testTable(); 
```

## 结论

类型有助于防止常见的错误。

通过使用`generics<T>`,我们可以定义与变量类型一起工作的函数、类或类型。这允许我们编写适用于许多类型的逻辑。

为了一般化代码，我们需要识别重复的逻辑，即使是在类型方面。