# 尝试使用 Mix 和 OTP 01: Mix

> 原文：<https://dev.to/gumi/mixotp-01-mix-dg1>

本文在 Elixir 官网的许可下，根据“[Introduction to Mix](https://elixir-lang.org/getting-started/mix-otp/introduction-to-mix.html) ”的解说，加以修改，简要说明 Elixir 的构建工具 Mix。

要在 Elixir 中创建 APP 应用程序，需要使用以下三种工具:

*   **OTP** ( Open Telecom Platform ) :是 Erlang 所具备的一套库。 Erlang 开发人员可以使用 OTP 构建强健、容错的 APP 应用程序(请参见“系统体系结构”“[1 Introduction](http://erlang.org/doc/system_architecture_intro/sys_arch_intro.html) )。 在本文中，我们将确认 OTP 和 Elixir 在哪些方面进行了集成，包括监视树和事件管理器等。
*   [**Mix**](https://hexdocs.pm/mix/) : Elixir 的构建工具。 用于创建和编译 APP 应用程序、测试应用程序、管理依赖关系等任务。
*   [**ExUnit**](https://hexdocs.pm/ex_unit/) :是基于 Elixir 所具备的测试单元的框架。

在此，使用 Mix 创建第一个项目，对 OTP、Mix、ExUnit 的各种功能进行说明。

# 第一个项目

安装 Elixir 后，可执行文件除了`elixir`、`elixirc`和`iex`之外，还包含名为`mix`的 Elixir 脚本。 用命令行工具在`mix new`后面输入项目名称，就会在使用该名称的目录中创建项目文件(图 001 )。

在以下命令中，`--module`选项决定了模块名称(`KV`)。 这是因为默认情况下只有项目名称的开头为大写(`Kv`)。

```
$  mix new kv --module KV 
```

Enter fullscreen mode Exit fullscreen mode

```
* creating README.md
* creating .formatter.exs
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/kv.ex
* creating test
* creating test/test_helper.exs
* creating test/kv_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd kv
    mix test

Run "mix help" for more commands. 
```

Enter fullscreen mode Exit fullscreen mode

#### t0-t1 -用 001■Mix 制作的项目文件

[![mix_otp_01_001.png](img/bca98fa0de226fd428f585e12b3c5e10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GDsbWlOT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/60il0fs5uihdkwn0v7a2.png)

# 项目的编译

新创建的项目文件夹(`kv`)中存储名为`mix.exs`的文件。 用于设定项目的文件。 规定了以下两个公共函数。

*   `project`:返回项目的设定。
    *   项目名称
    *   版本等
*   `application`:用于创建 APP 应用程序文件。
    *   用`mix help compile.app`表示说明。

私有函数`deps`从`project`函数调用，确定项目的依赖关系。 之所以将其作为其他函数进行区分，是为了使项目的设定更容易理解(`mix help deps`会进行说明)。

```
defmodule KV.MixProject do
  use Mix.Project

  def project do
    [
      app: :kv,
      version: "0.1.0",
      elixir: "~> 1.6",
      start_permanent: Mix.env() == :prod,
      deps: deps()
    ]
  end

  # Run "mix help compile.app" to learn about applications.
  def application do
    [
      extra_applications: [:logger]
    ]
  end

  # Run "mix help deps" to learn about dependencies.
  defp deps do
    [
      # {:dep_from_hexpm, "~> 0.3.0"},
      # {:dep_from_git, git: "https://github.com/elixir-lang/my_dep.git", tag: "0.1.0"},
    ]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`lib`文件夹中存储的是项目的文件。 模块中只规定了确认用的函数`hello`。

```
defmodule KV do
  @moduledoc """
  Documentation for KV.
  """

  @doc """
  Hello world.

  ## Examples

      iex> KV.hello
      :world

  """
  def hello do
    :world
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

可以使用命令行工具切换到项目目录，使用`mix compile`命令进行编译。

```
$  cd kv
$  mix compile 
```

Enter fullscreen mode Exit fullscreen mode

```
Compiling 1 file (.ex)
Generated kv app 
```

Enter fullscreen mode Exit fullscreen mode

编译结束后，按照`mix.exs`的规定将文件保存到新创建的`_build`目录中(图 002 )。 项目名称中扩展名为`.app`的文件(`kv.app`)是 APP 应用清单。 另外，协议也被合并(`consolidated`文件夹) (参照“[Elixir 入门 16 :协议](https://dev.to/gumi/elixir-16--lif)”的“协议合并”)。

#### t0-t1 -用 002Mix 编译而制作的文件

[![mix_otp_01_002.png](img/16e053fd36dd427445ba8fbcceb5ec8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bXNHYn4A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b40khpht8oslhvzrrkgw.png)

编译项目后，从项目目录中用以下命令开始`iex`会话，即可使用模块。

```
$  iex -S mix 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> KV.hello
:world 
```

Enter fullscreen mode Exit fullscreen mode

# 进行考试

Mix 在`test`文件夹中制作两个用于项目测试的文件(图 003 )。

#### t0-t1-003■在 test 文件夹中制作的文件

[![mix_otp_01_003.png](img/95142ca5a651d9802394cb1cc0d85325.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r7I2xs8H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tbt38ude89o4gk14ma0n.png)

一个是项目名称中附有`_test.exs`的文件(`kv_test.exs`)。 `lib`与目录的项目文件(`kv.ex`)对应。 一开始，只写了简单的动作确认的代码。

```
defmodule KVTest do
  use ExUnit.Case
  doctest KV

  test "greets the world" do
    assert KV.hello() == :world
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

有两件事要注意。

1.  测试文件为 Elixir 脚本(`exs`)。 因此，您不必在每次测试时重新编译文件。
2.  测试模块(`KVTest`)中使用的是[`ExUnit.Case`](https://hexdocs.pm/ex_unit/ExUnit.Case.html)。 这是添加测试用 API 的模块。 然后，使用[`test/3`宏](https://hexdocs.pm/ex_unit/ExUnit.Case.html#test/3)规定了简单的测试。

`test`文件夹中的另一个文件是负责测试框架设定的`test_helper.exs`。 每次运行测试时都需要 Mix。

```
ExUnit.start() 
```

Enter fullscreen mode Exit fullscreen mode

要进行测试，请从命令行工具按如下方式输入`mix test`。

```
$  mix test
..  Finished in 0.03 seconds
1 doctest, 1 test, 0 failures

Randomized with seed 71551 
```

Enter fullscreen mode Exit fullscreen mode

如果运行`mix test`，则会编译源文件，并重新创建`_build`目录。 APP 声明会被改变。 这是基于 Mix 支持多个环境。

然后，输入`mix test`后，ExUnit 只输出测试成功的数量的点。 此外，测试将自动随机化。

尝试通过改写测试文件( kv_test.exs )中的代码故意使测试失败，如下所示:

```
assert KV.hello() == :oops  # :world 
```

Enter fullscreen mode Exit fullscreen mode

```
$  mix test
 1) test greets the world (KVTest)
     test/kv_test.exs:5
     Assertion with == failed
     code:  assert KV.hello() == :oops
     left:  :world
     right: :oops
     stacktrace:
       test/kv_test.exs:6: (test) .  Finished in 0.03 seconds
1 doctest, 1 test, 1 failure

Randomized with seed 828995 
```

Enter fullscreen mode Exit fullscreen mode

ExUnit 会在每次失败时输出详细的报告。 包含的信息如下。

*   测试名称和测试用例
*   失败的代码(`code`)
*   失败表达式的左边值(`left`)和右边值(`right`)

测试名称的下一行显示测试的指定位置。 如果将这一行直接复制并添加到`mix test`命令中，Mix 就会从该位置读取并开始测试。 开发项目时，可以确定一个测试并立即重复，非常方便。

```
$  mix test test/kv_test.exs:5 
```

Enter fullscreen mode Exit fullscreen mode

堆栈跟踪(`stacktrace`)与失败本身相关，它显示测试信息，以及通常在源文件中发生故障的位置。

# 自动码格式

`.formatter.exs`是`mix new`指令制作的文件。 Elixir 具有代码格式化程序。 代码库会自动调整为统一的风格。 格式化程序在`mix format`任务中执行。 制作的`.formatter.exs`决定`mix format`要格式化哪个文件。

要尝试格式化，只需在`lib`和`test`目录的文件代码中添加额外的空格和换行符进行保存即可。

```
defmodule KVTest
do

  use ExUnit.Case

  doctest KV

  test "greets the world" do

    assert KV.hello()
    == :world

  end
end 
```

Enter fullscreen mode Exit fullscreen mode

从命令行工具输入`mix format`命令，代码的格式就完整了。

```
$  mix format 
```

Enter fullscreen mode Exit fullscreen mode

```
defmodule KVTest do
  use ExUnit.Case

  doctest KV

  test "greets the world" do
    assert KV.hello() == :world
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

许多编辑器都包含与格式化程序集成的功能，可以在保存文件时或通过按键设置文件格式。 学习 Elixir 时，使用这个集成功能的话，Elixir 的语法会更容易理解吧。

对于企业或团队开发，建议持续在集成服务器上运行`mix format --check-formatted`。 从现在到将来的代码遵循标准。

关于代码格式，请参阅“[mix format](https://hexdocs.pm/mix/Mix.Tasks.Format.html) ”。 另外，“[Elixir v1.6 released](https://elixir-lang.org/blog/2018/01/17/elixir-v1-6-0-released/) ”也对从该版本开始具备的代码格式进行了说明。

# environment

Mix 有“环境”的想法。 此环境允许开发人员根据需要设置编译等选项。 缺省情况下，Mix 识别以下三种环境:

*   `:dev:`Mix 默认执行编译等任务。
*   `:test:``mix test`使用。
*   `:prod:`将项目作为产品执行。

环境仅适用于当前项目。 添加到项目的依赖关系默认在`:prod`环境下执行。

每个环境的定制在`mix.exs`中调用[`Mix.env/0`](https://hexdocs.pm/mix/Mix.html#env/0)函数进行。 函数的返回值是表示当前环境的原子。 `project`函数中用于`start_permanent`选项。

```
defmodule KV.MixProject do

  def project do
    [

      start_permanent: Mix.env() == :prod,

    ]
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

`:start_permanent`的值为`true`即`:prod`环境时，APP 应用程序将以永久模式启动。 然后，在 APP 应用程序监视树结束时，Erlang VM 将崩溃。 在`:dev`和`:test`环境下，继续运行 VM 实例更能应对故障，比较方便吧。

Mix 的默认值为`:dev`环境。 但是，关于`test`任务，默认为`:test`环境。 环境根据变量[`MIX_ENV`](https://hexdocs.pm/mix/Mix.html#module-environment-variables)而变化(参照“[Environments](https://hexdocs.pm/mix/Mix.html#module-environments) ”)。

```
$  MIX_ENV=prod mix compile 
```

Enter fullscreen mode Exit fullscreen mode

Mix 是构建工具。 因此，产品不一定能用。 特别是在团队按照明确的构建步骤进行的情况下，很可能无法使用。 因此，参照`Mix.env/0`应该仅限于设定文件内的`mix.exs`中。 绝对不要在 APP 代码(`lib`文件夹)中使用。

# 更详细

详情请参阅“[Mix](https://hexdocs.pm/mix/Mix.html) ”。 [也可以查看 mix 的源代码](https://github.com/elixir-lang/elixir/tree/master/lib/mix)。

可以使用的任务一览用命令`mix help`显示。

```
$ mix help
mix                   # Runs the default task (current: "mix run")
mix app.start         # Starts all registered apps
mix app.tree          # Prints the application tree
mix archive           # Lists installed archives
mix archive.build     # Archives this project into a .ez file
mix archive.install   # Installs an archive locally
mix archive.uninstall # Uninstalls archives
mix clean             # Deletes generated application files
mix cmd               # Executes the given command
mix compile           # Compiles source files
mix deps              # Lists dependencies and their status
mix deps.clean        # Deletes the given dependencies' files
mix deps.compile      # Compiles dependencies
mix deps.get          # Gets all out of date dependencies
mix deps.tree         # Prints the dependency tree
mix deps.unlock       # Unlocks the given dependencies
mix deps.update       # Updates the given dependencies
mix do                # Executes the tasks separated by comma
mix escript           # Lists installed escripts
mix escript.build     # Builds an escript for the project
mix escript.install   # Installs an escript locally
mix escript.uninstall # Uninstalls escripts
mix format            # Formats the given files/patterns
mix help              # Prints help information for tasks
mix hex               # Prints Hex help information
mix hex.audit         # Shows retired Hex deps for the current project
mix hex.build         # Builds a new package version locally
mix hex.config        # Reads, updates or deletes local Hex config
mix hex.docs          # Fetches or opens documentation of a package
mix hex.info          # Prints Hex information
mix hex.organization  # Manages Hex.pm organizations
mix hex.outdated      # Shows outdated Hex deps for the current project
mix hex.owner         # Manages Hex package ownership
mix hex.publish       # Publishes a new package version
mix hex.repo          # Manages Hex repositories
mix hex.retire        # Retires a package version
mix hex.search        # Searches for package names
mix hex.user          # Manages your Hex user account
mix loadconfig        # Loads and persists the given configuration
mix local             # Lists local tasks
mix local.hex         # Installs Hex locally
mix local.phoenix     # Updates Phoenix locally
mix local.phx         # Updates the Phoenix project generator locally
mix local.public_keys # Manages public keys
mix local.rebar       # Installs Rebar locally
mix new               # Creates a new Elixir project
mix phoenix.new       # Creates a new Phoenix v1.3.2 application
mix phx.new           # Creates a new Phoenix v1.3.2 application
mix phx.new.ecto      # Creates a new Ecto project within an umbrella project
mix phx.new.web       # Creates a new Phoenix web project within an umbrella project
mix profile.cprof     # Profiles the given file or expression with cprof
mix profile.eprof     # Profiles the given file or expression with eprof
mix profile.fprof     # Profiles the given file or expression with fprof
mix run               # Starts and runs the current application
mix test              # Runs a project's tests
mix xref              # Performs cross reference checks
iex -S mix            # Starts IEx and runs the default task 
```

Enter fullscreen mode Exit fullscreen mode

而且，在命令后添加想知道的任务名称，调用`mix help タスク名`的话，也可以看到每个任务的详细信息(图 004 )。

#### t0.t1■显示任务的详细信息

[![mix_otp_01_004.png](img/63e64b7ae106fa54624f04ea97675fdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dlQCbMMf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mcsqsswaapzltvid0jyj.png)

#### Mix 和 OTP 也抽签

*   尝试使用 Mix 和 OTP 01: Mix
*   MixとOTP 02:经纪人
*   MixとOTP 03:基因服务器
*   [Mix 和 OTP 04 :管理员和 APP 沟通](https://dev.to/gumi/mix-otp-04-bj0)
*   [Mix 和 OTP 05 :动态管理员](https://dev.to/gumi/mix-otp-05-l3p)
*   [MixとOTP 06: ETS](https://dev.to/gumi/mix-otp-06-ets-ol3)
*   [Mix 和 OTP 07 :依存关系和模糊项目](https://dev.to/gumi/mix-otp-07-1p5k)
*   MixとOTP 08: Taskとgen_tcp
*   [Mix 和 OTP 09: DocTest 和 with 的模式匹配](https://dev.to/gumi/mix-otp-09-doctest-with-533g)
*   [Mix 和 OTP 10 :分散处理的 Task 和设定](https://dev.to/gumi/mix-otp-10-3i31)