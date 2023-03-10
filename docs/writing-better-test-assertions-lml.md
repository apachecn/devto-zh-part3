# 编写更好的测试断言

> 原文：<https://dev.to/webpapaya/writing-better-test-assertions-lml>

做 TDD 是我日常工作流程中不可或缺的一部分。测试帮助我把复杂的问题分解成更小的块，这样我就能更容易地处理。这有助于我独立开发应用程序的各个部分，并专注于核心业务逻辑，而不必担心破坏现有的功能。获得对我的变更的快速反馈使我能够快速行动并构建更健壮的系统。通过大量的小测试来检查被测单元的一种行为，可以很容易地看出应用程序的能力。这些小测试经常会导致维护开销，因为额外的需求会使这些测试中断，即使只是添加了功能，而现有的行为保持不变。这导致了这样一个问题，即使测试的行为没有改变，它们也需要被改变。另一个问题经常出现在针对外部系统(如数据库)编写测试时。

> 如果没有指定 order by 子句，那么 Q 的行的排序是依赖于实现的。([https://dba.stackexchange.com/a/6053](https://dba.stackexchange.com/a/6053)

每次测试运行时以不同的顺序获取记录是一个常见的问题，可能会导致 green suite 在本地无法通过 CI 测试。经过一些研究后，我意识到我的测试断言的编写方式可能是我脆弱测试的根本原因。在这篇文章中，我将分享我在编写更好的测试断言的旅程中的一些发现。

# TLDR

以双倍速度观看来自[维也纳 JS Meetup](https://www.youtube.com/watch?v=pXBZ0G1cp3Q) 的演讲。

# 什么是测试断言

断言是程序中特定点的布尔表达式，除非程序[源代码](http://wiki.c2.com/?WhatAreAssertions)中有错误，否则该表达式为真。断言的一个非常基本的实现可能如下所示:

```
const assert = (value, message = 'assertion failed') => {
  if (!value) { throw new Error(message); }
}

assert(1 === 1, '1 should be equal to 1');
assert(1 === 2, '1 should be equal to 1'); // Throws exception 
```

每当向 assert 函数传递一个`falsy`值时，就会抛出一个异常，并附带一条可选消息。当在测试用例中抛出一个未处理的异常时，它会被自动标记为失败。上面的测试断言很低级，表达性不强。断言库通过提供各种不同的高级断言来解决这个问题，使得测试更容易阅读。一些常见的断言库包括:

*   柴. js()
*   node.js 断言模块([https://nodejs.org/api/assert.html](https://nodejs.org/api/assert.html))
*   should . js([http://shouldjs.github.io/](http://shouldjs.github.io/))
*   哈姆杰特([https://github.com/rluba/hamjest](https://github.com/rluba/hamjest))

# 场景

使“难以维护”测试的问题更容易理解。我用不同的用户故事创建了一个人工应用程序。该应用程序是一个本地超市的员工管理系统。业主希望超市在周日营业，但由于法律限制，并非所有员工都被允许在周日工作。为了查看谁可以工作，她要求生成一份员工的特殊报告。出于简单起见，实现只关注 JS 中的业务逻辑。在现实世界的应用程序中，可以直接查询数据库。

## 第一个用户-故事

> 作为店主，我想查看所有 18 岁以上员工的列表，这样我就知道谁被允许在周日工作。

在阅读了这个需求之后，下面的测试用例就产生了。

```
import { assertThat, equalTo } from 'hamjest';

const employees = [
  { name: 'Max', age: 17 },
  { name: 'Sepp', age: 18 },
  { name: 'Nina', age: 15 },
  { name: 'Mike', age: 51 }
];

it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, equalTo([employees[1], employees[3]]));
}); 
```

运行测试后，以下测试失败:

```
❌ returns employees which are older than 18 
```

为了使该测试为绿色，实施以下功能:

```
const listEmployees = (employees) => employees
  .filter((employee) => employee.age >= 18); 
```

再次运行测试后，测试显示为绿色。

```
✔️ returns employees which are older than 18 
```

## 第二用户-故事

> 作为店主，我希望员工名单按姓名排序，这样我可以更容易地找到员工。

无需过多关注现有测试，就可以添加下一个测试用例:

```
import { assertThat, equalTo } from 'hamjest';

const employees = [
  { name: 'Max', age: 17 },
  { name: 'Sepp', age: 18 },
  { name: 'Nina', age: 15 },
  { name: 'Mike', age: 51 }
];

it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, equalTo([employees[1], employees[3]]));
});

// New test Case
it('returns employees ordered by their name', () => {
  const result = listEmployees(employees);
  assertThat(result, equalTo([employees[3], employees[1]]));
}); 
```

```
✔️ returns employees which are older than 18
❌ returns employees ordered by their name 
```

看到新测试失败后，将实现以下内容:

```
const listEmployees = (employees) => employees
  .filter((employee) => employee.age >= 18)
  .sort((a, b) => a.name.localeCompare(b.name)); 
```

```
❌ returns employees which are older than 18
✔️ returns employees ordered by their name 
```

排序功能已成功实现，但现在第一个已经工作的测试失败了。在比较了测试断言之后，测试失败的原因就显而易见了。该测试可能会更改如下:

```
// before
it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, equalTo([employees[1], employees[3]]));
});

// afterwards
it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, containsInAnyOrder(employees[1], employees[3]));
}); 
```

`containsInAnyOrder`匹配器通过忽略结果的排序来修复前面的问题。它验证了这两个元素需要独立于它们的顺序出现。这一变化产生了绿色测试套件。

```
️✔️ returns employees which are older than 18
✔️ returns employees ordered by their name 
```

## 第三用户-故事

> 作为店主，我希望员工名单大写，这样我可以更好地阅读它。

再次从测试文件开始，添加一个新的测试:

```
import { assertThat, equalTo, containsInAnyOrder } from 'hamjest';

const employees = [
  { name: 'Max', age: 17 },
  { name: 'Sepp', age: 18 },
  { name: 'Nina', age: 15 },
  { name: 'Mike', age: 51 }
];

it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, containsInAnyOrder(employees[1], employees[3]));
});

it('returns employees ordered by their name', () => {
  const result = listEmployees(employees);
  assertThat(result, equalTo([employees[3], employees[1]]));
});

// New test case
it('returns employees whose names are capitalized', () => {
  const result = listEmployees(employees);
  assertThat(result[0].name, equalTo('MIKE'));
  assertThat(result[1].name, equalTo('SEPP'));
}); 
```

```
✔️ returns employees which are older than 18
✔️ returns employees ordered by their name
❌ returns employees whose names are capitalized 
```

满足这一缺陷的一个可能的实现如下所示:

```
const listEmployees = (employees) => employees
  .filter((employee) => employee.age >= 18)
  .sort((a, b) => a.name.localeCompare(b.name))
  .map((employee) => ({ ...employee, name: employee.name.toUpperCase() })); 
```

运行测试后，我们看到新行为被成功添加，但是我们破坏了所有其他测试。

```
❌️ returns employees which are older than 18
❌️ returns employees ordered by their name
✔️ returns employees whose names are capitalized 
```

其他测试的问题是，hamjest 无法再比较对象，因为大写的名称与原始名称不同。在这个微不足道的例子中，改变两个测试可能不是最大的问题。在一个更复杂的例子中，判断变化是否破坏了原来的行为可能需要更多的时间。在本例中，测试可能会更改为:

```
// original test
it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, equalTo([employees[1], employees[3]]));
});

// first iteration
it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  result.forEach((employee) => {
    assertThat(employee.age >= 18, equalTo(true));
  });
});

// final iteration
it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, everyItem(hasProperty('age', greaterThanOrEqualTo(18))));
}); 
```

通过将断言更改为以下内容，我们向该测试引入了一个主要问题。以下实现导致绿色测试。

```
const listEmployees = (employees) => [] 
```

所以这个断言现在是“未指定的”，这意味着一个无效的/损坏的实现会导致一个绿色的测试套件。通过将断言更改为以下内容，可以防止这种情况:

```
it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, allOf(
    hasProperty('length', greaterThanOrEqualTo(1)),
    everyItem(hasProperty('age', greaterThanOrEqualTo(18))),
  );
}); 
```

```
✔️ returns employees which are older than 18
❌️ returns employees ordered by their name
✔️ returns employees whose names are capitalized 
```

另一个测试可能会更改为:

```
// original implementation
it('returns employees ordered by their name', () => {
  const result = listEmployees(employees);
  assertThat(result, equalTo([employees[3], employees[1]]));
});

// final iteration
it('returns employees ordered by name', () => {
  const result = listEmployees(employees);
  assertThat(result, orderedBy((a, b) => a.name < b.name));
}); 
```

在这些改变之后，所有 3 个测试都是绿色的。由于前面的测试已经检查了空结果问题，所以我们不在其他测试中测试这种行为。

```
✔️ returns employees which are older than 18
✔️ returns employees ordered by their name
✔️ returns employees whose names are capitalized 
```

## 第四个用户-故事

> 作为店主，我希望员工按姓名降序排列，而不是升序。

因为已经有一个测试用例来验证订单，我们决定改变这个测试来匹配新的需求。

```
import { 
  assertThat,
  greaterThanOrEqualTo, 
  everyItem, 
  orderedBy,
  hasProperty,
} from 'hamjest';

const employees = [
  { name: 'Max', age: 17 },
  { name: 'Sepp', age: 18 },
  { name: 'Nina', age: 15 },
  { name: 'Mike', age: 51 },
];

it('returns employees which are older than 18', () => {
  const result = listEmployees(employees);
  assertThat(result, everyItem(hasProperty('age', greaterThanOrEqualTo(18))));
});

// changed assertion
it('returns employees ordered by name descendent', () => {
  const result = listEmployees(employees);
  assertThat(result, orderedBy((a, b) => a.name > b.name));
});

it('returns employees whose names are capitalized', () => {
  const result = listEmployees(employees);
  assertThat(result[0].name, equalTo('MIKE'));
  assertThat(result[1].name, equalTo('SEPP'));
}); 
```

```
✔️ returns employees which are older than 18
️️❌ returns employees ordered by their name descendent
️️️✔️ returns employees whose names are capitalized 
```

为了让我们的测试再次变绿，实现了以下代码:

```
const listEmployees = (employees) => employees
  .filter((employee) => employee.age >= 18)
  .sort((a, b) => b.name.localeCompare(a.name))
  .map((employee) => ({ ...employee, name: employee.name.toUpperCase() })); 
```

第三个测试现在报告失败。

```
✔️ returns employees which are older than 18
✔️ returns employees ordered by their name descendent
️️️️️❌ returns employees whose names are capitalized 
```

```
// original implementation
it('returns employees whose names are capitalized', () => {
  const result = listEmployees(employees);
  assertThat(result[0].name, equalTo('MIKE'));
  assertThat(result[1].name, equalTo('SEPP'));
});

// first iteration
it('returns employees whose names are capitalized', () => {
  const result = listEmployees(employees);
  assertThat(result, everyItem(hasProperty('name', matchesPattern(/[A-Z]*/))));
});

// second iteration
const inUpperCase = () => matchesPattern(/[A-Z]*/);
it('returns employees whose names are capitalized', () => {
  const result = listEmployees(employees);
  assertThat(result, everyItem(hasProperty('name', inUpperCase())));
}); 
```

我们运行测试，看到所有测试都是绿色的。

```
✔️ returns employees which are older than 18
✔️ returns employees ordered by their name descendent
️️️️️✔️ returns employees whose names are capitalized 
```

# 结论

这篇博文显示了额外的需求可能会导致现有的测试失败，即使它们的行为没有改变。通过在断言中表达确切的期望结果，使得整个测试套件不那么脆弱，并且更容易更改。拥有不依赖于实现细节或先前测试的测试可以更容易地添加和删除功能。例如，一个新的特性请求，其中的雇员应该被随机返回，不会导致其他测试中断。过去几年我一直在使用 hamjest，我可以推荐测试一下。

# 编辑

我用 jest 和 chai 重构了示例，并将它们推送到[https://github.com/webpapaya/better-test-assertions](https://github.com/webpapaya/better-test-assertions)。jest-expect 和 chai 在匹配数组中的元素时都有问题。hamjest 的 API 设计很容易扩展，使得编写非常复杂的匹配器成为可能。

# 编辑 2

退税部分被一个更好的匹配器取代，因为它没有引入新的测试。