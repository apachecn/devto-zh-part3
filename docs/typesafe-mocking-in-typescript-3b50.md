# TypeScript 中的类型安全模拟

> 原文：<https://dev.to/jonrimmer/typesafe-mocking-in-typescript-3b50>

假设我有一个角度服务`TodosApiService`，形状如下:

```
@Injectable()
class TodosApiService {
  constructor(private http: HttpClient) {}
  getTodo(id: number): Observable<Todo> {}
  getTodos(): Observable<Todo[]> {}
  createTodo(todo: Todo): Observable<number> {}
  updateTodo(todo: Todo): Observable<void> {}
  deleteTodo(id: number): Observable<void> {}
  private checkCache(id: number): Todo | null
} 
```

现在，假设我想编写一个类`MockTodosApiService`来代替`TodosApiService`，它返回模拟数据，而不是发出实际的网络请求。这对于在像[故事书](https://storybook.js.org/)这样的系统中孤立地测试或开发组件可能是有用的。

如何确保我的模拟类提供与`TodoApisService`相同的接口？也就是说，我实现了服务的所有公共方法，并且返回了正确的类型？如果有人过来给`TodosApiService`添加一个方法`bulkUpdateTodos()`，他们会得到一个编译错误，除非他们也把它添加到`MockTodosApiService`？

一种方法是创建一个类型脚本`interface`，由`TodosApiService`和`MockTodosApiService`实现:

```
interface TodosApi {
  getTodo(id: number): Observable<Todo> {}
  getTodos(): Observable<Todo[]> {}
  createTodo(todo: Todo): Observable<number> {}
  updateTodo(todo: Todo): Observable<void> {}
  deleteTodo(id: number): Observable<void> {}
}

class TodosApiService implements TodosApi { }
class MockTodosApiService implements TodosApi { } 
```

在像 C#或 Java 这样的语言中，这将是我唯一真正的选择，但这有点痛苦。我现在有三个地方可以互相保持同步。

然而，JS 的动态特性和 TypeScript 的表达能力提供了不同的选择。相反，我可以使用映射类型告诉编译器，我希望我的模拟类提供与“真正的”服务相同的公共接口。就这么简单:

```
class MockTodosApiService implements Pick<TodosApiService, keyof TodosApiService> { } 
```

如果我以与`TodosApiService`的公共接口不兼容的方式实现`MockTodosApiService`，TypeScript 现在会抱怨。但是，它会*而不是*抱怨我没有复制构造函数，或者私有的`checkCache()`方法和`http`字段。

这是可行的，因为`keyof`返回一个类型的每个公共字段和方法的名称。然后我们使用`Pick<>`映射类型，它返回一个类型属性的子集。结果是一个类型，它只包含那些由`keyof TodosApiService`返回的来自`TodosApiService`的属性。通过说`MockTodosService` `implements`这种类型，我得到了编译时检查，我这样做是正确的。