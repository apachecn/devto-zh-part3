# 使用 cake build 在 Travis CI、Azure pipeline 和 appveyor 上集成测试和对接 ASP.NET 核心应用。

> 原文：<https://dev.to/iambipinpaul/integration--testing-and--dockerizing--aspnet-core-apps-on-travis-ci--azure-pipeline-and-appveyor-using-cake-build-538c>

原帖:[https://bipinpaul . com . NP/posts/integration-testing-and-dockerizing-ASP . net-core-apps-on-Travis-ci-azure-pipeline-and-app veyor-using-cake-build](https://bipinpaul.com.np/posts/integration-testing-and-dockerizing-asp.net-core-apps-on-travis-ci-azure-pipeline-and-appveyor-using-cake-build)

当我们处理一些开源项目时，我们经常需要在多个 CI 服务器上构建相同的项目，每个 CI 服务器都有自己的配置文件，编写不同的文件来完成相同的任务是没有意义的。

下面是 Cake build，它在引擎盖下使用了罗斯林。

你可能不熟悉蛋糕造型，这是官方的定义

> Cake (C# Make)是一个跨平台的构建自动化系统，使用 C# DSL 来执行诸如编译代码、复制文件和文件夹、运行单元测试、压缩文件和构建 NuGet 包等任务。

为了使用 cake，我们需要一个 cake 构建文件，通常是 build.cake。在这个文件中，我们可以定义不同的任务，这些任务将按照定义的方式执行。我们可以让一些任务依赖于另一个任务。假设`Task A`依赖于`task B`，那么首先`task B`然后`task A`将执行。

示例

```
Task("Restore")
    .Does(() =>
{
    DotNetCoreRestore(Paths.SolutionFile.FullPath);
}); 

Task("Build")
    .IsDependentOn("Restore")
    .Does(() =>
{
    DotNetCoreBuild(
        Paths.ProjectFile.FullPath,
        new DotNetCoreBuildSettings
        {
            Configuration = configuration
        });
}); 
```

在上面的例子中，当目标设置为`Build`时，它将在任务`Build`之前运行任务`Restore`。

这样定义任务的执行顺序就容易多了。

如果您想了解更多信息，请访问文档网站。

我将要使用的项目很简单，它也说明了集成测试。

## 我们有什么项目。

我们有一个名为`Person`的 ASP.NET 核心应用程序，它以 json 数据的形式从家庭控制器返回一个人员列表。

## 测试设置

出于测试目的，我使用 nUnit。项目结构包含一个 ASP.NET 核心应用程序，只有一个从数据库返回人员列表的端点。

我有一个种子项目，种子与 100 人的数据库。它有一个名为`Data`的类和一个名为`CreateData`的方法。它将 DbContext 作为参数，因此在独立运行控制台应用程序时，它将数据播种到数据库中。我也使用这个`CreateData`来播种测试数据。

为数据库设定种子的方法。

```
namespace AspNetCoreDevOps.Seeder
{
    public class Data
    {
        public static void CreateData(ApplicationDbContext _context)
        {
            var users = Builder<Person>.CreateListOfSize(1000)
                .All()
                .With(c => c.Id = 0)
                .With(c => c.Name = Name.First())
                .With(c => c.Surname = Name.Last())
                .Build();

            _context.People.AddRange(users);
            _context.SaveChanges();
        }
    }
} 
```

为了测试，我有两个项目`AspNetCoreDevOps.Tests.Core`和`AspNetCoreDevOps.Controllers.Tests`。

`AspNetCoreDevOps.Tests.Core`有一个 BaseIntegrationTests.cs 类，它在开始一个测试用例之前处理数据库的创建和播种，在完成一个测试用例之后删除数据库。

```
namespace AspNetCoreDevOps.Tests.Core
{
    public abstract class BaseIntegrationTests
    {
        protected ApplicationDbContext Context;

        public virtual void SetUp()
        {
            var helper = new Helper();
            var result = helper.GetContextAdnUserManager();
            Console.WriteLine("Deleting database");
            result.Database.EnsureDeleted();

            Context = result;

            Console.WriteLine("Applying Migrations");
            result.Database.Migrate();

            Console.WriteLine("Making sure database is created ");
            result.Database.EnsureCreated();

            Console.WriteLine("Going to save the data ");

            Data.CreateData(result);
            Console.WriteLine("Adding Data into database");
            result.SaveChanges();

            Console.WriteLine("Database successfully seeded");
            var totalTopic = result.People.ToList();
            Console.WriteLine($"Total People seeded is {totalTopic.Count()}");
        }

        [TearDown]
        public virtual void TearDown()
        {
            Context.Database.EnsureDeleted();
        }
    }
} 
```

这里，`setup`方法将创建数据库并播种初始数据，而`TearDown`方法将在完成一个测试用例后删除数据库。

另一个项目`AspNetCoreDevOps.Controllers.Tests`有 BaseControllerTests.cs 类，它创建主机服务器、测试服务器和用于测试的测试客户机。

```
namespace AspNetCoreDevOps.Controllers.Tests
{
    public class BaseControllerTests : BaseIntegrationTests
    {
        protected HttpClient Client, Client2;

        protected TestServer Server;

        public override void SetUp()
        {
            base.SetUp();
            Server = new TestServer(new WebHostBuilder()
                .UseConfiguration(new ConfigurationBuilder()
                    .AddJsonFile("appsettings.json")
                    .Build()
                )
                .UseStartup<Startup>());
            Client = Server.CreateClient();
            Client.BaseAddress = new Uri("https://localhost");
            Client.DefaultRequestHeaders.Accept.Clear();
            Client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        }
    }
} 
```

此类继承自 BaseIntegrationTests，用于创建和播种数据库。

这里我们有一个从 BaseControllerTests 继承的 HomeControllerTests 类。它有 SetUp()和 GetPeopleSucessFullyAsync()方法。
SetUp()方法调用基类准备数据库，测试服务器和客户端。
getpeoplescessefullyasync()调用 Api 获取人员列表，并将其与预期值进行比较。

```
 namespace AspNetCoreDevOps.Controllers.Tests.IntegrationTests
{
    [TestFixture]
    internal class HomeControllerTests : BaseControllerTests
    {
        [SetUp]
        public override void SetUp()
        {
            base.SetUp();
        }

        [Test]
        public async Task GetPeopleSuccessfullyAsync()
        {
            var response = await Client.GetAsync("Home/index");
            var people = new List<Person>();
            Assert.That(response.IsSuccessStatusCode, Is.EqualTo(true));
            if (response.IsSuccessStatusCode)
            {
                var af = await response.Content.ReadAsStringAsync();
                people = JsonConvert.DeserializeObject<List<Person>>(af);
            }

            Assert.That(people.Count, Is.EqualTo(1000));
        }
    }
} 
```

我们有一个 docker 合成文件，它提取并运行 postgreSQL 映像。

```
version: '3'

services:
  db:
    image: postgres
    container_name: travis_db
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=1q2w3e
      - POSTGRES_DB=travisdb
    ports:
      - "1234:5432" 
```

测试使用 postgreSQL 作为数据库，所以我们在这里测试真实的数据库。

我们还有另一个 docker 文件，为我们的 api 项目构建多部分图像。

```
FROM microsoft/dotnet:2.2.100-sdk-alpine3.8 AS buildimg
WORKDIR /app
COPY . .
RUN dotnet restore
RUN dotnet build
WORKDIR /app/src/AspNetCoreDevOps.Api
RUN dotnet publish -c  Release -o output

FROM microsoft/dotnet:2.2.0-aspnetcore-runtime-alpine3.8
WORKDIR output
COPY --from=buildimg /app/src/AspNetCoreDevOps.Api/output .
ENTRYPOINT ["dotnet","AspNetCoreDevOps.Api.dll"] 
```

对于蛋糕构建，我们可以使用 bootstrapper powershell 或 bash 文件从 nuget 下载所有需要的文件，包括蛋糕构建。或者我们可以使用 dotnet 全球工具来做所有这些事情，我将在这篇文章中使用。

我们可以安装运行以下命令的 cake 全局工具

```
dotnet tool install -g Cake.Tool 
```

我们还有一个 cake.config 文件，在这里我们可以配置使用哪个 nuget 提要，它将在哪里存储 nuget 包和其他东西。

这是文件的外观。

```
; This is the default configuration file for Cake.
; This file was downloaded from https://github.com/cake-build/resources

[Nuget]
Source=https://api.nuget.org/v3/index.json
UseInProcessClient=true
LoadDependencies=false

[Paths]
Tools=./caketools
Addins=./caketools/Addins
Modules=./caketools/Modules

[Settings]
SkipVerification=false 
```

我们现在有了 cake 配置文件，因此我们可以创建 build.cake 文件，该文件将定义测试、构建和将 docker 映像推送到 docker hub 的所有步骤。

> 为了简单起见，我使用了 path.cake 和 projectInfo.cake，它们具有静态类、方法和变量，提供一些基本信息，如解决方案名称、要推送的 Docker hub 库等。我们也可以在主 build.cake 文件中这样做。

path.cake

```
 public static class Paths
{
    public static FilePath SolutionFile => "AspNetCoreDevOps.sln";
    public static FilePath ProjectFile => "src/AspNetCoreDevOps.Api/AspNetCoreDevOps.Api.csproj";
    public static FilePath TestProjectFile => "test/AspNetCoreDevOps.Controllers.Tests/AspNetCoreDevOps.Controllers.Tests.csproj";
}
public static FilePath Combine(DirectoryPath directory, FilePath file)
{
    return directory.CombineWithFilePath(file);
} 
```

projectInfo.cake

```
public static class Docker
{
    public static string  Username ="iambipinpaul";
    public static string  Repository ="aspnet-core-devops";
} 
```

我们的 buid.cake 文件构建解决方案，测试它，如果成功，它会将映像推送到 docker hub。

在第一行，我们引用我们的助手蛋糕文件，从那里我们将得到项目/解决方案来构建。我们还添加了 docker 插件，这样我们就可以在 cake 构建文件中使用 docker cli。

```
#load cake/paths.cake  
#load cake/projectInfo.cake  
#addin "Cake.Docker 
```

我正在根据分支和构建 id 标记 docker 图像。所以我们需要从构建服务器获取分支，但是不同的服务器有不同的方法来获取分支和构建 id。幸运的是，它为所有主要的 CI 服务器提供了包装器

```
var buildId ="";
var branch="";
if(BuildSystem.TFBuild.IsRunningOnVSTS)
{
   branch= $"VSTS{BuildSystem.TFBuild.Environment.Repository.Branch}";
   buildId= (BuildSystem.TFBuild.Environment.Build.Id).ToString();
}
if(BuildSystem.AppVeyor.IsRunningOnAppVeyor)
{     branch=$"AppVeyor{BuildSystem.AppVeyor.Environment.Repository.Branch}";
      buildId= BuildSystem.AppVeyor.Environment.Build.Id; 
}

if (BuildSystem.TravisCI.IsRunningOnTravisCI)
{          
     branch= $"TravisCI{BuildSystem.TravisCI.Environment.Build.Branch}";            
     buildId= BuildSystem.TravisCI.Environment.Build.BuildId;
}
if(string.IsNullOrEmpty(buildId))
{
    buildId="github-action";
    branch="master";
} 
```

这里有两个变量，buildId 和 branch，在写这篇文章的时候，Github 动作还没有可用的包装器。所以，我硬编码了 branch 和 buildId。文件的剩余部分包含构建和测试的 cake 任务。它还构建 docker 并将其推送到 docker hub。

> 我们可以像这样在蛋糕文件中使用环境变量。

```
var dockerPassword = EnvironmentVariable("DOCKER_PASSWORD"); 
here we are passing the envornment varialbe name “DOCKER_PASSWORD” and it assign this to dokcerPassword  varaiable. 
```

所以我们完整的 build.cake 看起来像这样。

```
#load cake/paths.cake 
#load cake/projectInfo.cake  
#addin "Cake.Docker"

var target = Argument("target", "DockerPush");
var configuration = Argument("configuration", "Release");
var buildId ="";
var branch="";
if(BuildSystem.TFBuild.IsRunningOnVSTS)
{
   branch= $"VSTS{BuildSystem.TFBuild.Environment.Repository.Branch}";
   buildId= (BuildSystem.TFBuild.Environment.Build.Id).ToString();
}
if(BuildSystem.AppVeyor.IsRunningOnAppVeyor)
{     branch=$"AppVeyor{BuildSystem.AppVeyor.Environment.Repository.Branch}";
      buildId= BuildSystem.AppVeyor.Environment.Build.Id; 
}

if (BuildSystem.TravisCI.IsRunningOnTravisCI)
{          
     branch= $"TravisCI{BuildSystem.TravisCI.Environment.Build.Branch}";            
     buildId= BuildSystem.TravisCI.Environment.Build.BuildId;
}
if(string.IsNullOrEmpty(buildId))
{
    buildId="github-action";
    branch="master";
}
Task("DockerCompose")
.Does(() => {
   DockerComposeUp(new DockerComposeUpSettings{ForceRecreate=true,DetachedMode=true,Build=true});   
});

Task("DockerLogin")
.IsDependentOn("Test")
.Does(() => {   
  var dockerPassword = EnvironmentVariable("DOCKER_PASSWORD");
        if(string.IsNullOrEmpty(dockerPassword))
        {
            throw new Exception("Could not get dockerPassword environment variable");
        }
   DockerLogin(new DockerRegistryLoginSettings{Password=dockerPassword,Username=Docker.Username});   
});

Task("DockerBuild")
 .IsDependentOn("DockerLogin")
.Does(() => {
    string [] tags = new string[]  { $"{Docker.Username}/{Docker.Repository}:{buildId}"};
    DockerBuild(new DockerImageBuildSettings{Tag=tags},".");   
});

Task("DockerTag")
   .IsDependentOn("DockerBuild")
    .Does(() => {      
 bool IsVSTSMasterBrach = StringComparer.OrdinalIgnoreCase.Equals("VSTSmaster", branch);
 string tag="";
if(IsVSTSMasterBrach && BuildSystem.TFBuild.IsRunningOnVSTS)
{
tag="latest";
}else
{    
   tag=$"{branch}-{buildId}";
}
   DockerTag($"{Docker.Username}/{Docker.Repository}:{buildId}",$"{Docker.Username}/{Docker.Repository}:{tag}");   
});

Task("DockerPush")
    .IsDependentOn("DockerTag")
    .Does(() => {     
   DockerPush($"{Docker.Username}/{Docker.Repository}");   
});

Task("Restore")
    .Does(() =>
{
    DotNetCoreRestore(Paths.SolutionFile.FullPath);
}); 

Task("Build")
    .IsDependentOn("Restore")
    .Does(() =>
{
    DotNetCoreBuild(
        Paths.ProjectFile.FullPath,
        new DotNetCoreBuildSettings
        {
            Configuration = configuration
        });
});
Task("Test")
    .IsDependentOn("DockerCompose")
    .IsDependentOn("Restore")    
    .Does(() =>
{
    DotNetCoreTest(Paths.TestProjectFile.FullPath);
});
RunTarget(target); 
```

> 注意 RunTarget(target)应该在 build.cake 文件的最后。我们可以定义在文件的开头运行哪个目标，或者在运行构建时通过传递它来覆盖它。

### 现在，要在 CI 服务器上运行这个项目，我们需要有与服务器相关的单独的 yml 文件，它将告诉服务器它需要哪个服务以及如何执行蛋糕构建。

## 为特拉维斯词(`.travis.yml`)

```
dist: xenial 
sudo: required 
language: csharp 
mono: none 
dotnet: 2.2.101 
solution: AspNetCoreDevOps.sln 
services: 
- docker 
script: 
- dotnet tool install -g Cake.Tool  
- export PATH=$HOME/.dotnet/tools:$PATH 
- dotnet cake build.cake 
```

这里，我们还安装了用于蛋糕构建的 dotnet 全局工具，并将其添加到路径中。在最后一行，我们执行蛋糕构建来运行`build.cake`文件。

```
dotnet cake build.cake 
```

## 为送货员(`.appveyor.yml`)

```
version: '1.0.{build}' 
image: ubuntu 
environment: 
DOTNET_SKIP_FIRST_TIME_EXPERIENCE: 1 
DOTNET_CLI_TELEMETRY_OPTOUT: 1 
services: 
- docker 
install: 
- sudo curl -L "https://github.com/docker/compose/releases/download/1.23.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
- sudo chmod +x /usr/local/bin/docker-compose 
- wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
- sudo dpkg -i packages-microsoft-prod.deb 
- sudo apt-get install apt-transport-https 
- sudo apt-get update 
- sudo apt-get -y install dotnet-sdk-2.2 
# Install repo specific stuff here 
before_build: 
- dotnet tool install -g Cake.Tool 
build_script: 
- dotnet cake build.cake 
deploy: off 
```

在写这篇文章的时候。net core 2.2 没有预装在 appveyor 上，但是 project 的目标是。net core 2.2，所以我必须手动安装。Appveyor 安装了 2.1 版，因为 2.1 版支持全局工具，所以我们不需要在路径中添加全局工具。

## 为天蓝色的线(`.appveyor.yml`)

```
pool:
   vmImage: 'Ubuntu 16.04'   
variables:
   buildConfiguration: 'Release' 
steps:
  - task: DotNetCoreInstaller@0
    inputs:
       version: '2.2.100' # replace this value with the version that you need for your project
  - script: |
       dotnet tool install -g Cake.Tool
       dotnet cake build.cake 
```

在 azure pipeline 上，在写这篇文章的时候，还没有预装的。net 2.2，所以我不得不使用 DotnetCoreInstaller 来安装它，但我们不必将全局工具导入到 path，因为 2.1 已经安装了，并且 2.1 支持全局工具。

> 在我们的本地机器上，我们不必按照当前的限制将它添加到路径中，我们必须关闭 cmd 并再次打开，以便自动将它添加到路径中，但是我们不能在 ci 服务器上这样做，所以我们必须手动将其添加到该路径中。

源代码。
[https://github.com/iAmBipinPaul/AspNetCoreDevOps/](https://github.com/iAmBipinPaul/AspNetCoreDevOps/)

谢谢大家！
编码快乐！