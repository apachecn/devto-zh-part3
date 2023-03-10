# 构建支持用户上传的 Phoenix 应用程序的分步教程

> 原文：<https://dev.to/alvisesus/step-by-step-tutorial-to-build-a-phoenix-app-that-supports-user-uploads-345p>

有兴趣了解 Elixir、Phoenix 和软件架构吗？[订阅我的时事通讯，了解一般思考和每周深入的方法](https://www.poeticoding.com/subscribe-to-newsletter/)。

有许多方法可以让用户将他们的文件上传到我们的 Phoenix 应用程序，包括为我们管理上传的可靠库，将文件安全地保存在云存储中。

然而，在本教程中，我们将专注于基础——我们将只使用 Phoenix 附带的工具，而不是第三方库。

我们将从头开始创建一个全功能的 Phoenix 应用程序，运行并配置我们的 Postgres 数据库和 Ecto 模式，使用 Plug 处理上传，并将文件存储在本地。

完成后，我们就可以下载文件了。

现在，让我们开始编码吧！👩‍💻👨‍💻

# 新凤凰 app

让我们首先创建一个名为*诗意*
的新凤凰 1.4 应用程序

```
$ mix phx.new poetic && cd poetic 
```

创建后，确保正确下载依赖关系

```
$ mix deps.get
Resolving Hex dependencies... 
```

我们的系统中需要 [Node.js](https://nodejs.org) ，这样我们就可以在`assets`目录中构建资产。我发现如果你需要在同一台机器上处理不同的节点版本，nvm 非常有用并且容易使用。

如果你只是想编译这些资产，可以从 [Node.js 网站](https://nodejs.org)下载最新版本的安装程序。一旦你安装了 Node.js 的最新版本，你就可以使用`npm install`和`webpack.js`命令
来构建资产

```
$ cd assets && npm install && node node_modules/webpack/bin/webpack.js --mode development 
```

# Postgres🐘使用 Docker🐳

我们刚刚创建的 Phoenix 应用程序带有 [Ecto](https://hexdocs.pm/ecto/Ecto.html) ，它帮助我们处理关系数据库，如 [PostgreSQL](https://www.postgresql.org/) 。需要一个数据库来存储上传文件的详细信息。

我认为，在这种情况下，使用 Docker 是在我们的本地系统上启动和运行 PostgreSQL 数据库的最简单、最快的方法。

如果你不确定如何使用 docker，可以看看[如何运行 Docker 容器](https://www.poeticoding.com/how-to-run-a-docker-container/)资源，初步了解如何运行 Docker 容器。

首先，我们创建一个 docker 卷，以保持数据库数据的持久性

```
$ docker volume create poetic-postgres` 
```

有了卷，我们可以安全地销毁和重新创建容器，而不会导致任何数据丢失。

然后，我们启动一个容器，运行官方 Postgres docker 映像，版本 *11-alpine* 。

```
$ docker container run --name postgres -p 5432:5432 -e POSTGRES_PASSWORD=postgres -v poetic-postgres:/var/lib/postgresql/data --rm postgres:11-alpine
LOG:  listening on IPv4 address "0.0.0.0", port 5432
LOG:  database system is ready to accept connections 
```

我们已经用这些选项启动了一个 Postgres 服务器:

*   `-p 5432:5432`表示`--publish port_local_machine:port_container`。本地机器中的端口被打开，连接被转发到容器端口。
*   `-e POSTGRES_PASSWORD=postgres`我们通过环境变量`POSTGRES_PASSWORD`将密码`postgres`设置为默认的`postgres`用户。
*   `-v poetic-postgres:/var/lib/postgresql/data`我们将之前创建的 docker 卷挂载到 */var/lib/postgresql/data* 目录，这是 Postgres 用来存储数据的目录。
*   我们给容器起的名字，以后我们可以用它来指代它。
*   `--rm`一旦停止，容器会自动移除，而卷会保留。

要移除容器，假设它是用`--rm`选项启动的，我们只需停止它，它就会被自动移除。我们可以在启动 *postgres* 的终端按`CTRL+C`。或者，使用另一个终端，我们可以使用命令:
停止容器

```
$ docker container stop postgres 
```

删除容器时，不会删除卷。

我们可以使用之前使用的相同的`docker container run ...`命令来启动一个新的 *postgres* 容器，使用相同的卷，这将恢复旧的数据。

# 配置

## Ecto 配置

既然我们的 Postgres 服务器已经启动并运行，我们需要配置 Phoenix 应用程序来连接数据库。

让我们来看看 [`config/dev.exs`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/config/dev.exs) ，在这里我们找到了我们的[爱克托回购](https://hexdocs.pm/ecto/Ecto.Repo.html)配置

```
# config/dev.exs
...
config :poetic, Poetic.Repo,
    username: "postgres",
    password: "postgres",
    database: "poetic_dev",
    hostname: "localhost",
    pool_size: 10 
```

默认情况下，这个配置匹配我们服务器的用户名、密码和主机名，所以我们不需要接触它。

然而，目前我们的 Postgres 中还没有 poetic_dev 数据库。为了创建它，让我们在终端
上运行这个 ecto 任务

```
$ mix ecto.create
The database for Poetic.Repo has been created 
```

这就创建了一个数据库。

如果您还没有看到`Poetic.Repo`模块，它是我们用来运行数据库查询的模块，它继承了 [Ecto。回购](https://hexdocs.pm/ecto/Ecto.Repo.html)功能(`insert`、`get`、...)

```
# lib/poetic/repo.ex
defmodule Poetic.Repo do
  use Ecto.Repo,
    otp_app: :poetic,
    adapter: Ecto.Adapters.Postgres
end 
```

## 上传目录

现在，让我们添加一个配置来设置*上传目录*的绝对路径，概述我们将在本地存储上传的位置，在本例中是针对开发环境。

```
# config/dev.exs
config :poetic,
   uploads_directory: "/Users/alvise/uploads_dev" 
```

我更喜欢将*上传目录*放在 Phoenix app 文件夹之外，所以在我的例子中，我在我的主目录中创建了一个*上传 _ 开发*文件夹。

如果我们想在生产中使用这个应用——我们想利用云存储，而不是重构它来使用 S3(这显然是一个很好的选择！)-我们可以使用像 AWS EFS 这样的服务，在云服务器的目录中安装网络文件系统。

然后我们只需要设置生产配置来使用挂载的网络文件系统

```
# config/prod.exs
config :poetic,
  uploads_directory: System.get_env("POETIC_UPLOADS_DIRECTORY") || "/uploads" 
```

在生产中，就像在这种情况下，通常最好使用环境变量将我们的设置传递给配置文件。

# 上传模块

我们可以使用 [`phx.gen.html`](https://hexdocs.pm/phoenix/Mix.Tasks.Phx.Gen.Html.html) 生成器，它为我们创建模式、迁移、上下文、控制器、视图、测试和 html 文件。

然而，我发现生成的支架对于我们需要的来说有点太多了。相反，我喜欢使用这个生成器来学习更多关于 phoenix 模式的知识，观察组件应该如何一起使用。

这里我们将使用的唯一生成器是创建 Ecto 模式和迁移。我们将手动创建剩余的必要文件。

## Ecto 图式与迁移

一旦用户上传了一个文件，我们想保存不同的信息到我们的数据库中。让我们使用`phx.gen.schema`任务
为上传生成*模式*和*迁移*文件

```
$ mix phx.gen.schema Documents.Upload uploads filename:string size:integer content_type:string hash:string 
* creating lib/poetic/documents/upload.ex
* creating priv/repo/migrations/20190412141226_create_uploads.exs 
```

第一个参数`Documents.Upload`是模式名，它创建了一个`Poetic.Documents.Upload`模块，其中`Documents`是我们的上下文(我们将在后面看到更多)。

`uploads`是将在数据库中创建的表。

其余参数定义模式中的字段和迁移文件中的列。

让我们先来看看迁移文件

```
# priv/repo/migrations/20190412141226_create_uploads.exs

defmodule Poetic.Repo.Migrations.CreateUploads do
  use Ecto.Migration

  def change do
    create table(:uploads) do
      add :filename, :string
      add :size, :integer
      add :content_type, :string
      add :hash, :string

      timestamps()
    end
  end
end 
```

这个文件反映了我们想要在数据库中做的更改，创建了包含`filename`、`size`、`content_type`和`hash`列的`uploads`表。默认情况下，Ecto 还创建了`id`列，这是主键。

使用`timestamps()` Ecto 添加了两个*时间戳*列，`inserted_at`和`updated_at`，每当我们在表中插入或更新一条记录时，这两个列都会被自动管理。

让我们对我们的迁移做一些小的改变

```
 create table(:uploads) do
    ...
    add :size, :bigint
    add :hash, :string, size: 64
    ...
end

create index(:uploads, [:hash]) 
```

我们改变的第一件事是`size`列的类型。 [`integer`](https://www.postgresql.org/docs/10/datatype-numeric.html) Postgres 类型是介于 *-2147483648* 和 *+2147483647* 之间的整数。为了保存大于 2GB 的值，我们需要一个`bigint` -这是足够的空间来存储我们需要在应用程序中处理的文件(高达 8192！).

为了散列文件，我们将使用的算法是 SHA-256，其中十六进制摘要是一个 64 个字符的字符串。我们已经使用`size: 64`选项设置了字符串的大小，并使用`create index(:uploads, [:hash])`在该列上创建了一个索引，因此如果我们需要使用散列来搜索文件，搜索会快得多。

现在让我们看一下 Upload Ecto 模式文件，它用于将数据库记录映射到一个 Elixir 结构。

```
# lib/poetic/documents/upload.ex
defmodule Poetic.Documents.Upload do
  use Ecto.Schema
  import Ecto.Changeset

  schema "uploads" do
    field :content_type, :string
    field :filename, :string
    field :hash, :string
    field :size, :integer

    timestamps()
  end

end 
```

模式中的字段是`%Poetic.Documents.Upload{}`结构的字段。他们映射数据库类型(比如 *bigint* ， *varchar* 等。)到仙丹式(*整数*，*字符串*等。).

```
iex> alias Poetic.Documents.Upload
iex> %Upload{}
%Poetic.Documents.Upload{
  __meta__: #Ecto.Schema.Metadata<:built, "uploads">,
  content_type: nil,
  filename: nil,
  hash: nil,
  id: nil,
  inserted_at: nil,
  size: nil,
  updated_at: nil
} 
```

在这个模块中，我们还找到了`changeset/2`函数，在这里我们设置了验证规则和必填字段。

```
# lib/poetic/documents/upload.ex
defmodule Poetic.Documents.Upload do
  ...
  def changeset(upload, attrs) do
    upload
    |> cast(attrs, [:filename, :size, :content_type, :hash])
    |> validate_required([:filename, :size, :content_type, :hash])

    # added validations
    |> validate_number(:size, greater_than: 0) #doesn't allow empty files
    |> validate_length(:hash, is: 64)
  end
end 
```

我们添加了另外两个验证:

*   `validate_number(:size, greater_than: 0)`检查给定的大小是否大于 0(没有空文件)。
*   `validate_length(:hash, is: 64)`检查哈希是否是 64 个字符的字符串。

现在是时候通过运行迁移任务
来创建`uploads`表了

```
$ mix ecto.migrate
[info] == Running 20190412141226 Poetic.Repo.Migrations.CreateUploads.change/0 forward
[info] create table uploads
[info] == Migrated 20190412141226 in 0.0s 
```

为了查看所有内容是否创建正确，我们可以使用正在运行的 *postgres* Docker 容器
中的`psql`客户端

```
$ docker container exec -it postgres psql -U postgres
psql (10.7)
Type "help" for help.

postgres=# \l
                                  List of databases
    Name     |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-------------+----------+----------+------------+------------+-----------------------
 poetic_dev  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
...
postgres=# \c poetic_dev
You are now connected to database "poetic_dev" as user "postgres".

postgres=# \d
 Schema |       Name        |   Type   |  Owner
--------+-------------------+----------+----------
 public | schema_migrations | table    | postgres
 public | uploads           | table    | postgres
 public | uploads_id_seq    | sequence | postgres

postgres=# \d+ uploads
    Column    |              Type              
--------------+--------------------------------
 id           | bigint                         
 filename     | character varying(255) 
 size         | integer                        
 content_type | character varying(255)         
 hash         | character varying(64)         
 inserted_at  | timestamp(0) without time zone 
 updated_at   | timestamp(0) without time zone 
 Indexes:
    "uploads_pkey" PRIMARY KEY, btree (id)
    "uploads_hash_index" btree (hash) 
```

然后，我们运行 *iex* 并使用 *Ecto* 和 *Upload* 来创建一个记录

```
poetic$ iex -S mix

iex> alias Poetic.Repo
iex> alias Poetic.Documents.Upload
iex> %Upload{} \
...> |> Upload.changeset(%{
    filename: "image.jpg", 
    content_type: "image/jpeg",
    hash: String.duplicate("a",64), #fake hash
    size: 1_000
}) |> Repo.insert()

[debug] QUERY OK db=2.4ms decode=0.8ms queue=0.8ms
INSERT INTO "uploads" ...

{:ok, %Poetic.Documents.Upload{id: 1, ...} 
```

`Upload.changeset/2`返回一个 [`Ecto.Changeset`](https://hexdocs.pm/ecto/Ecto.Changeset.html) ，传递给 [`Repo.insert/2`](https://hexdocs.pm/ecto/Ecto.Repo.html#c:insert/2) 函数，在数据库中创建上传记录。

## SHA-256 函数

使用我们在[中看到的在 Elixir](https://www.poeticoding.com/hashing-a-file-in-elixir/) 中散列一个文件，我们在`Poetic.Documents.Upload`模块中添加了`sha256(chunks_enum)`函数。这将计算并返回给定流的 SHA-256 十六进制摘要。

```
# lib/poetic/documents/upload.ex
defmodule Poetic.Documents.Upload do
    ...
    def sha256(chunks_enum) do
        chunks_enum
        |> Enum.reduce(:crypto.hash_init(:sha256),&(:crypto.hash_update(&2, &1)))
        |> :crypto.hash_final()
        |> Base.encode16()
        |> String.downcase()
    end
end 
```

要计算一个文件的散列值，我们可以使用下面的方法

```
iex> File.stream!("assets/statimg/phoenix.png", [], 2048) \
...> |> Poetic.Documents.Upload.sha256()
"07aa9b01595fe10..." 
```

## 本地 _ 路径

现在，我们需要一个简单的函数，告诉我们一旦收到文件，在哪里存储。

```
# lib/poetic/documents/upload.ex
defmodule Poetic.Documents.Upload do
    @upload_directory Application.get_env(:poetic, :uploads_directory)

    def local_path(id, filename) do
        [@upload_directory, "#{id}-#{filename}"]
        |> Path.join()
    end
end 
```

这个函数返回一个*绝对路径*，将文件放在我们之前在`config/dev.exs`中定义的上传目录中。

有几种方法可以使路径变得独特；在这种情况下，我们只需将`id`(上传的数据库记录的主键)添加到文件名的开头。

一般来说，如果我们有很多上传，把它们都保存在一个目录中并不是一个好主意。更好的做法是使用上传特性，比如创建日期，来产生一个目录层次结构，比如`"2019/04/15/#{id}-#{filename}"`。

# 上传控制器

在 [`PoeticWeb.UploadController`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/controllers/upload_controller.ex) 控制器中，我们将实现一些函数来回答以下 HTTP 请求:

*   呈现一个带有表单的页面来上传我们的文件
*   `POST /uploads`接收上传并将其存储在本地
*   `GET /uploads`列出上传的文件
*   `GET /uploads/:id`下载文件

我们首先改变我们的 [`PoeticWeb.Router`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/router.ex) 路由器，使用 [`resources`](https://hexdocs.pm/phoenix/routing.html#resources) 将 HTTP 请求匹配到控制器动作

```
# lib/poetic_web/router.ex
defmodule PoeticWeb.Router do
    ...

  scope "/", PoeticWeb do
    pipe_through :browser

    resources "/uploads", UploadController, only: [:index, :new, :create, :show]
  end
end 
```

使用`phx.routes`混合任务，我们可以列出所有路线

```
$ mix phx.routes
...
upload_path  GET   /uploads         PoeticWeb.UploadController :index
upload_path  GET   /uploads/new     PoeticWeb.UploadController :new
upload_path  GET   /uploads/:id     PoeticWeb.UploadController :show
upload_path  POST  /uploads         PoeticWeb.UploadController :create 
```

现在应该会列出*上传*路线。

## :新动作

为了开始编写`UploadController`，我们必须创建一个新文件`lib/poetic_web/controllers/upload_controller.ex`，在其中我们定义了 [`PoeticWeb.UploadController`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/controllers/upload_controller.ex) 模块

```
# lib/poetic_web/controllers/upload_controller.ex
defmodule PoeticWeb.UploadController do
  use PoeticWeb, :controller

  def new(conn, _params) do
    render(conn, "new.html")
  end
end 
```

`new/2`函数呈现了`new.html`模板，我们将在这里编写上传表单。

这个模板文件必须被命名为 [new.html.eex](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/templates/upload/new.html.eex) ，并放置在`lib/poetic_web/templates/upload`(其中 *upload* 是控制器的小写名称)。

```
<%= form_for @conn, Routes.upload_path(@conn, :create), [multipart: true], fn f-> %>

    <%= file_input f, :upload, class: "form-control" %>
    <%= submit "Upload", class: "btn btn-primary" %>

<% end %> 
```

这个 *EEx* 模板创建了一个**多部分**表单，用户可以在其中选择要发送的文件。当用户提交表单时，它会发出一个`POST /uploads`请求(由`Routes.upload_path(@conn, :create)`返回的动作)。

为了呈现模板，我们需要这个控制器的视图。Phoenix 假设从控制器到视图，再到它们所呈现的模板都有一个很强的命名约定。`UploadController`需要一个`UploadView`来渲染`lib/poetic_web/templates/upload`目录中的模板。

```
defmodule PoeticWeb.UploadView do
  use PoeticWeb, :view
end 
```

在测试我们的代码之前，让我们首先将`create/2`函数添加到控制器中，并检查表单向动作
发送了什么

```
defmodule PoeticWeb.UploadController do
    ...

  def create(conn, %{"upload" => %Plug.Upload{}=upload}) do
        IO.inspect(upload, label: "UPLOAD")
        text conn, "ok"
  end

end 
```

我们期望 params 中有一个`upload`键，一个 [`%Plug.Upload{}`](https://hexdocs.pm/plug/Plug.Upload.html) 结构保存我们上传的文件。

[Plug](https://github.com/elixir-plug/plug) 为我们管理上传，将数据保存在一个临时文件中，我们可以在该结构的`path`字段中找到该文件；当请求终止时，临时文件将被自动删除。

是时候运行凤凰服务器测试上传了。

要看表格，我们需要去*[http://localhost:4000/uploads/new](http://localhost:4000/uploads/new)*

```
$ mix phx.server
[info] Running PoeticWeb.Endpoint with cowboy 2.6.3 at 0.0.0.0:4000 (http)
[info] Access PoeticWeb.Endpoint at http://localhost:4000 
```

[![New upload multipart form](img/0213ee0989f6c47140548c4cec8f33d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G27aJwIC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1o6jjh24p1t92lcqci49qg8a-wpengine.netdna-ssl.com/wp-content/uploads/2019/04/phoenix_multipart_upload_form.jpg)

我将上传在项目资产中找到的*phoenix.png*图像。一旦提交，我们应该在终端
上看到这个检查日志

```
%Plug.Upload{
  content_type: "image/png",
  filename: "phoenix.png",
  path: "/var/folders/lt/t88p62t91mg1mxdlmv_rlkl80000gn/T//plug-1555/multipart-1555258629-39672758309128-2"
} 
```

Plug 给了我们三个重要的字段:`filename`、`content_type`和`path`——这是临时文件的路径。

# 单据上下文

一旦 [`UploadController.create/2`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/controllers/upload_controller.ex#L16) 函数被调用，它必须在数据库中创建一个上传记录，并将文件存储在上传目录中。

与其将所有的逻辑放入`create`函数，不如将这个逻辑写入一个[上下文](https://hexdocs.pm/phoenix/contexts.html)中。上下文是将控制器从应用程序的逻辑中分离出来的一个很好的方式(比如使用`Repo`来创建或加载记录)。

## 从%Plug 创建上传。上传{}

让我们用我们的上下文`Poetic.Documents`创建文件 [`lib/poetic/documents.ex`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic/documents.ex) ，我们用它作为管理上传的公共接口。

```
# lib/poetic/documents.ex
defmodule Poetic.Documents do
    import Ecto.Query, warn: false

    alias Poetic.Repo
    alias Poetic.Documents.Upload

    def create_upload_from_plug_upload(%Plug.Upload{
        filename: filename,
        path: tmp_path,
        content_type: content_type
    }) do

        # upload creation logic
    end

end 
```

实际的散列计算、数据库记录创建和文件复制作为 [`create_upload_from_plug_upload/1`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic/documents.ex#L16) 功能的一部分发生。

```
#create_upload_from_plug_upload function

hash = 
    File.stream!(tmp_path, [], 2048) 
    |> Upload.sha256()

with {:ok, %File.Stat{size: size}} <- File.stat(tmp_path),

  {:ok, upload} <- 
        %Upload{}
        |> Upload.changeset(%{
                filename: filename, content_type: content_type,
                hash: hash, size: size }) |> Repo.insert(),

    :ok <- File.cp(
                tmp_path, 
                Upload.local_path(upload.id, filename))

do 
    {:ok, upload}

else
    {:error, reason}=error -> error
end 
```

使用模式匹配，我们可以很容易地从*插件中提取出*文件名*、*内容类型*和*路径*。上传* struct 传递给函数。

我们首先计算文件的 SHA-256 散列，然后使用`with`构造来检查所有三个传递的子句是否匹配。

*   在第一个子句中，我们使用 [`File.stat`](https://hexdocs.pm/elixir/File.html#stat/2) 和模式匹配将`size`变量绑定到文件的大小。
*   如果前面的子句匹配，我们现在就拥有了将上传插入数据库所需的一切。
*   一旦在数据库中成功创建了上传，我们就使用`Upload.local_path(upload.id, filename)`函数将文件复制到上传目录中。

如果一切顺利，函数返回`{:ok, upload}`，其中`upload`是一个`Poetic.Documents.Upload`结构。

如果其中一个子句不匹配，就会出现错误，函数将返回`{:error, reason}`元组。

为了在 *iex* 上测试这个函数，我们传递一个指向有效文件的`%Plug.Upload{}`结构(记得设置一个绝对路径)。

```
iex> alias Poetic.Documents
iex> upload = %Plug.Upload{
    filename: "phoenix.png", 
    content_type: "image/png",
    path: "/absolute_path_to/phoenix.png", 
}
iex> Documents.create_upload_from_plug_upload(upload)
{:error, :enoent} 
```

啊哦-出问题了🤔

`enoent`意味着*没有这样的文件或目录*，在这种情况下，我们收到了这个错误，因为我们没有创建上传目录(在我的例子中是*/Users/al vise/uploads _ dev*)。

我们还可以看到，很难理解错误指的是哪个子句。使用元组包装有一种有效的方法来处理这个问题。

但是还有另一个问题，所以让我们看看上传表
里面有什么

```
poetic_dev=# select * from uploads;
id |  filename   | size  | ...
 5 | phoenix.png | 13900 | ...

(1 rows) 
```

虽然我们有一个错误，上传记录仍然在数据库中。

发生这种情况是因为在将上传插入数据库后，返回错误 [`File.cp/2`](https://hexdocs.pm/elixir/File.html#cp/3) 的函数*失败。*

## 回购交易

希望我们可以使用[事务](https://hexdocs.pm/ecto/Ecto.Repo.html#c:transaction/2_)将一切打包到一个*原子*操作中。这样做意味着，如果有任何失败，更改将被回滚(即，如果我们匹配一个错误，我们将请求回滚)。

```
Repo.transaction fn ->
    with ...
    do
        upload
    else
        {:error, reason} -> 
            Repo.rollback(reason)
    end
end 
```

如果一切顺利，`Repo.transaction`返回`{:ok, upload}`，而如果出错，则返回`{:error, reason}`。

让我们通过创建*上传目录*来修复错误，然后重试。

```
iex> %Plug.Upload{
    filename: "phoenix.png", 
    content_type: "image/png",
    path: "/absolute_path_to/phoenix.png", 
} |> Document.create_upload_from_plug_upload()

{:ok,
  %Poetic.Documents.Upload{
        ...
        content_type: "image/png",
    filename: "phoenix.png",
    hash: "07aa9b01...",
    id: 7,
    size: 13900,
    inserted_at: ~N[2019-04-15 12:25:24],
    updated_at: ~N[2019-04-15 12:25:24]
  }
} 
```

太棒了——成功了🎉！上传在数据库中创建，文件被复制到 *uploads_dev* 目录中。

```
$ ls /Users/alvise/uploads_dev/
7-phoenix.png 
```

# 上传控制器`:create`动作

我们可以使用刚刚创建的文档上下文来实现上传控制器
中的`create/2`功能

```
# PoeticWeb.UploadController
alias Poetic.Documents

def create(conn, %{"upload" => %Plug.Upload{}=upload}) do
    case Documents.create_upload_from_plug_upload(upload) do
        {:ok, upload}->
            put_flash(conn, :info, "file uploaded correctly")
            redirect(conn, to: Routes.upload_path(conn, :index))
        {:error, reason}->
            put_flash(conn, :error, "error upload file: #{inspect(reason)}")
            render(conn, "new.html")
    end
end 
```

我们将上传传递给`Documents.create_upload_from_plug_upload/1`，它为我们处理一切。

*   当它成功返回时，我们设置一个 flash info 消息，并重定向到列出所有上传内容的 *index* 页面。
*   当它返回一个错误时，我们设置一个 flash 错误消息并重定向到*新的*页面。

# 【上传控制器】- `:index`动作

为了列出上传的内容，首先我们在`Documents`上下文中添加一个简单的函数，称为 [`list_uploads`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic/documents.ex#L12) 。这将返回数据库中的所有上传内容。

```
defmodule Poetic.Documents do
    ...
    def list_uploads do
        Repo.all(Upload)
    end
end 
```

在`UploadController`中，我们然后创建 [`index/2`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/controllers/upload_controller.ex#L7) 函数，在那里我们获得上传列表并呈现 index.html 模板*。* 

```
defmodule PoeticWeb.UploadController do
    ...
  def index(conn, _params) do
        uploads = Documents.list_uploads()
        render(conn, "index.html", uploads: uploads)
  end
end 
```

我们将*上传*列表传递给渲染函数，并且在模板 [`lib/poetic_web/templates/upload/index.html.eex`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/templates/upload/index.html.eex) 中，我们循环该列表来渲染上传列表。

```
<table class="table">
    <thead>
        <th>ID</th>
        <th>Filename</th>
        <th>Type</th>
        <th>Time</th>
    </thead>
    <tbody>

        <%= for upload <- @uploads do %>
            <tr>
                <td><%= upload.id %></td>
                <td><%= upload.filename %></td>
                <td><%= upload.content_type %></td>
                <td><%= upload.inserted_at %></td>
            </tr>
        <% end %>

    </tbody>
</table> 
```

# 让我们试试吧

```
$ mix phx.server
Running PoeticWeb.Endpoint with cowboy
Access PoeticWeb.Endpoint at http://localhost:4000
... 
```

[![:index action to list uploads](img/d69bc4c6b13f175e5487f6b489b70544.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SsN-SVfc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/index_action_to_list_uploads.png)

# 下载

为了请求下载，我们使用 [`show/2`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/controllers/upload_controller.ex#L30) 函数，我们使用`GET /uploads/:id` HTTP 请求调用该函数。

我们开始修改 [`index.html.eex`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/templates/upload/index.html.eex) 模板，在每次上传的行中添加一个下载链接。

```
<%= for upload <- @uploads do %>
    ...
    <td>
        <%= link "download", to: Routes.upload_path(@conn, :show, upload.id) %>
    </td>
<% end %> 
```

然后，在`Documents`上下文中，我们添加了 [`get_upload!(id)`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic/documents.ex#L7) 函数，当给定一个`id`时，该函数返回一个上传结构

```
# lib/poetic/documents.ex
defmodule Poetic.Documents do
  ...
    def get_upload!(id) do
        Upload
        |> Repo.get!(id)
    end
end 
```

最后，让我们实现 [`show/2`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/controllers/upload_controller.ex#L30) 功能。

这里，我们使用给定的`id`从数据库中获取上传，连同保存文件的`local_path`，并使用 [`send_download`](https://hexdocs.pm/phoenix/Phoenix.Controller.html#send_download/3) 发送文件。

```
# PoeticWeb.UploadController

def show(conn, %{"id" => id}) do
    upload = Documents.get_upload!(id)
    local_path = Upload.local_path(upload.id, upload.filename)
    send_download conn, {:file, local_path}, filename: upload.filename
end 
```

然后，我们将连接传递给`send_download`和一个元组，该元组包含我们要发送的文件的路径和文件名。

用我们的浏览器进入*[http://localhost:4000/uploads](http://localhost:4000/uploads)*我们现在看到了*下载*链接，我们可以用它来下载文件

[![List of uploads with download link](img/f2536082c506c0e6f5884697781f574a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6dRsoOB6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/index_download_link.jpg)

# 上传大小限制

如果我们尝试上传一个大文件，如 100MB 的 tiff 图像，我们会收到以下错误

[![The request is too large](img/678abfe9a6eb25e25fd9a71191449035.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--swnVzwmN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/the_request_is_too_large.jpg)

默认情况下，[插件多部分解析器](https://hexdocs.pm/plug/Plug.Parsers.MULTIPART.html)最多读取 *8_000_000 字节*。为了增加这个限制，我们需要修改 [`lib/poetic_web/endpoint.ex`](https://github.com/poeticoding/phoenix_uploads_articles/blob/d67c4b2495a75410f7cc497a6df2dd54be37cc03/lib/poetic_web/endpoint.ex#L30) 文件

```
# lib/poetic_web/endpoint.ex
plug Plug.Parsers,
    parsers: [:urlencoded, :multipart, :json], 
```

用`{:multipart, length: num_of_bytes}`
改变`:multipart`

```
plug Plug.Parsers,
    parsers: [:urlencoded, {:multipart, length: 500_000_000}, :json], 
```

在这种情况下，我们设置了 500MB 的限制。重新启动服务器后，我们看到在这个改变后，我们现在可以上传一个巨大的 200MB 的 tiff 图像。

[![Large image uploaded correctly](img/27a22cb689c26817c1320a672ab4f099.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SnqFT2wA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/large_file_upload.jpg)

# 总结起来

在本教程中，我们深入了解了如何从头开始构建 Phoenix 应用程序，让用户使用多部分表单上传他们的文件。我们学习了如何在我们的机器上使用 Docker 轻松运行 PostgreSQL，使用 Plug 接收上传。上传，以及如何使用 Ecto 将上传的详细信息存储到数据库中。

有很多库可以帮助你处理上传和云存储。一个很棒的是 [Arc](https://github.com/stavro/arc) ，它与 Ecto 和 AWS S3 集成得很好。

你可以在 GitHub repo 中找到与本文相关的所有代码:[poeticoding/phoenix _ uploads _ articles](https://github.com/poeticoding/phoenix_uploads_articles)。

有兴趣了解 Elixir、Phoenix 和软件架构吗？[订阅我的时事通讯，了解一般思考和每周深入的方法](https://www.poeticoding.com/subscribe-to-newsletter/)。