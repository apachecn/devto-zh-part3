# 用 JavaScript 建模(和查询)关系数据

> 原文：<https://dev.to/mikewheaton/filtering-related-arrays-4kn5>

这篇文章描述了一种用 JavaScript 建模关系数据的方法，并展示了如何使用`filter()`和`some()`数组方法来查询这些数据。它并不打算取代一个真正的数据库，而是展示如何根据一个对象数组的内容来过滤另一个对象数组。

## 结构化数据

对于这个例子，让我们考虑老师和学生。每个人都可以用简单的对象来表示，包括他们的名字和唯一的 ID。ID 允许我们引用一个特定的老师或学生，即使他们的名字将来会改变。因为我们将有多个老师和学生，所以让我们将他们组织成数组:

```
const teachers = [
  {
    id: 1,
    name: 'John Kreuger',
  },
  {
    id: 2,
    name: 'Amy David',
  },
];

const students = [
  {
    id: 1,
    name: 'Alice Thompson',
  },
  {
    id: 2,
    name: 'Bill Lemond',
  },
  {
    id: 3,
    name: 'Ashley Jefferson',
  },
]; 
```

老师有多个学生，学生有多个老师。这是一种多对多的关系，有几种方法可以对这些数据进行建模。一种方法是创建一个教师和学生之间联系的列表。如果你想显得聪明，但把眼镜忘在家里了，这可以被称为[联想实体](https://en.wikipedia.org/wiki/Associative_entity)。

让我们创建第三个数组来描述这些连接。请注意，我们使用每个教师和学生的唯一 id，以防将来姓名更改，并允许人们共享同一个姓名。

```
const teachersToStudents = [
  {
    teacherId: 1,
    studentId: 1,
  },
  {
    teacherId: 1,
    studentId: 2,
  },
  {
    teacherId: 1,
    studentId: 3,
  },
  {
    teacherId: 2,
    studentId: 2,
  },
  {
    teacherId: 2,
    studentId: 3,
  },
]; 
```

## 过滤数据

好了，我们现在有三个数组来描述老师，学生，以及他们之间的联系。🎉

让我们把这个派上用场。我们虚构的应用程序可以为每个教师提供一个包含学生列表的个人资料页面。我们将需要一个函数，该函数将接受一个教师(通过他们的 ID 引用)并返回拥有该教师的学生的数组。

```
// Returns an array of students for a given teacher.
function getStudentsForTeacher(teacherId) {
  // @todo: Return array.
} 
```

这里我们要返回的是`students`数组的子集。幸运的是，这正是[过滤器()方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)所做的。它逐个遍历数组中的元素，将每个元素传递给我们提供的函数。如果我们的函数返回`true`，这个元素将被添加到一个新的数组中。

例如，如果我们的函数总是返回`true`，我们将得到一个包含每个学生的数组。

```
// Returns an array of students for a given teacher.
function getStudentsForTeacher(teacherId) {
  students.filter(student => true);
} 
```

所以我们需要的是一个函数，如果学生连接到老师，它将返回`true`，否则返回`false`。为了清楚起见，让我们为此创建第二个函数:

```
// Returns true or false, indicating whether a given teacher has a given student.
function teacherHasStudent(teacherId, studentId) {
  // @todo: Return true or false.
} 
```

我们如何回答这个问题？没错，通过引用我们描述了教师和学生之间所有联系的`teachersToStudents`数组。

我们可以创建一个循环来遍历数组，如果找到匹配，返回`true`,如果到达末尾，返回`false`。不可怕，但是有更好的方法。类似于过滤， [some()方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)将迭代数组中的元素。但是它不是创建一个新的数组，而是根据我们提供的测试函数运行每个元素，如果至少有一个元素通过了测试，就返回`true`。

```
// Returns true or false, indicating whether a given teacher has a given student.
function teacherHasStudent(teacherId, studentId) {
  return teachersToStudents.some(/* @todo: Test function that returns true if we find a match. */);
} 
```

因此，所缺少的是一个函数，它将告诉我们`teachersToStudents`数组是否包含我们正在寻找的带有教师和学生的对象。这里有一个箭头函数，它通过比较 IDs 来实现这一目的。

```
// Returns true if this item matches, and false otherwise.
teacherToStudent =>
  teacherToStudent.teacherId === teacherId &&
  teacherToStudent.studentId === studentId; 
```

通过将该函数传递给`some()`，我们现在有了一个工作函数，它告诉我们一个给定的老师是否有一个给定的学生！

```
// Returns true or false, indicating whether a teacher has a given student.
function teacherHasStudent(teacherId, studentId) {
  return teachersToStudents.some(
    teacherToStudent =>
      teacherToStudent.teacherId === teacherId &&
      teacherToStudent.studentId === studentId
  );
} 
```

我们可以更新`getStudentsForTeacher()`来使用它，这样它将通过`students`并创建一个新的数组，该数组只包含那些与指定教师相关联的学生。

```
// Returns an array of students for a given teacher.
function getStudentsForTeacher(teacherId) {
  return students.filter(student => teacherHasStudent(teacherId, student.id));
}

// Test it out!
console.log(getStudentsForTeacher(1)); // Returns 3 students.
console.log(getStudentsForTeacher(2)); // Returns 2 students. 
```

成功！我们采用了两个数组，使用第三个数组描述了它们的元素之间的关系，然后查询我们的数据以获得需要在教师的个人资料页面上显示的内容。看一看 [CodePen 演示](https://codepen.io/mikewheaton/pen/Nerxqd?editors=0012)，看看这一切是如何组合在一起的。

虽然这种确切的用例可以通过在服务器端进行查询来更好地处理(特别是当您处理成千上万的记录或敏感数据时)，但是这些数组方法对于您可能遇到的其他问题也很有用。

## 习题与延伸阅读

想得到更多的实践或了解更多？我建议你:

*   编写一个函数，返回给定学生的教师。
*   仔细阅读[模式](http://learnmongodbthehardway.com/schema/schemabasics/),找到组织这些数据的其他方法。
*   遍历教师，并在浏览器中显示每位教师的学生列表。
*   添加一系列课程，将这些课程与教师和学生联系起来。注意冗余数据。
*   尝试其他常见的数组方法，如 [reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 和 [forEach()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 。