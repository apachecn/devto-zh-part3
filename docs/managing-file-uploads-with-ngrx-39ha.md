# 使用 NgRx 管理文件上传

> 原文：<https://dev.to/angular/managing-file-uploads-with-ngrx-39ha>

在本文中，我们将构建一个全功能的文件上传控件，它由 **Angular** 提供支持，并由 **NgRx** 功能库提供支持。该控件将为用户提供以下功能:

*   使用`<input #file type="file" />` HTML 元素上传文件的能力。
*   能够通过`reportProgress` `HttpClient`选项查看准确的上传进度。
*   取消正在进行的上传的能力

作为一个额外的奖励，我们将简单地构建*服务器端*ASP.NET 核心 WebAPI 控制器，它将处理文件上传。

# 开始之前

在本文中，我将向您展示如何使用 NgRx 管理文件上传。如果您是 NgRx 的新手，那么我强烈建议您首先阅读我的文章， [NgRx -企业角度应用的最佳实践](https://wesleygrimes.com/angular/2018/05/30/ngrx-best-practices-for-enterprise-angular-applications.html)。我们将使用那篇文章中描述的技术来构建用于文件上传的 NgRx 组件。

如果您是 Angular 的新手，那么我建议您查看以下资源之一:

*   [终极课程](https://bit.ly/2WubqhW)
*   [官方角度文档](https://angular.io/guide/router)
*   [NgRx 文档](https://ngrx.io/docs)

# NPM 包版本

对于上下文，本文假设您正在使用以下`npm` `package.json`版本:

*   `@angular/*` : 7.2.9
*   `@ngrx/*` : 7.3.0

# 先决条件

在开始构建文件上传控件之前，请确保您已经做好了以下准备:

1.  生成了一个角度 7+的应用程序
2.  NgRx 依赖项已安装
3.  NgRx 存储在您的应用程序中。[例如，遵循本指南](https://wesleygrimes.com/angular/2018/05/30/ngrx-best-practices-for-enterprise-angular-applications.html)

* * *

# 创建上传文件服务

让我们在`Angular`中创建一个全新的服务。该服务将负责处理从客户端到服务器后端的文件上传。我们将使用惊人的 [`HttpClient`](https://angular.io/guide/http) 与`Angular`一同提供。

## 生成服务

```
$ ng g service file-upload 
```

Enter fullscreen mode Exit fullscreen mode

## 注入 HttpClient

因为我们使用`HttpClient`向后端发出请求，我们需要将它注入到我们的服务中。更新代码的`constructor`行，如下所示:

```
constructor(private httpClient: HttpClient) {} 
```

Enter fullscreen mode Exit fullscreen mode

## 为`API_BASE_URL`添加一个私有字段

> 我通常在`src/environments`区域存储`API`基本 URL。如果你有兴趣在`Angular`中了解更多关于`environments`的信息，那么看看这篇伟大的文章:[成为一个棱角分明的环保主义者](https://blog.angularindepth.com/becoming-an-angular-environmentalist-45a48f7c20d8)

让我们创建一个名为`API_BASE_URL`的新私有字段，这样我们就可以在对后端`API`的调用中使用它。

实现这一点的一种方法是执行以下操作:

```
import { environment } from 'src/environments/environment';
...
private API_BASE_URL = environment.apiBaseUrl; 
```

Enter fullscreen mode Exit fullscreen mode

## 添加一个 uploadFile 公共方法

让我们为服务创建一个名为`uploadFile`的新公共方法。该方法将接受一个参数`file: File`并返回一个`Observable<HttpEvent<{}>>`。

> 通常从这样的服务返回一个`get`或`post` `Observable<T>`。然而，在这种情况下，我们实际上将返回原始的`request`，它是一个`Observable<HttpEvent<{}>>`。
> 
> 通过返回一个原始的`request`，我们对这个过程有了更多的控制，传递像`reportProgress`这样的选项，并允许取消一个`request`。

```
public uploadFile(file: File): Observable<HttpEvent<{}>> {
  const formData = new FormData();
  formData.append('files', file, file.name);

  const options = {
    reportProgress: true
  };

  const req = new HttpRequest(
    'POST',
    `${this.API_BASE_URL}/api/file`,
    formData,
    options
  );
  return this.httpClient.request(req);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 完成文件上传服务

完成的`file-upload.service.ts`将如下所示:

```
import { HttpClient, HttpEvent, HttpRequest } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';
import { environment } from 'src/environments/environment';

@Injectable({
  providedIn: 'root'
})
export class FileUploadService {
  private API_BASE_URL = environment.apiBaseUrl;

  constructor(private httpClient: HttpClient) {}

  public uploadFile(file: File): Observable<HttpEvent<{}>> {
    const formData = new FormData();
    formData.append('files', file, file.name);

    const options = {
      reportProgress: true
    };

    const req = new HttpRequest(
      'POST',
      `${this.API_BASE_URL}/api/file`,
      formData,
      options
    );
    return this.httpClient.request(req);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 创建上传文件特征库

为了让你的 **NgRx** 商店有条理，我建议创建一个单独的上传文件功能商店。让我们将它们打包在一个名为`upload-file-store.module.ts`的模块中，并保存在一个名为`upload-file-store`的子目录下。

## 创建特征存储模块

使用以下命令创建一个特性存储模块:

```
$ ng g module upload-file-store --flat false 
```

Enter fullscreen mode Exit fullscreen mode

## 创建状态界面

在`upload-file-store`文件夹下创建一个新文件，命名为`state.ts`。该文件的内容如下:

> 我们正在使用一种相对较新的技术，我们将设置一个`enum`来跟踪状态。这个`enum`将反映上传过程的当前状态。关于这种方法的更多信息，请查看[亚历克斯·奥克鲁什科的文章](https://blog.angularindepth.com/ngrx-how-and-where-to-handle-loading-and-error-states-of-ajax-calls-6613a14f902d)。

```
export enum UploadStatus {
  Ready = 'Ready',
  Requested = 'Requested',
  Started = 'Started',
  Failed = 'Failed',
  Completed = 'Completed'
}

export interface State {
  status: UploadStatus;
  error: string | null;
  progress: number | null;
}

export const initialState: State = {
  status: UploadStatus.Ready,
  error: null,
  progress: null
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 创建特征动作

> 如果你想了解更多关于 **NgRx 行动**的信息，请查阅[官方文件](https://ngrx.io/guide/store/actions)。

在`upload-file-store`文件夹下创建一个新文件，命名为`actions.ts`。该文件将保存我们希望在此存储上可用的操作。

我们将在我们的功能存储上创建以下操作:

*   `UPLOAD_REQUEST` -这个动作是从文件上传表单调度的，它的有效负载将包含实际上传的`File`。

*   `UPLOAD_CANCEL` -当点击取消按钮时，该动作从文件上传表单中发出。这将用于取消正在进行的上传。

*   `UPLOAD_RESET` -当点击重置按钮时，从文件上传表单调度该动作。这将用于将存储的状态重置为默认值。

*   `UPLOAD_STARTED` -当 API 报告`HttpEventType.Sent`事件时，从文件上传效果`HttpClient`调度该动作。

*   `UPLOAD_PROGRESS` -当 API 报告`HttpEventType.UploadProgress`事件时，从文件上传效果`HttpClient`调度该动作。有效负载将包含整数形式的进度百分比。

*   `UPLOAD_FAILURE` -当 API 返回一个错误，或者有一个`HttpEventType.ResponseHeader`或者有一个`event.status !== 200`的`HttpEventType.Response`，或者当一个未知的`HttpEventType`被返回时，这个动作从文件上传效果被调度。有效负载将包含从 API 返回的特定错误消息，并将其放入存储中的一个`error`字段。

*   `UPLOAD_COMPLETED` -当 API 报告一个`HttpEventType.ResponseHeader`或`HttpEventType.Response`事件`event.status === 200`时，从文件上传效果中调度该动作。没有有效负载，因为 API 只是返回一个`200 OK`响应。

最终的`actions.ts`文件将如下所示:

```
import { Action } from '@ngrx/store';

export enum ActionTypes {
  UPLOAD_REQUEST = '[File Upload Form] Request',
  UPLOAD_CANCEL = '[File Upload Form] Cancel',
  UPLOAD_RESET = '[File Upload Form] Reset',
  UPLOAD_STARTED = '[File Upload API] Started',
  UPLOAD_PROGRESS = '[File Upload API] Progress',
  UPLOAD_FAILURE = '[File Upload API] Failure',
  UPLOAD_COMPLETED = '[File Upload API] Success'
}

export class UploadRequestAction implements Action {
  readonly type = ActionTypes.UPLOAD_REQUEST;
  constructor(public payload: { file: File }) {}
}

export class UploadCancelAction implements Action {
  readonly type = ActionTypes.UPLOAD_CANCEL;
}

export class UploadResetAction implements Action {
  readonly type = ActionTypes.UPLOAD_RESET;
}

export class UploadStartedAction implements Action {
  readonly type = ActionTypes.UPLOAD_STARTED;
}

export class UploadProgressAction implements Action {
  readonly type = ActionTypes.UPLOAD_PROGRESS;
  constructor(public payload: { progress: number }) {}
}

export class UploadFailureAction implements Action {
  readonly type = ActionTypes.UPLOAD_FAILURE;
  constructor(public payload: { error: string }) {}
}

export class UploadCompletedAction implements Action {
  readonly type = ActionTypes.UPLOAD_COMPLETED;
}

export type Actions =
  | UploadRequestAction
  | UploadCancelAction
  | UploadResetAction
  | UploadStartedAction
  | UploadProgressAction
  | UploadFailureAction
  | UploadCompletedAction; 
```

Enter fullscreen mode Exit fullscreen mode

## 创建特征减速器

> 如果你想了解更多关于 **NgRx 减速器**的信息，请查阅[官方文件](https://ngrx.io/guide/store/reducers)。

在`upload-file-store`文件夹下创建一个新文件，命名为`reducer.ts`。这个文件将保存我们创建的 reducer 来管理到存储的状态转换。

对于上述动作，我们将按如下方式处理状态转换:

*   `UPLOAD_REQUEST` -复位状态，除了将`state.status`设置为`UploadStatus.Requested`。

*   `UPLOAD_CANCEL` -重置状态树。我们的效果将监听任何`UPLOAD_CANCEL`事件调度，因此不需要特定的状态字段。

*   `UPLOAD_RESET` -重置该动作的状态树。

*   `UPLOAD_FAILURE` -重置状态树，除了将`state.status`设置为`UploadStatus.Failed`，将`state.error`设置为从`uploadRequestEffect`效果中的`API`扔进`catchError`的`error`。

*   `UPLOAD_STARTED` -将`state.progress`设置为`0`，将`state.status`设置为`UploadStatus.Started`。

*   `UPLOAD_PROGRESS` -将`state.progress`设置为动作提供的电流`action.payload.progress`。

*   `UPLOAD_COMPLETED` -重置状态树，除了将`state.status`设置为`UploadStatus.Completed`，以便 UI 可以显示成功消息。

```
import { Actions, ActionTypes } from './actions';
import { initialState, State, UploadStatus } from './state';

export function featureReducer(state = initialState, action: Actions): State {
  switch (action.type) {
    case ActionTypes.UPLOAD_REQUEST: {
      return {
        ...state,
        status: UploadStatus.Requested,
        progress: null,
        error: null
      };
    }
    case ActionTypes.UPLOAD_CANCEL: {
      return {
        ...state,
        status: UploadStatus.Ready,
        progress: null,
        error: null
      };
    }
    case ActionTypes.UPLOAD_RESET: {
      return {
        ...state,
        status: UploadStatus.Ready,
        progress: null,
        error: null
      };
    }
    case ActionTypes.UPLOAD_FAILURE: {
      return {
        ...state,
        status: UploadStatus.Failed,
        error: action.payload.error,
        progress: null
      };
    }
    case ActionTypes.UPLOAD_STARTED: {
      return {
        ...state,
        status: UploadStatus.Started,
        progress: 0
      };
    }
    case ActionTypes.UPLOAD_PROGRESS: {
      return {
        ...state,
        progress: action.payload.progress
      };
    }
    case ActionTypes.UPLOAD_COMPLETED: {
      return {
        ...state,
        status: UploadStatus.Completed,
        progress: 100,
        error: null
      };
    }
    default: {
      return state;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建特征效果

> 如果你想了解更多关于 **NgRx 效果**的信息，请查阅[官方文件](https://ngrx.io/guide/effects)。

在`upload-file-store`文件夹下创建一个新文件，命名为`effects.ts`。这个文件将保存我们创建的效果，以处理对后端`API`服务的任何副作用调用。这种效果是应用程序中最神奇的地方。

### 注入依赖关系

让我们将必要的依赖项添加到我们的`constructor`中，如下所示:

```
constructor(
  private fileUploadService: FileUploadService,
  private actions$: Actions<fromFileUploadActions.Actions>
) {} 
```

Enter fullscreen mode Exit fullscreen mode

### 添加新的上传请求效果

> 效果大量使用`RxJS`概念和主题。如果你是第一次接触`RxJS`，那么我建议你查看一下[的官方文件](https://rxjs.dev)

让我们在名为`uploadRequestEffect$`的文件中创建新的效果。

关于这种效应的一些评论:

*   监听`UPLOAD_REQUEST`动作，然后调用`fileUploadService.uploadFile`服务方法来启动上传过程。

*   在这里使用 [`concatMap`](https://rxjs.dev/api/operators/concatMap) RxJS 操作符，以便多个文件上传请求排队，并按照它们被分派的顺序进行处理。

*   使用 [`takeUntil`](https://rxjs.dev/api/operators/takeUntil) RxJS 操作符监听要调度的`UPLOAD_CANCEL`动作。这允许我们**短路**任何正在传输的请求。

*   使用 [`map`](https://rxjs.dev/api/operators/map) RxJS 操作符将特定的`HttpEvent`响应映射到我们在`Store`中定义的调度特定的`Actions`。

*   使用 [`catchError`](https://rxjs.dev/api/operators/catchError) RxJS 操作符处理`HttpClient`可能抛出的任何错误。

效果看起来会像这样:

```
@Effect()
uploadRequestEffect$: Observable<Action> = this.actions$.pipe(
  ofType(fromFileUploadActions.ActionTypes.UPLOAD_REQUEST),
  concatMap(action =>
    this.fileUploadService.uploadFile(action.payload.file).pipe(
      takeUntil(
        this.actions$.pipe(
          ofType(fromFileUploadActions.ActionTypes.UPLOAD_CANCEL)
        )
      ),
      map(event => this.getActionFromHttpEvent(event)),
      catchError(error => of(this.handleError(error)))
    )
  )
); 
```

Enter fullscreen mode Exit fullscreen mode

### 添加 getActionFromHttpEvent 私有方法

> 如需了解更多关于收听进展事件的信息，请点击此处查看[官方文档指南](https://angular.io/guide/http#listening-to-progress-events)。

该方法将负责将特定的`HttpEventType`映射到被分派的特定的`Action`。

*   `HttpEventType.Sent` -此事件在上传过程开始时发生。我们将发送一个`UPLOAD_STARTED`动作来表示流程已经开始。

*   `HttpEventType.UploadProgress` -当上传过程取得进展时发生此事件。我们将分派一个负载为`progress: Math.round((100 * event.loaded) / event.total)`的`UPLOAD_PROGRESS`动作来计算上传的实际完成百分比。这是因为`HttpClient`以整数格式返回一个`event.loaded`和`event.total`属性。

*   `HttpEventType.Response` / `HttpEventType.ResponseHeader` -这些事件在上传过程完成时发生。需要注意的是，这可能是成功也可能是失败，所以我们需要询问`event.status`来检查`200`。如果`event.status === 200`和`UPLOAD_FAILURE`作为错误有效载荷传递给`event.status !== 200`，我们将调度`UPLOAD_COMPLETED`动作。

*   所有其他(默认情况)-我们将任何其他可能返回的事件视为错误，因为它们是意外的行为。我们将调度一个`UPLOAD_FAILURE`动作，通过`JSON.stringify`运行`event`的有效负载。

```
private getActionFromHttpEvent(event: HttpEvent<any>) {
  switch (event.type) {
    case HttpEventType.Sent: {
      return new fromFileUploadActions.UploadStartedAction();
    }
    case HttpEventType.UploadProgress: {
      return new fromFileUploadActions.UploadProgressAction({
        progress: Math.round((100 * event.loaded) / event.total)
      });
    }
    case HttpEventType.ResponseHeader:
    case HttpEventType.Response: {
      if (event.status === 200) {
        return new fromFileUploadActions.UploadCompletedAction();
      } else {
        return new fromFileUploadActions.UploadFailureAction({
          error: event.statusText
        });
      }
    }
    default: {
      return new fromFileUploadActions.UploadFailureAction({
        error: `Unknown Event: ${JSON.stringify(event)}`
      });
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 添加 handleError 私有方法

> 关于处理`HttpClient`错误的更多信息，请点击查看[官方文档指南。](https://angular.io/guide/http#getting-error-details)

这个方法将负责处理在请求期间从`HttpClient`抛出的任何错误。我正在利用 npm 的一个名为`serialize-error`的简洁库，不管抛出什么类型的错误，它都会给我一个可预测的`error.message`。

如此安装库:

```
$ npm install serialize-error 
```

Enter fullscreen mode Exit fullscreen mode

```
import serializeError from 'serialize-error';
...
private handleError(error: any) {
  const friendlyErrorMessage = serializeError(error).message;
  return new fromFileUploadActions.UploadFailureAction({
    error: friendlyErrorMessage
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

### 完成特征效果

完成后的效果看起来会像这样:

```
import { HttpEvent, HttpEventType } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Actions, Effect, ofType } from '@ngrx/effects';
import { Action } from '@ngrx/store';
import { Observable, of } from 'rxjs';
import { catchError, concatMap, map, takeUntil } from 'rxjs/operators';
import serializeError from 'serialize-error';
import { FileUploadService } from 'src/app/_services';
import * as fromFileUploadActions from './actions';

@Injectable()
export class UploadFileEffects {
  @Effect()
  uploadRequestEffect$: Observable<Action> = this.actions$.pipe(
    ofType(fromFileUploadActions.ActionTypes.UPLOAD_REQUEST),
    concatMap(action =>
      this.fileUploadService.uploadFile(action.payload.file).pipe(
        takeUntil(
          this.actions$.pipe(
            ofType(fromFileUploadActions.ActionTypes.UPLOAD_CANCEL)
          )
        ),
        map(event => this.getActionFromHttpEvent(event)),
        catchError(error => of(this.handleError(error)))
      )
    )
  );

  constructor(
    private fileUploadService: FileUploadService,
    private actions$: Actions<fromFileUploadActions.Actions>
  ) {}

  private getActionFromHttpEvent(event: HttpEvent<any>) {
    switch (event.type) {
      case HttpEventType.Sent: {
        return new fromFileUploadActions.UploadStartedAction();
      }
      case HttpEventType.UploadProgress: {
        return new fromFileUploadActions.UploadProgressAction({
          progress: Math.round((100 * event.loaded) / event.total)
        });
      }
      case HttpEventType.ResponseHeader:
      case HttpEventType.Response: {
        if (event.status === 200) {
          return new fromFileUploadActions.UploadCompletedAction();
        } else {
          return new fromFileUploadActions.UploadFailureAction({
            error: event.statusText
          });
        }
      }
      default: {
        return new fromFileUploadActions.UploadFailureAction({
          error: `Unknown Event: ${JSON.stringify(event)}`
        });
      }
    }
  }

  private handleError(error: any) {
    const friendlyErrorMessage = serializeError(error).message;
    return new fromFileUploadActions.UploadFailureAction({
      error: friendlyErrorMessage
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建特征选择器

> 如果你想了解更多关于 **NgRx 选择器**的信息，请查阅[官方文件](https://ngrx.io/guide/store/selectors)。

在`upload-file-store`文件夹下创建一个新文件，命名为`selectors.ts`。这个文件将保存选择器，我们将使用这些选择器从存储中提取特定的状态片段。这些在技术上不是必需的，但是强烈鼓励。选择器通过使用`MemoizedSelector`包装器来提高应用程序的性能。选择器还简化了 UI 逻辑。

我们将为状态的每个重要属性创建一个选择器。这包括以下属性:

*   `state.status` -由于这是一个`enum`，我们将为每个`enum`选项创建一个选择器。
*   `state.error`
*   `state.progress`

完成后的选择器文件看起来如下:

```
import {
  createFeatureSelector,
  createSelector,
  MemoizedSelector
} from '@ngrx/store';
import { State, UploadStatus } from './state';

const getError = (state: State): string => state.error;

const getStarted = (state: State): boolean =>
  state.status === UploadStatus.Started;

const getRequested = (state: State): boolean =>
  state.status === UploadStatus.Requested;

const getReady = (state: State): boolean => state.status === UploadStatus.Ready;

const getProgress = (state: State): number => state.progress;

const getInProgress = (state: State): boolean =>
  state.status === UploadStatus.Started && state.progress >= 0;

const getFailed = (state: State): boolean =>
  state.status === UploadStatus.Failed;

const getCompleted = (state: State): boolean =>
  state.status === UploadStatus.Completed;

export const selectUploadFileFeatureState: MemoizedSelector<
  object,
  State
> = createFeatureSelector<State>('uploadFile');

export const selectUploadFileError: MemoizedSelector<
  object,
  string
> = createSelector(
  selectUploadFileFeatureState,
  getError
);

export const selectUploadFileReady: MemoizedSelector<
  object,
  boolean
> = createSelector(
  selectUploadFileFeatureState,
  getReady
);

export const selectUploadFileRequested: MemoizedSelector<
  object,
  boolean
> = createSelector(
  selectUploadFileFeatureState,
  getRequested
);

export const selectUploadFileStarted: MemoizedSelector<
  object,
  boolean
> = createSelector(
  selectUploadFileFeatureState,
  getStarted
);

export const selectUploadFileProgress: MemoizedSelector<
  object,
  number
> = createSelector(
  selectUploadFileFeatureState,
  getProgress
);

export const selectUploadFileInProgress: MemoizedSelector<
  object,
  boolean
> = createSelector(
  selectUploadFileFeatureState,
  getInProgress
);

export const selectUploadFileFailed: MemoizedSelector<
  object,
  boolean
> = createSelector(
  selectUploadFileFeatureState,
  getFailed
);

export const selectUploadFileCompleted: MemoizedSelector<
  object,
  boolean
> = createSelector(
  selectUploadFileFeatureState,
  getCompleted
); 
```

Enter fullscreen mode Exit fullscreen mode

## 更新特征模块

我们现在需要更新功能模块`UploadFileStoreModule`来连接商店。

完成的`UploadFileStoreModule`应该类似于下面的内容:

```
import { CommonModule } from '@angular/common';
import { NgModule } from '@angular/core';
import { EffectsModule } from '@ngrx/effects';
import { StoreModule } from '@ngrx/store';
import { UploadFileEffects } from './effects';
import { featureReducer } from './reducer';

@NgModule({
  declarations: [],
  imports: [
    CommonModule,
    StoreModule.forFeature('uploadFile', featureReducer),
    EffectsModule.forFeature([UploadFileEffects])
  ]
})
export class UploadFileStoreModule {} 
```

Enter fullscreen mode Exit fullscreen mode

### 在需要的地方导入该模块

确保在需要的地方导入这个新的`UploadFileStoreModule`。在这个例子中，我们将把它导入到`AppModule`中，因为我们没有任何延迟加载的特性。

### 更新你的 AppModule 来导入商店&的效果

最后，确保你更新了你的`AppModule`来导入`StoreModule.forRoot`和`EffectsModule.forRoot`。

更新后的`AppModule`可能如下所示:

```
import { HttpClientModule } from '@angular/common/http';
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { EffectsModule } from '@ngrx/effects';
import { StoreModule } from '@ngrx/store';
import { StoreDevtoolsModule } from '@ngrx/store-devtools';
import { environment } from 'src/environments/environment';
import { AppComponent } from './app.component';
import { UploadFileStoreModule } from './upload-file-store/upload-file-store.module';

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    HttpClientModule,
    StoreModule.forRoot({}),
    EffectsModule.forRoot([]),
    StoreDevtoolsModule.instrument({
      maxAge: 25, // Retains last 25 states
      logOnly: environment.production // Restrict extension to log-only mode
    }),
    UploadFileStoreModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 让我们回顾到此为止

*   至此，我们已经创建了一个新的`FileUploadService`，它调用我们的后端`API`来上传一个`File`对象。

*   我们还创建了一个新的`UploadFileStore`特性库，它提供了`Actions`、`Reducer`、`Effects`和`Selectors`来管理文件上传过程。

*   最后，商店已经被导入到我们的`AppModule`中使用。

现在我们已经为自己打下了基础，我们可以将注意力转向用户界面，并为我们创建的用于管理流程的`UploadFileStore`连接一个新组件。

这将是有趣的部分！

* * *

# 创建上传文件组件

让我们从创造一个全新的`Component`开始。这一部分将包括以下内容:

*   一个`input`元素，供用户上传文件时使用。`change`事件将调度`UploadFileStoreActions.UploadRequest()`动作

*   连接到`UploadFileStoreSelectors.selectUploadFileProgress`选择器的进度百分比，用于实时进度

*   一个取消上传按钮来调度`UploadFileStoreActions.UploadCancelRequest()`动作

*   一个上传另一个文件按钮来调度`UploadFileStoreActions.UploadResetRequest()`动作并允许新文件上传

> 附注:这可能是创建一个带有哑组件的连接容器的好场景，但是为了本文的简洁，我将把它们组合在一起展示。在示例存储库中，我将展示这两种场景。

## 生成组件

> [点击此处](https://angular.io/cli)了解使用强大的 Angular CLI
> 的更多详情

```
$ ng g component upload-file 
```

Enter fullscreen mode Exit fullscreen mode

> 为了简化本文，我们将只显示进度百分比，这可以很容易地适应挂钩到进度条控件的`value`属性，就像 Angular 素材库提供的那样。

## 更新组件*。ts 文件

### 注入商店

我们需要将我们的商店连接到这个组件中以供使用。先从把店铺注入`constructor`开始。完成后的`constructor`应该是这样的:

```
...
constructor(private store$: Store<fromFileUploadState.State>) {} 
```

Enter fullscreen mode Exit fullscreen mode

### 从状态连接我们的选择器

让我们在组件上创建六(6)个公共字段。一个好的做法是将`$`作为后缀，这样你就知道这些是`Observable`并且必须在模板中订阅。

```
completed$: Observable<boolean>;
progress$: Observable<number>;
error$: Observable<string>;

isInProgress$: Observable<boolean>;
isReady$: Observable<boolean>;
hasFailed$: Observable<boolean>; 
```

Enter fullscreen mode Exit fullscreen mode

让我们在生命周期挂钩中将这些与商店挂钩。

```
ngOnInit() {
  this.completed$ = this.store$.pipe(
    select(fromFileUploadSelectors.selectUploadFileCompleted)
  );

  this.progress$ = this.store$.pipe(
    select(fromFileUploadSelectors.selectUploadFileProgress)
  );

  this.error$ = this.store$.pipe(
    select(fromFileUploadSelectors.selectUploadFileError)
  );

  this.isInProgress$ = this.store$.pipe(
    select(fromFileUploadSelectors.selectUploadFileInProgress)
  );

  this.isReady$ = this.store$.pipe(
    select(fromFileUploadSelectors.selectUploadFileReady)
  );

  this.hasFailed$ = this.store$.pipe(
    select(fromFileUploadSelectors.selectUploadFileFailed)
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

### 连接我们的行动调度程序

让我们添加`uploadFile`、`resetUpload`和`cancelUpload`方法来连接我们的按钮点击以调度商店中的动作。

```
uploadFile(event: any) {
  const files: FileList = event.target.files;
  const file = files.item(0);

  this.store$.dispatch(
    new fromFileUploadActions.UploadRequestAction({
      file
    })
  );

  // clear the input form
  event.srcElement.value = null;
}

resetUpload() {
  this.store$.dispatch(new UploadFileStoreActions.UploadResetAction());
}

cancelUpload() {
  this.store$.dispatch(new UploadFileStoreActions.UploadCancelAction());
} 
```

Enter fullscreen mode Exit fullscreen mode

### 成品组件*。ts 文件

成品部件*。ts 文件应该类似于以下内容:

```
import { Component, OnInit } from '@angular/core';
import { select, Store } from '@ngrx/store';
import { Observable } from 'rxjs';
import * as fromFileUploadActions from 'src/app/upload-file-store/actions';
import * as fromFileUploadSelectors from 'src/app/upload-file-store/selectors';
import * as fromFileUploadState from 'src/app/upload-file-store/state';

@Component({
  selector: 'app-upload-file',
  templateUrl: './upload-file.component.html',
  styleUrls: ['./upload-file.component.css']
})
export class UploadFileComponent implements OnInit {
  completed$: Observable<boolean>;
  progress$: Observable<number>;
  error$: Observable<string>;
  isInProgress$: Observable<boolean>;
  isReady$: Observable<boolean>;
  hasFailed$: Observable<boolean>;

  constructor(private store$: Store<fromFileUploadState.State>) {}

  ngOnInit() {
    this.completed$ = this.store$.pipe(
      select(fromFileUploadSelectors.selectUploadFileCompleted)
    );

    this.progress$ = this.store$.pipe(
      select(fromFileUploadSelectors.selectUploadFileProgress)
    );

    this.error$ = this.store$.pipe(
      select(fromFileUploadSelectors.selectUploadFileError)
    );

    this.isInProgress$ = this.store$.pipe(
      select(fromFileUploadSelectors.selectUploadFileInProgress)
    );

    this.isReady$ = this.store$.pipe(
      select(fromFileUploadSelectors.selectUploadFileReady)
    );

    this.hasFailed$ = this.store$.pipe(
      select(fromFileUploadSelectors.selectUploadFileFailed)
    );
  }

  uploadFile(event: any) {
    const files: FileList = event.target.files;
    const file = files.item(0);

    this.store$.dispatch(
      new fromFileUploadActions.UploadRequestAction({
        file
      })
    );

    // clear the input form
    event.srcElement.value = null;
  }

  resetUpload() {
    this.store$.dispatch(new fromFileUploadActions.UploadResetAction());
  }

  cancelUpload() {
    this.store$.dispatch(new fromFileUploadActions.UploadCancelAction());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更新组件*。html 模板

我们将向上传文件组件添加五(5)个主要部分。

### 添加输入字段

没有上传文件按钮，相反我们将利用内置的输入组件并挂钩到`change`事件。每当一个文件被添加到表单中，这个事件就会被触发。我们也只希望在接受新文件上传时显示该表单，即该表单失败或就绪。我们将使用`*ngIf`结构指令来帮助引用我们的`isReady$`和`hasFailed$`可观察对象。

```
<div class="message" *ngIf="(isReady$ | async) || (hasFailed$ | async)">
  <input #file type="file" multiple (change)="uploadFile($event)" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 添加进度消息

当进度大于或等于 0%且`UploadStatus`为`Failed`时，将显示此信息。我们将使用`isInProgress$`选择器值，使用`*ngIf`来显示它是否处于这种状态。我们将进度消息的文本设置为`progress$`选择器值。

```
<div class="message" *ngIf="(isInProgress$ | async)">
  <div style="margin-bottom: 14px;">Uploading... {{ progress$ | async }}%</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 添加取消上传按钮

该按钮将利用`*ngIf`仅在使用`isInProgress$`选择器值上传正在进行时显示。点击事件将触发`UploadCancelAction`的调度。

```
<div class="message" *ngIf="(isInProgress$ | async)">
  <button (click)="cancelUpload()">Cancel Upload</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 添加重置上传按钮

该按钮将利用`*ngIf`仅在使用`completed$`选择器值完成上传时显示。点击事件将触发`UploadResetAction`的调度。

```
<div class="message" *ngIf="(completed$ | async)">
  <h4>
    File has been uploaded successfully!
  </h4>
  <button (click)="resetUpload()">Upload Another File</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 添加错误信息

该按钮将利用`*ngIf`仅在`hasFailed$`选择器值返回`true`时显示。实际错误信息来自`error$`选择器值。

```
<div class="message error" *ngIf="(hasFailed$ | async)">
  Error: {{ error$ | async }}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 成品组件*。html 文件

```
<div class="message" *ngIf="(isReady$ | async) || (hasFailed$ | async)">
  <input #file type="file" multiple (change)="uploadFile($event)" />
</div>

<div class="message" *ngIf="(isInProgress$ | async)">
  <div style="margin-bottom: 14px;">Uploading... {{ progress$ | async }}%</div>
</div>

<div class="message" *ngIf="(isInProgress$ | async)">
  <button (click)="cancelUpload()">Cancel Upload</button>
</div>

<div class="message" *ngIf="(completed$ | async)">
  <h4>
    File has been uploaded successfully!
  </h4>
  <button (click)="resetUpload()">Upload Another File</button>
</div>

<div class="message error" *ngIf="(hasFailed$ | async)">
  Error: {{ error$ | async }}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 给我们的组件*添加一些样式。css 文件

为了格式化，让我们向组件样式表添加几个简单的类:

```
.message {
  margin-bottom: 15px;
}

.error {
  color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 将组件添加到我们的 AppComponent

出于本文的目的，我们将把新的`UploadFileComponent`组件添加到我们的`AppComponent`中。该模板将如下所示:

```
<app-upload-file></app-upload-file> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# (附加功能)后端休息终点

要获得完整的模拟后端服务器，请点击这里查看我的[repository:

*   [github.com/wesleygrimes/aspnetcore-mock-file-upload-server](https://github.com/wesleygrimes/aspnetcore-mock-file-upload-server)

对于那些已经走到这一步的勇敢的人...您可能会问后端`API`端点看起来像什么。嗯，这里有一个`ASP.NET Core` `Controller`免费提供的例子；——)

```
public class FileController : ControllerBase
{
    [HttpPost("")]
    public async Task<IActionResult> Post(List<IFormFile> files)
    {
        try
        {
            foreach (var file in files)
            {
                Console.WriteLine($"Begin Uploaded File: {file.FileName}");

                //simulate upload
                Task.Delay(5000).Wait();

                Console.WriteLine($"Finished Uploaded File: {file.FileName}");
            }

            return Ok();
        }
        catch (Exception ex)
        {
            return BadRequest($"Unable to upload file(s).");
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# GitHub 范例库

我总是喜欢在文章后面提供工作代码示例。您可以在以下存储库中找到这篇文章的配套应用程序:

*   [github.com/wesleygrimes/ngrx-file-upload](https://github.com/wesleygrimes/ngrx-file-upload)

# 结论

重要的是要记住，我已经在几个“真实世界”的应用程序中实现了这些最佳实践。虽然我发现这些最佳实践很有帮助，并且可维护，但我不认为它们是 NgRx 项目的终极解决方案；我就是这么做的。我很好奇你们都是怎么想的？在使用 NgRx 构建企业级 Angular 应用程序时，请随时提供您学到的任何建议、技巧或最佳实践，我将更新文章以反映这一点。编码快乐！

* * *

# 附加资源

我强烈推荐参加终极角度课程，尤其是 NgRx 课程。它物有所值，我已经把它作为新的 Angular 开发者的培训工具。点击下面的链接注册。

[终极课程:JavaScript、Angular、NGRX 和 TypeScript 专家在线课程](https://bit.ly/2WubqhW)