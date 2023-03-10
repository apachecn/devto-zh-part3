# 将 Jenkins 设置为代码

> 原文：<https://dev.to/simplytunde/setting-up-jenkins-as-code-2ca6>

好的，我们这里的目标是通过单击按钮部署 jenkins，并配置好我们的作业。我们的秘密武器是 jenkins**configuration-as-code-plugin**(JCasC)，它允许你在 YAML 文件或文件夹中定义你的 Jenkins 设置。
问题是，我们想使用 JCasC 来配置 jenkins，但我们需要提前安装 JCasC 插件来为我们完成这项工作。谢天谢地，我们有一个解决方案。我们将使用 Jenkins 内置的进程来安装插件。

#### 安装插件

```
workflow-aggregator:latest
blueocean:latest
pipeline-maven:latest
configuration-as-code-support:latest
job-dsl:latest 
```

对于那些使用 kubernetes 安装 jenkins 的人，你需要更新你的头盔值[文件](https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos/kubernetes-helm)。现在，让我们开始吧；

```
#plugins.txt
workflow-aggregator:2.6
blueocean:1.16.0
pipeline-maven:3.6.11
configuration-as-code-support:1.14
job-dsl:1.74
workflow-job:2.32
credentials-binding:1.18
git:3.10.0 
```

#### 构建和配置

```
jenkins:
  systemMessage: "I  did  this  using  Jenkins  Configuration  as  Code  Plugin  \n\n"
tool:
  git:
    installations:
    - home: "git"
      name: "Default"
  maven:
    installations:
    - name: "Maven  3"
      properties:
      - installSource:
          installers:
            - maven:
                id: "3.5.4"
jobs:
  - script: >
      pipelineJob('pipeline') {
          definition {
              cpsScm {
                  scriptPath 'Jenkinsfile'
                  scm {
                    git {
                        remote { url 'https://github.com/mkrzyzanowski/blog-001.git' }
                        branch '*/docker-for-mac'
                        extensions {}
                    }
                  }
              }
          }
      } 
```

这些是我们试图安装的插件，以及我们希望我们的詹金斯设置。这是 Docker 映像构建，负责为我们安装。

```
#Dockerfile
FROM jenkins/jenkins:lts
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt 
```

一旦构建了映像，我们需要一种方法让 JCasC 知道我们的配置文件的位置(大多数情况下命名为 jenkins.yaml)。

