# 从代码中生成一个詹金斯

> 原文：<https://dev.to/pencillr/spawn-a-jenkins-from-code-gfa>

## 隔着 CI 窥镜

在企业 CI 基础架构中，配置是关键。

当谈到[詹金斯](https://jenkins.io/)时，我们可以很容易地把它**视为仅仅是一个被美化的 crontab jobs** 的集合。只是后来我们意识到它变得更大，它管理的**敏感数据和配置**比我们最初意识到的更多。

凭证、主持人信息、工作描述、测试和生产工作流定义，以及保持节目继续的所有特殊设置和插件。
使用[工作 DSL](https://github.com/jenkinsci/job-dsl-plugin) 是 **CI 团队在与 Jenkins 合作时必须实现的第一个重大里程碑**。这至少给我们提供了一些保障。使用 Job DSL，我们的作业定义作为我们产品管道的蓝图可以在版本控制中维护。这确实减轻了 DevOps 的负担。

如果你愿意，它能让我们内心平静。

**还是**，在我们的 Jenkins 的壁炉里有一个恶毒的秘密，那个秘密就是我们在配置页面上通过繁琐的手工工作完成的**配置。如果我们的服务器碰巧被*打败了*，我们可能会努力找出如何让我们的工作在一个普通的 Jenkins 中再次工作，对这些隐含的依赖一无所知，这些依赖是一些*特殊的*端口配置的结果，或者是几年前由某个当时甚至不在公司的*家伙*完成的插件安装。在这样一个不方便的事故之后，恢复 CI 基础设施可能是一件相当麻烦的事情。**

> 我将在这里深入探讨一个更高级的主题，所以如果您是 Jenkins 的新手，最好从头开始。这不是一份文件。
> 来吧。不要对自己撒谎。你只能通过做事情来学习。只需安装一个詹金斯，并开始打破它在不同的方式。说真的。

* * *

## 不按说明书办事

让我们这样宣布我们的目标:

##### *直接从代码中设置一个 Jenkins，使用保存在文件中的配置，这样就可以进行版本控制。*

我们想要一个可脚本化的过程，可以从命令行用一个命令和几个参数来触发。

> *有一点*我必须补充:
> 在做*零到英雄*的自动化詹金斯设置时，这里有很多隐含的依赖关系需要注意。存在一定的缺陷(比如处理必要的依赖关系，管理我们的插件需求与所选 Jenkins 版本的兼容性，或者在通过 cli 配置时弄清楚如何控制 Jenkins 的 java 实例或某个插件)。这里我不会给你一个完整的解决方案。如果你愿意，可以把它看作是一个想法的集合，或者是指导。这与我的团队在构建我们自己的配置即代码解决方案时使用的流程相同。

## 我们来看看流程

在我们开始编写代码之前，您可能还想看看[新的配置代码插件](https://github.com/jenkinsci/configuration-as-code-plugin)，它对维护 Jenkins 配置有很大帮助。不管怎样，根据传闻，它可能很快会成为詹金斯的核心部件。

### 第一步:获取你的詹金斯战争文件。

没有詹金斯运行没有必要的二进制。你可以从这里得到:[https://updates.jenkins-ci.org/download/war/](https://updates.jenkins-ci.org/download/war/)

### 创建您的定制詹金斯家

我们将从开始*。从字面上看，我们将创建一个新的空目录`.jenkins`作为我们未来的詹金斯的家，我们将*以一种*的方式填充它，它可以被詹金斯解释为一个基本的詹金斯配置。对它来说，为了能够作为我们新的 Jenkins 实例的主目录，它应该看起来像这样:* 

```
|-- config.xml
|-- jenkins.install.InstallUtil.lastExecVersion
|-- org.jenkinsci.main.modules.sshd.SSHD.xml
`-- users
|   |-- pencillr_xxxxxxxxxxxxxxxxxxx
|   |   `-- config.xml
    `-- users.xml 
```

Enter fullscreen mode Exit fullscreen mode

当我们经历这个过程时，上面列出的所有文件将被详细解释。

**让我们从创建主目录开始:**

```
jenkins_home="/home/$USER/.jenkins"
mkdir "${jenkins_home}" 
```

Enter fullscreen mode Exit fullscreen mode

如果你曾经启动过 Jenkins，你会知道首先有一个**设置过程，通过它你可以创建你的管理用户**。所有我们想要的本质上都是*预先创造的*。

首先，我们必须*避免*詹金斯开始这个设置过程。我们通过将我们选择的 Jenkins 版本添加到我们新创建的`jenkins.install.InstallUtil.lastExecVersion`文件中来实现这一点。

```
jenkins_version=2.175.1 # your Jenkins version, see: https://jenkins.io/changelog/
echo "$jenkins_version" > "${jenkins_home}"/jenkins.install.InstallUtil.lastExecVersion 
```

Enter fullscreen mode Exit fullscreen mode

### 添加用户记录

我们通过创建代表管理员用户帐户的配置文件**，从代码中添加管理员用户。我们在 Jenkins 目录中创建了包含`users.xml`文件的`users`目录。Jenkins 从版本 2.150.1 开始使用保存用户名- >配置文件目录映射的`users.xml`文件。我们将遵循这一惯例。** 

```
username=pencillr # the admin username you like
mkdir "${jenkins_home}/users"
touch "${jenkins_home}/users/users.xml"
mkdir "${jenkins_home}/${username}_1234567890123456789" # notice that this is an arbitrary hash
touch "${jenkins_home}/${username}_1234567890123456789/config.xml" 
```

Enter fullscreen mode Exit fullscreen mode

我们在`"${jenkins_home}"/users`下的 config.xml 将是这样的，使用我们在@username@:
的用户名

```
<?xml version='1.1' encoding='UTF-8'?>
<hudson.model.UserIdMapper>
  <version>1</version>
  <idToDirectoryNameMap class="concurrent-hash-map">
    <entry>
      <string>@username@</string>
      <string>@username@_1234567890123456789</string>
    </entry>
  </idToDirectoryNameMap>
</hudson.model.UserIdMapper> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们跳到我们的`"${jenkins_home}/users/${username}_1234567890123456789"`目录下的用户配置定义。我们需要一个`config.xml`文件来代表我们的管理员用户帐户。这不是一个普通的用户注册程序，所以你必须自己做密码散列。我们可以做到这一点，也可以添加密码盐，使我们的配置更加安全。

```
pass_salt=$(< /dev/urandom tr -dc 'a-zA-Z0-9' | fold -w 16 | head -n 1)
pass_hash=$(echo "${jenkins_password}{${jenkins_password_salt}}" | \
    sha256sum -b | cut -f 1 -d " ") 
```

Enter fullscreen mode Exit fullscreen mode

我们想要一个 ssh 可访问的 jenkins，所以我们也在配置文件中添加了我们的公钥，最终看起来像这样:

```
<?xml version='1.0' encoding='UTF-8'?>
<user>
  <properties>
    <hudson.security.HudsonPrivateSecurityRealm_-Details>
      <passwordHash>$pass_salt:$pass_hash</passwordHash>
    </hudson.security.HudsonPrivateSecurityRealm_-Details>
    <org.jenkinsci.main.modules.cli.auth.ssh.UserPropertyImpl>
      <authorizedKeys>$ssh_public_key</authorizedKeys>
    </org.jenkinsci.main.modules.cli.auth.ssh.UserPropertyImpl>
  </properties>
</user> 
```

Enter fullscreen mode Exit fullscreen mode

### SSH 可访问性

我们的计划是在 Jenkins 实例运行后通过 CLI 注入配置。为此，我们必须明确允许 [ssh 访问](https://jenkins.io/doc/book/system-administration/security/)API。我们只需将所需的 ssh 端口添加到`"$jenkins_home"/org.jenkinsci.main.modules.sshd.SSHD.xml`文件中。

```
<?xml version='1.0' encoding='UTF-8'?>
<org.jenkinsci.main.modules.sshd.SSHD>
  <port>$jenkins_sshd_port</port>
</org.jenkinsci.main.modules.sshd.SSHD> 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是在 Jenkins 中启用我们首选的安全设置。我们在主配置文件`"$jenkins_home"/config.xml`中这样做。

```
<?xml version='1.0' encoding='UTF-8'?>
<hudson>
  <authorizationStrategy class="hudson.security.FullControlOnceLoggedInAuthorizationStrategy">
    <denyAnonymousReadAccess>true</denyAnonymousReadAccess>
  </authorizationStrategy>
  <securityRealm class="hudson.security.HudsonPrivateSecurityRealm">
    <disableSignup>true</disableSignup>
    <enableCaptcha>false</enableCaptcha>
  </securityRealm>
</hudson> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了基本的 Jenkins 设置。在新创建的主目录上启动 Jenkins，如下:

```
java -DJENKINS_HOME=$JENKINS_HOME jar jenkins.war --httpPort=$PORT" 
```

Enter fullscreen mode Exit fullscreen mode

### 设置插件。

因为我们有一个 ssh 可访问的 Jenkins，所以我们可以通过 ssh 在 Jenkins 上进行所有配置。要查看您可以在自己的 Jenkins 上使用哪些 CLI 命令，请打开浏览器并转到 localhost:$PORT/cli。您还必须使用新创建的凭据登录。之后，我们就可以运行我们的命令了。

> 顺便提一下，确实有一个用于此目的的 [CLI 客户端](https://jenkins.io/doc/book/managing/cli/)，但是这里为了简单起见，我们将不使用那个 jar 文件。

您可以通过检查 Jenkins 的会话 id 来测试 CLI 是否可通过 ssh 访问:

```
# ssh port is the one you defined in the SSHD file
ssh -p $SSH_PORT $USER@localhost session-id 
```

Enter fullscreen mode Exit fullscreen mode

安装插件就像 CLI 帮助建议的那样简单:

`ssh -p $SSH_PORT $USER@localhost install-plugin SOURCE ... [-deploy] [-name VAL] [-restart]`

参见 CLI 帮助，非常简单:

```
 SOURCE    : If this points to a local file (?-remoting? mode only), that file
             will be installed. If this is an URL, Jenkins downloads the URL
             and installs that as a plugin. If it is the string ?=?, the file
             will be read from standard input of the command, and ?-name? must
             be specified. Otherwise the name is assumed to be the short name
             of the plugin in the existing update center (like ?findbugs?), and
             the plugin will be installed from the update center. If the short
             name includes a minimum version number (like ?findbugs:1.4?), and
             there are multiple update centers publishing different versions,
             the update centers will be searched in order for the first one
             publishing a version that is at least the specified version.
 -deploy   : Deploy plugins right away without postponing them until the reboot.
 -name VAL : If specified, the plugin will be installed as this short name
             (whereas normally the name is inferred from the source name
             automatically).
 -restart  : Restart Jenkins upon successful installation. 
```

Enter fullscreen mode Exit fullscreen mode

## 通过 CLI 应用配置

您可以简单地通过 *groovy 脚本*将 config 应用到 Jenkins。谈到可访问性，Jenkins 非常棒。你实际上可以通过脚本控制台访问插件的 Java 实例以及[詹金斯本身](https://javadoc.jenkins-ci.org/jenkins/model/Jenkins.html)。你可以通过 ssh 用下面的方法注入任何 groovy 脚本:

```
ssh -p $SSH_PORT $USER@localhost groovy = < "${your_groovy_script_file} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看一个例子，我们通过注入一个简单的脚本来设置 Jenkins 的 scm 重试计数，这样我们的 Jenkins 中就有了一个更冗余的结账逻辑。

```
#!/usr/bin/env groovy

import jenkins.model.Jenkins

def jenkins = Jenkins.getInstanceOrNull()
jenkins.setScmCheckoutRetryCount(5)
jenkins.save()
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子:这里我们确保 timestamps 插件将时间戳全局添加到所有管道控制台日志中。

```
#!/usr/bin/env groovy

import hudson.plugins.timestamper.TimestamperConfig

//  Enable timestamper plugin globally
def timestamper = TimestamperConfig.get()
if (!timestamper.isAllPipelines()) {
    timestamper.setAllPipelines(true)
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过滚动浏览 [Java 文档](https://javadoc.jenkins.io/)来发现 Jenkins 实例及其插件的接口。这不是我所知道的公园里的*散步*。

## 创造就业岗位

我们现在所需要的就是使用我们的 JobDSL 定义来创建我们的工作。

> 很遗憾，我们现在没有任何工作，但我们可以通过一个简单的技巧来获得种子工作。您只需要手动创建一个种子作业，然后将其 xml 表示保存到一个文件中。然后，在您的脚本中，您可以插入这个 xml 文件作为种子作业创建。您也可以通过 CLI 触发它。

```
ssh -p $SSH_PORT $USER@localhost create-job "$SEED_JOB_NAME" "${seed_job_as_xml}" 
```

Enter fullscreen mode Exit fullscreen mode

## 利润

的确，这有点不合常规，但有时人们必须走很远的路，以确保公司层面的冗余。创建 groovy 配置脚本可能是一项单调乏味的工作，但是值得一试。我还要补充一点，在你一次点击就能完美地拉起或拆掉你的詹金斯之前，可能会有很多陷阱。