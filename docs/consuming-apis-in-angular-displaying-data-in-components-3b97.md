# 在 Angular 中使用 API:在组件中显示数据

> 原文：<https://dev.to/florimondmanca/consuming-apis-in-angular-displaying-data-in-components-3b97>

欢迎回来！本文是上一篇文章的后续:

[![florimondmanca](img/122f3b1f9c33dcdad1433bb20f0dab94.png)](/florimondmanca) [## 使用 Angular 中的 APIs 模型适配器模式

### 弗洛里蒙德·曼卡 9 月 5 日 188 分钟阅读

#webdev #angular #typescript #devtips](/florimondmanca/consuming-apis-in-angular--the-model-adapter-pattern-3fk5)

如果你还没有读过，去看看吧！这篇文章会经常提到它。

最近我收到了来自[@米兰科瓦奇](https://dev.to/milankovach)的一个问题，关于如何在一个组件中使用`CourseService`来显示课程列表。

[![milankovach profile image](img/cc041bc60a70b0276bccf64751e31178.png) ](/milankovach) [ Milan Kovac ](/milankovach) • [<time datetime="2019-02-22T00:53:50Z"> Feb 22 '19 </time>](https://dev.to/milankovach/comment/910k) 

你好，Florimond，
这很好，但是你能添加简短的描述(可能在评论部分)如何在 n00bs 的其他组件中使用你的服务吗？谢谢！:)

这正是我们将在这篇初学者友好的文章中讨论的内容。

这篇文章应该对那些想知道如何检索和显示从外部 API 获取的数据的人有所帮助。😊

## 快速复习

在最初的帖子中，我们讨论了一个设计模式，我用它来标准化我的 Angular 应用程序如何与 REST APIs 通信:模型适配器模式。

给定`GET /courses` API 端点，我们构建了一个`Course`模型、`CourseAdapter`和`CourseService`来帮助我们从 API 中获取课程列表。

现在项目结构看起来是这样的:

```
src/app
├── app.component.css
├── app.component.html
├── app.component.ts
├── app.module.ts
└── core
    ├── course.model.ts
    └── course.service.ts 
```

Enter fullscreen mode Exit fullscreen mode

这里的目标是构建一个使用`CourseService`获取课程列表的`CourseListComponent`，并以简单无序列表的形式显示它们。

## 我们来造吧！

### 生成`CourseListComponent`

好了，我们开始吧。首先，我们将使用[角度命令](https://cli.angular.io) :
生成组件

```
ng generate component CourseList 
```

Enter fullscreen mode Exit fullscreen mode

组件的 TypeScript (TS)、HTML 和 CSS 文件将在`src/app/course-list/`下生成。下面是目前为止 TS 文件的样子:

```
// course-list/course-list.component.ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-course-list',
  templateUrl: './course-list.component.html',
  styleUrls: ['./course-list.component.css']
})
export class CourseListComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 设置属性和模板

首先，让我们在组件上添加一个空的课程列表:

```
 // course-list/course-list.component.ts
  import { Component, OnInit } from '@angular/core';

  @Component({
    selector: 'app-course-list',
    templateUrl: './course-list.component.html',
    styleUrls: ['./course-list.component.css']
  })
  export class CourseListComponent implements OnInit {

+   courses: Courses[];

-   constructor() { }
+   constructor() {
+     this.courses = [];
+   } 
    ngOnInit() {
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们设置模板。这里没有什么特别的，我们只是使用一个 [`*ngFor`](https://angular.io/guide/displaying-data#showing-an-array-property-with-ngfor) 来显示每个课程的列表项，并使用 [`DatePipe`](https://angular.io/api/common/DatePipe) 来格式化日期。

```
<!-- course-list/course-list.component.html -->
<ul>
  <li *ngFor="let course of courses">
    <p>
      {{ course.name }} • {{ course.code }} • Created
      {{ course.created | date }}
    </p>
  </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们更新`AppComponent`的模板以显示课程列表:

```
<!-- app.component.html -->
<h1>Courses</h1>

<app-course-list></app-course-list> 
```

Enter fullscreen mode Exit fullscreen mode

好吧！让我们打开浏览器，应该会看到“课程”标题和……一个空列表。为什么？嗯，我们还没有拿到任何课程！

### 实现 API 端点

在我们插入`CourseService`之前，请记住现在它指的是`https://api.myapp.com/courses`——并且 API 不存在！

也就是说，在一个服务器上测试`CourseService`会很好，不是吗？

因此，让我们为此构建一个快速后端 API。我将使用 Python 和 [Bocadillo](https://github.com/bocadilloproject/bocadillo) (无耻的插一句:我是 Bocadillo 的维护者！)来提供我们需要从浏览器访问的`GET /courses`端点。

你不需要了解 Python，也不需要理解下面的代码，但是我在这里展示给感兴趣的人:

```
# app.py
# Install: `pip install bocadillo` from bocadillo import App

app = App(
    enable_cors=True,
    cors_config={"allow_origins": ["*"], "allow_methods": ["*"]},
)

COURSES = [
    {
        "id": 1,
        "code": "adv-maths",
        "name": "Advanced Mathematics",
        "created": "2018-08-14T12:09:45",
    },
    {
        "id": 2,
        "code": "cs1",
        "name": "Computer Science I",
        "created": "2018-06-12T18:34:16",
    },
]

@app.route("/courses")
async def courses_list(req, res):
    res.media = COURSES

if __name__ == "__main__":
    app.run() 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`GET /courses`端点将返回一个硬编码的课程列表。

我们可以使用`$ python app.py`在终端中启动 API，并让它运行。

### 集成 API 与`CourseService`

作为最后一个集成步骤，我们需要更新 URL，`CourseService`用来获取课程:

```
// core/course.service.ts
// ...

@Injectable({
  providedIn: 'root'
})
export class CourseService {

  private apiUrl = 'http://localhost:8000/courses';

  constructor(
    private http: HttpClient,
    private adapter: CourseAdapter,
  ) { }

  list(): Observable<Course[]> {
    return this.http.get(this.apiUrl).pipe(
      // Adapt each item in the raw data array
      map((data: any[]) => data.map(item => this.adapter.adapt(item))),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用`CourseService`抓取课程

我们现在准备将`CourseService`插入到`CourseListComponent`中！

下面是我们将采取的步骤:

1.  **导入**服务。
2.  **使用 Angular 的[依赖注入](https://www.angular.io/guide/dependency-injection)在组件中注入**。
3.  在组件的`ngOnInit()`方法中，获取可观察到课程列表的 [RxJS](https://angular.io/guide/rx-library) ，并通过**订阅**。
4.  **将获取的课程列表存储在组件上，以便在模板中呈现。**

想知道如何翻译成代码？请看下面——我为上面的每个步骤添加了地标:

```
import { Component, OnInit } from "@angular/core";
import { Course } from "../core/course.model";
// (1) Import
import { CourseService } from "../core/course.service";

@Component({
  selector: "app-course-list",
  templateUrl: "./course-list.component.html",
  styleUrls: ["./course-list.component.css"]
})
export class CourseListComponent implements OnInit {
  courses: Course[];

  // (2) Inject
  constructor(private courseService: CourseService) {
    this.courses = [];
  }

  ngOnInit() {
    // (3) Subscribe
    this.courseService.list().subscribe((courses: Course[]) => {
      // (4) Store
      this.courses = courses;
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 庆祝

这就对了。如果我们在`http://localhost:8000`打开浏览器，我们会看到用 sexy Times New Roman 显示的课程列表。

[![](img/b0f704c85a1664757cc8dfa207345778.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8_UqZGH5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/1e5a9e87-ce68-4825-b972-790fa84d55f5.png) 
造型确实不在这篇博文的讨论范围之内。

## 包装完毕

好吧，让我们看看我们在这里取得了什么:

1.  我们使用[角度命令](https://cli.angular.io)生成了`CourseListComponent`。
2.  我们设置了组件的`courses`属性和它的【模板】。
3.  我们使用 Python 和 [Bocadillo](https://bocadilloproject.github.io) 构建 API 端点来测试我们的组件。
4.  当然，我们使用了`CourseService`和 [RxJS](https://angular.io/guide/rx-library) 来获取列表。

事实上，当我使用 Angular 构建 web 应用程序时，这对我来说是一个非常典型的工作流——我从剔除组件开始，然后实现我需要的后端端点，并将它们与服务集成，最终显示数据。

如果你对代码感兴趣，我把它上传到了 GitHub repo: [ng-courses](https://github.com/florimondmanca/ng-courses) 。

## 保持联系！

如果你喜欢这篇文章，你可以在 Twitter 上找到我，获取更新、公告和新闻。🐤