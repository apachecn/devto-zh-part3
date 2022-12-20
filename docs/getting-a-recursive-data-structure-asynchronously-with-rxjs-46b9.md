# 用 RxJS 异步获取递归数据结构

> 原文：<https://dev.to/krumpet/getting-a-recursive-data-structure-asynchronously-with-rxjs-46b9>

在你的应用程序中，你可能想要保存一个递归数据结构——比如一个文件夹树。这些文件夹可以用一个`id`属性来标识，并且包含一个子文件夹数组:

```
export interface Folder {
  id: number;
  name: string;
  children: Folder[];
} 
```

然而，在服务器上，每个文件夹的数据可能只包含子文件夹的标识符:

```
export interface ServerData {
  id: number;
  name: string;
  children: number[];
} 
```

这意味着当我们编写一个函数来返回一个文件夹的可观察对象时，给定它的`id`，我们首先需要从服务器获取它的所有子文件夹，然后才能返回完整的对象。

让我们来看看一些模拟数据，以及一个异步获取这些模拟数据的函数:

```
export const results: ServerData[] = [
  { id: 0, name: "first", children: [1, 2, 3] },
  { id: 1, name: "second", children: [4] },
  { id: 2, name: "third", children: [] },
  { id: 3, name: "fourth", children: [] },
  { id: 4, name: "fifth", children: [] }
];

export function getFromServer(id: number): Observable<ServerData> {
  return of(results[id]);
} 
```

现在，让我们试着把我们得到的`ServerData`转换成一个`Folder`，包括它的`children`属性:

```
export function getRecursive(id: number): Observable<Folder> {
  return getFromServer(id).pipe(
    map(data => ({
      id: data.id,
      name: data.name,
      // oops, wrong type!
      children: data.children.map(childId => getRecursive(childId))
    }))
  );
} 
```

这个实现不起作用，因为上面的`children`属性实际上是一个`Observable<Folder>`数组。我们需要将获取父文件夹的结果与递归调用父子文件夹 id 的所有结果结合起来。

```
export function getRecursive(id: number): Observable<Folder> {
  return getFromServer(id).pipe(
    map(data => ({
      parent: { name: data.name, id: data.id, children: [] },
      childIds: data.children
    })),
    flatMap(parentWithChildIds => forkJoin([
      of(parentWithChildIds.parent),
      ...parentWithChildIds.childIds.map(childId => getRecursive(childId))
    ])),
    tap(([parent, ...children]) => parent.children = children),
    map(([parent,]) => parent)
  );
} 
```

我们在这里做的是创建没有任何子对象的`parent`，并沿着管道进一步移动它和它的子 id。然后，我们创建一个 observables 数组，包含我们已经拥有的`parent`(这是`of`部分)和每个子文件夹 id 的所有`getRecursive`调用。一旦我们获得了每个递归调用的返回值，我们就设置`parent.children = children`，并使用数组析构来返回父文件夹，现在父文件夹的子属性设置正确了。

一个简单的测试来展示这个函数的作用:

```
describe('test recursive observables', () => {
  test('test', () => {
    getRecursive(0).subscribe(data => {
      console.log(data);
      console.log(data.children.find(f => f.id === 1).children);
    });
  })
}) 
```

并且输出:

```
 parent folder: { name: 'first',
      id: 0,
      children:
       [ { name: 'second', id: 1, children: [Array] },
         { name: 'third', id: 2, children: [] },
         { name: 'fourth', id: 3, children: [] } ] }

    children of child with id 1: [ { name: 'fifth', id: 4, children: [] } ] 
```