*   将 **jenkins.yaml** 文件复制到/var/jenkins_home/。默认情况下，它会查找该文件
*   使用**CASC _ 詹金斯 _ 配置**环境变量指向文件位置，该位置可以是以下任何一个；
    *   文件路径(/my/path/jenkins.yaml)
    *   文件夹路径(/my/path/jenkins_casc_configs/)
    *   配置文件的 URL 路径([https://example.com/git/jenkins.yaml](https://example.com/git/jenkins.yaml))

对于这个例子，我将用 docker
将 jenkins.yaml 挂载到/var/jenkins_home

```
$ docker run --name jenkins -p -d 8081:8080 -v $(pwd):/var/jenkins_home my_jenkins_image
Running from: /usr/share/jenkins/jenkins.war
webroot: EnvVars.masterEnvVars.get("JENKINS_HOME")
May 08, 2019 12:00:19 AM org.eclipse.jetty.util.log.Log initialized
INFO: Logging initialized @612ms to org.eclipse.jetty.util.log.JavaUtilLog
May 08, 2019 12:00:19 AM winstone.Logger logInternal
INFO: Beginning extraction from war file
May 08, 2019 12:00:40 AM org.eclipse.jetty.server.handler.ContextHandler setContextPath
WARNING: Empty contextPath
May 08, 2019 12:00:40 AM org.eclipse.jetty.server.Server doStart
INFO: jetty-9.4.z-SNAPSHOT; built: 2018-08-30T13:59:14.071Z; git: 27208684755d94a92186989f695db2d7b21ebc51; jvm 1.8.0_212-8u212-b01-1~deb9u1-b01
May 08, 2019 12:00:47 AM org.eclipse.jetty.webapp.StandardDescriptorProcessor visitServlet
INFO: NO JSP Support for /, did not find org.eclipse.jetty.jsp.JettyJspServlet
May 08, 2019 12:00:47 AM org.eclipse.jetty.server.session.DefaultSessionIdManager doStart
INFO: DefaultSessionIdManager workerName=node0
May 08, 2019 12:00:47 AM org.eclipse.jetty.server.session.DefaultSessionIdManager doStart
INFO: No SessionScavenger set, using defaults
May 08, 2019 12:00:47 AM org.eclipse.jetty.server.session.HouseKeeper startScavenging
INFO: node0 Scavenging every 660000ms
Jenkins home directory: /var/jenkins_home found at: EnvVars.masterEnvVars.get("JENKINS_HOME")
May 08, 2019 12:00:50 AM org.eclipse.jetty.server.handler.ContextHandler doStart
INFO: Started w.@a50b09c{Jenkins v2.164.2,/,file:///var/jenkins_home/war/,AVAILABLE}{/var/jenkins_home/war}
May 08, 2019 12:00:50 AM org.eclipse.jetty.server.AbstractConnector doStart
INFO: Started ServerConnector@5a38588f{HTTP/1.1,[http/1.1]}{0.0.0.0:8080}
May 08, 2019 12:00:50 AM org.eclipse.jetty.server.Server doStart
INFO: Started @31513ms
May 08, 2019 12:00:50 AM winstone.Logger logInternal
INFO: Winstone Servlet Engine v4.0 running: controlPort=disabled
May 08, 2019 12:00:53 AM jenkins.InitReactorRunner$1 onAttained
INFO: Started initialization
May 08, 2019 12:02:20 AM hudson.ClassicPluginStrategy createClassJarFromWebInfClasses
WARNING: Created /var/jenkins_home/plugins/job-dsl/WEB-INF/lib/classes.jar; update plugin to a version created with a newer harness
May 08, 2019 12:02:36 AM jenkins.InitReactorRunner$1 onAttained
INFO: Listed all plugins
May 08, 2019 12:02:58 AM jenkins.InitReactorRunner$1 onAttained
INFO: Prepared all plugins
May 08, 2019 12:02:58 AM jenkins.InitReactorRunner$1 onAttained
INFO: Started all plugins
May 08, 2019 12:03:09 AM jenkins.InitReactorRunner$1 onAttained
INFO: Augmented all extensions
May 08, 2019 12:03:10 AM io.jenkins.plugins.casc.impl.configurators.DataBoundConfigurator tryConstructor
INFO: Setting class hudson.plugins.git.GitTool.name = Default
May 08, 2019 12:03:10 AM io.jenkins.plugins.casc.impl.configurators.DataBoundConfigurator tryConstructor
INFO: Setting class hudson.plugins.git.GitTool.home = git
May 08, 2019 12:03:10 AM io.jenkins.plugins.casc.impl.configurators.DataBoundConfigurator tryConstructor
INFO: Setting class hudson.tasks.Maven$MavenInstallation.name = Maven 3
May 08, 2019 12:03:10 AM io.jenkins.plugins.casc.impl.configurators.DataBoundConfigurator tryConstructor
INFO: Setting class hudson.tasks.Maven$MavenInstaller.id = 3.5.4
May 08, 2019 12:03:10 AM io.jenkins.plugins.casc.impl.configurators.DataBoundConfigurator tryConstructor
INFO: Setting class hudson.tools.InstallSourceProperty.installers = [{maven={}}]
May 08, 2019 12:03:10 AM io.jenkins.plugins.casc.impl.configurators.DataBoundConfigurator tryConstructor
INFO: Setting class hudson.tasks.Maven$MavenInstallation.properties = [{installSource={}}]
May 08, 2019 12:03:11 AM io.jenkins.plugins.casc.Attribute setValue
INFO: Setting hudson.model.Hudson@4fbfd7e4.systemMessage = I did this using Jenkins Configuration as Code Plugin 

Processing provided DSL script
May 08, 2019 12:03:15 AM javaposse.jobdsl.plugin.JenkinsJobManagement createOrUpdateConfig
INFO: createOrUpdateConfig for pipeline
May 08, 2019 12:03:16 AM io.jenkins.plugins.casc.impl.configurators.DataBoundConfigurator tryConstructor
INFO: Setting class hudson.plugins.git.GitTool.name = Default
May 08, 2019 12:03:16 AM io.jenkins.plugins.casc.impl.configurators.DataBoundConfigurator tryConstructor
INFO: Setting class hudson.plugins.git.GitTool.home = git
May 08, 2019 12:03:16 AM io.jenkins.plugins.casc.Attribute setValue
INFO: Setting hudson.plugins.git.GitTool$DescriptorImpl@7d18607f.installations = [GitTool[Default]]
.... 
```

这是我们新配置的詹金斯的截图。

[![Jenkins setup](img/852e2447453bbaac04fc3ea6d921b755.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wJShQpyX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j2bb528d06hramjrlhrf.png)

自动化快乐！！！