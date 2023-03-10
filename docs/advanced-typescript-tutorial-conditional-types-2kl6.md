# TypeScript 教程-条件类型

> 原文：<https://dev.to/aexol/advanced-typescript-tutorial-conditional-types-2kl6>

您好，这是高级 typescript 教程系列的第二篇文章。今天我将讲述
的基本用法

```
extends 
```

Enter fullscreen mode Exit fullscreen mode

关键字和条件类型。条件类型看起来像什么？

```
type StringOrArray<T> = T extends string[] ? 'array' : T extends string ? 'string' : never
const a:string = "hello"
const b:string[] = ["hello","world"]
const c:number = 1

const d:StringOrArray<typeof a> = "string"
const e:StringOrArray<typeof b> = "array"
let f:StringOrArray<typeof c> 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分析这段代码:

1.  我们检查泛型类型是否是字符串数组
2.  如果是数组，将类型设为字符串常量' array '
3.  如果它不检查类型。如果是字符串，请将类型设为字符串常量' string '
4.  否则，类型为“从不”

说实话，这段代码毫无用处，但可以给你一些`extends`关键字如何工作的空间。下一个例子将是一个真实的例子，我们确定表单域的类型，以给用户正确的选项。

```
type FieldType = "string" | "float" | "date"

type BaseField = {
    name:string
}

type Field<T extends FieldType> = BaseField & {
    value: T extends "string" ? string : T extends "float" ? number : T extends "date" ? Date : never
}

const stringField:Field<"string"> = {
    name:"myfield",
    value:"aaa"
}
const floatField:Field<"float"> = {
    name:"myfield",
    value:1.0
}

const dateField:Field<"date"> = {
    name:"myfield",
    value: new Date()
} 
```

Enter fullscreen mode Exit fullscreen mode

这个稍微高级一点。FieldType 是怎么回事？它只是检查转换为泛型类型的字符串，以返回正确的类型。

```
 type FieldType = "string" | "float" | "date";
type BaseFieldExtended = {
  name: string;
  type: FieldType;
};
const FieldExtended = <T extends BaseFieldExtended>(
  baseField: T & {
    value: T["type"] extends "string"
      ? string
      : T["type"] extends "date"
      ? Date
      : T["type"] extends "float"
      ? number
      : never;
  }
) => baseField;

FieldExtended({
  name: "a",
  type: "string",
  value: "bbb"
});

FieldExtended({
  name: "a",
  type: "float",
  value: 12
});

FieldExtended({
  name: "a",
  type: "date",
  value: new Date()
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就是 typescript 的用途。提供复杂的自动补全功能:)。等待下一系列的高级 typescript 教程。