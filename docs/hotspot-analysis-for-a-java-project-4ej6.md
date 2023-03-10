# Java 项目的热点分析

> 原文：<https://dev.to/janux_de/hotspot-analysis-for-a-java-project-4ej6>

软件项目往往变得非常复杂，难以维护。在重构的帮助下，代码可以变得相对容易理解。然而，在现实世界的项目中，似乎不可能处处都有非常高的代码质量。

对于程序的热点，即具有高复杂性和高变更率的位置，重构工作很可能具有最高的投资回报。这篇博文探索了如何在 Java 项目中识别它们的方法。

Apache Wicket 被选为示例项目。为了对其进行分析，使用了开源工具 [SonarCube](https://www.sonarqube.org/) 、 [Code Charta](https://github.com/MaibornWolff/codecharta) 和 [Code 玛特](https://github.com/adamtornhill/code-maat)以及 [Python](https://www.python.org/) 脚本。复杂性是用 SonarCube 的[认知复杂性](https://www.sonarsource.com/resources/white-papers/cognitive-complexity.html)度量和特定文件的提交数量的变化率来衡量的。

# 环境

下面的步骤是在一个[数字海洋液滴](https://cloud.digitalocean.com/marketplace/5ba19751fc53b8179c7a0071?i=73c749)中完成的，除了最后的可视化。

*   Ubuntu 18.04
*   预装对接器

# 准备

## 下载所需的仓库

在此过程中，将需要以下存储库:

```
mkdir ~/src && cd ~/src
git clone https://github.com/apache/wicket.git
git clone https://github.com/adamtornhill/code-maat.git
git clone https://github.com/experimental-software/code-analytics.git 
```

Enter fullscreen mode Exit fullscreen mode

## 刀具设置

SonarCube 服务器可以用这个`docker run`指令启动:

```
docker run --env ES_JAVA_OPTS="-Xms750m -Xmx750m" -d -p 9000:9000 -p 9092:9092 sonarqube 
```

Enter fullscreen mode Exit fullscreen mode

稍后我们还需要一些系统工具:

```
apt update && apt install maven npm leiningen unzip -y 
```

Enter fullscreen mode Exit fullscreen mode

代码 Charta 需要通过节点包管理器安装:

```
npm install codecharta-analysis -g
npm install codecharta-visualization -g 
```

Enter fullscreen mode Exit fullscreen mode

Code Mate 没有二进制发行版，需要自己编译:

```
cd ~/src/code-maat
lein uberjar
mkdir ~/bin
cp target/code-maat-1.1-SNAPSHOT-standalone.jar ~/bin/code-maat.jar 
```

Enter fullscreen mode Exit fullscreen mode

通过安装`sonar-scanner` CLI 工具，我们可以在不改变配置的情况下导入 Java 项目:

```
cd ~/bin

curl https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-3.3.0.1492-linux.zip \
    > sonar-scanner-cli-3.3.0.1492-linux.zip

unzip sonar-scanner-cli-3.3.0.1492-linux.zip 

PATH="$PATH:~/bin/sonar-scanner-3.3.0.1492-linux/bin" 
```

Enter fullscreen mode Exit fullscreen mode

# 分析

现在我们可以开始代码分析了。第一步是将 Java 项目导入 SonarCube:

```
cd ~/src/wicket

mvn install

sonar-scanner -Dsonar.projectKey=wicket \
    -Dsonar.source=. -Dsonar.java.binaries=. \
    -Dsonar.exclusions='**/*.js,**/*.xml'

curl http://localhost:9000/api/ce/task?id=AWrweUgHuDEJjgfjbZpz \
    | jq '.task.status' 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将 SonarCube 中的数据提取到一个 JSON 文件中，这是最终代码可视化的基础:

```
ccsh sonarimport http://localhost:9000 wicket > /tmp/sonar.json 
```

Enter fullscreen mode Exit fullscreen mode

对每个文件提交次数的计数将通过代码玛特来完成，它将`git log`作为输入:

```
cd ~/src/wicket

git log --since="1 year" --all --numstat --date=short \
    --pretty=format:'--%h--%ad--%aN' --no-renames \
    > /tmp/gitlog.txt

java -jar ~/bin/code-maat.jar --log /tmp/gitlog.txt \
    --version-control git2 --analysis revisions \
    > /tmp/codemaat-revisions.txt 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以用代码玛特提取的信息来充实`sonar.json`文件，并添加一个小的助手脚本:

```
cd ~/src/code-analytics

python enrich_codecharta_data.py --sonar-json /tmp/sonar.json \
    --codemaat-csv /tmp/codemaat-revisions.txt 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以启动代码图表可视化，在其 GUI 中加载`sonar.json`,并选择我们想要可视化的指标。

```
codecharta-visualization 
```

Enter fullscreen mode Exit fullscreen mode

# 结果

[![](img/3d529b33dffd2010dd101d64f75b0a72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XZ-V0MoL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/10rvwk53izy83uqwh3w4.png)