# 在 Angular 7 中查询和更新 Firestore 文档

> 原文：<https://dev.to/crazedvic/query--update-firestore-documents-in-angular-7-5fhg>

2019 年 2 月 14 日

当我继续构建时间跟踪系统时，我遇到了挑战。挑战如此恼人，以至于我不可避免地不得不连续几个小时在 stackoverflow 中搜寻。拼命搜索相关知识，最终找到可行的解决方案。现在我在这里分享这些最新的发现。

所以我有一个星期的导航控制。用户可以在一年中的每一周向前或向后移动。我键入年周和用户 id 的时间表。当用户点击前进或后退时，它应该加载正确的时间表(如果存在的话)并填充表单(正如我在上一篇文章中所讨论的)。然后，如果用户对时间表进行更新，它应该更新 Firestore 中的文档。很简单。

所以这里的关键需求是:通过查询而不是 id 来访问文档，显示文档的数据，引用底层文档，这样我就可以更新它，更快，并最大限度地减少到服务器的行程，这符合我的钱包的利益。

下面是我的客户端界面，供参考:

```
export interface Client {
    name: string;
    phone: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我为此需要的变量，在 AppComponent.ts 中声明:

```
 private clientRef: DocumentReference;
    clients: Observable<any[]>;
    myClient: Client; 
```

Enter fullscreen mode Exit fullscreen mode

快速解释:

*   clientRef =将保存对所选客户端的文档引用
*   clients =将保存所有客户端的列表以供选择
*   myClient =将是当前选定的客户端

下面是 html 表单，AppComponent.html:

```
<ul>
    <li class="text" *ngFor="let client of clients | async">
        <a href="#" (click)="selectClient(client)">{{client.name}}</a>
    </li>
</ul>
<div>Selected Client: {{myClient?.name}}</div>
<button (click)="updateSelectedClient()">Update Client</button> 
```

Enter fullscreen mode Exit fullscreen mode

因此，基本上加载所有客户端，单击客户端名称选择它。它将显示为选定的客户端，然后单击“更新客户端”以实时查看列表和 Firestore 控制台中的客户端更新。

当用户点击一个客户端名称时，我实际上是要通过客户端名称执行查询，因为我没有 ID。这是有意的，因为我的实际用例，如简介中所述。下面是发生的情况:

```
async selectClient(client: Client) {

     const snapshotResult = await this.db.collection('clients', ref =>
        ref.where('name', '==', client.name)
           .limit(1))
           .snapshotChanges()
           .pipe(flatMap(clients => clients)); 

        snapshotResult.subscribe(doc => {
            this.myClient = <Client>doc.payload.doc.data();
            this.clientRef = doc.payload.doc.ref;
        }); 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到，我将结果通过管道传输到 flatMap 中，这将结果数组展平，因为它只有一个结果，这使得剩余的代码更加简洁。我等待 snapshotResult 返回，然后将我的 DocumentReference 分配给 clientRef，并用文档数据填充 myClient。

最后为了测试这是否允许我更新，我创建了下面的函数:

```
updateSelectedClient() {
  this.myClient.name = this.myClient.name + '.';
  this.clientRef.update(this.myClient);
} 
```

Enter fullscreen mode Exit fullscreen mode

我只是在客户端名称的末尾添加了一个句点，以确保这是有效的。单击 UpdateClient 按钮，我可以看到所选的客户端标签、Firestore 文档和列表中的名称会实时更新。

附录

您可以通过使用 BehaviorSubject 获得相同的结果，因此单击客户端只是更改 Subject 的值，这反过来将触发查询。由于我目前的一年一周已经是一个主题，我想我应该快速添加这个。

```
private _currentClient: BehaviorSubject<string>; 
```

Enter fullscreen mode Exit fullscreen mode

然后在构造函数
中初始化它

```
this._currentClient = new BehaviorSubject(''); 
```

Enter fullscreen mode Exit fullscreen mode

添加一个 ngOnInit，订阅主题

```
ngOnInit() {
        this._currentClient.subscribe((val: string) => {
            this.snapshotResult = this.db.collection('clients', ref =>
                ref.where('name', '==', val).limit(1)).snapshotChanges()
                .pipe(flatMap(clients => clients));

            this.snapshotResult.subscribe(doc => {
                this.myClient = <Client>doc.payload.doc.data();
                this.clientRef = doc.payload.doc.ref;
            });
        });
    } 
```

Enter fullscreen mode Exit fullscreen mode

并将 selectClient()函数更改为:

```
async selectClient(client: Client) {
        this._currentClient.next(client.name);
} 
```

Enter fullscreen mode Exit fullscreen mode

总体功能保持不变，只是使用了 SubjectBehavior。