# Java 开发人员的自动化——技巧和片段

> 原文：<https://dev.to/tomavelev/automation-for-java-developers---tips-and-snippets-433p>

自动化可以为你未来的自己节省很多时间。这不是一件紧急的事情，但却非常重要。它会给你时间处理业务逻辑，执行某些无聊的事情，当代码被修改时保护你免受自己和他人的伤害，当某些东西被破坏时得到早期通知，给用户带来更好的体验(更少的错误),最后会给你自由，不用担心低级的技术问题，更好地享受生活。

**借力风味**。这些是为您的应用程序将运行的不同[环境](http://tomavelev.com/blog/Software%20Environments?en_US)预定义的常量、参数，甚至代码(尽管最好是最小的)。在 Java 桌面和服务器上，这样做的标准方式通常是用。属性文件。所有不同的东西——数据库连接、文件系统上的路径、JVM 参数、命令等等都可以放在代码之外。当把应用程序移动到一个新的地方时，这将最小化额外更改的需要(尽管这对于容器来说不是太大的问题)。

安卓口味的是一等公民。运行时系统从当前操作系统环境的设置中选择资源、图像、菜单、布局文件、值、颜色。在编译时，也可以定义风味。从主源文件夹创建相同的文件结构——覆盖一些特定的文件，它可以替换 URL 端点、数据访问凭证、签名证书、资源和代码。编译时，您可以选择将哪种风格捆绑到安装包中。

让我们来看看我正在使用的一些具体的技术片段:

我已经使用 SSHJ[https://github.com/hierynomus/sshj](https://github.com/hierynomus/sshj)下载或上传文件到远程服务器:

```
try (SSHClient ssh = new SSHClient()) {
 ssh.loadKnownHosts();        
 ssh.addHostKeyVerifier("some-key");
 ssh.connect(host);
 ssh.authPassword(user, pass);
 ssh.newSCPFileTransfer().upload(localFilePath, remoteFilePath);
 ssh.disconnect();
}
```

我已经使用 sshxcute[https://code.google.com/archive/p/sshxcute/](https://code.google.com/archive/p/sshxcute/)在服务器上完成了一堆任务——将特定文件复制到它们需要的位置，压缩目录，触发数据库备份，删除旧文件，等等。

```
CustomTask task = new ExecCommand("some command");
ConnBean cb = new ConnBean(host, user, pass);
SSHExec ssh1 = SSHExec.getInstance(cb);
ssh1.connect();
ssh1.exec(task);
ssh1.disconnect();
```

我已经使用 Java Process API 在子流程中执行了一些脚本或命令，并且根据返回的结果，根据需要，我随后执行了不同的代码。

```
processBuilder = new ProcessBuilder(list of commands);
processBuilder.directory(working directory);
processBuilder.inheritIO();
process = processBuilder.start();
int waitFor = process.waitFor();
if (waitFor == 0) {
//something
} else {
//something else 
}
```

在构建或者在其他地方，我用下面的代码片段替换了不同环境下不同风格的配置文件:

```
Files.copy(sourceFile.toPath(), destFile.toPath(), StandardCopyOption.REPLACE_EXISTING);
```

构建本身是用 Gradle、Maven 和/或 Ant 完成的。这些工具的位置，有时还有它们的参数都在属性文件中，因此，将构建迁移到新机器上只是替换. properties 文件中的一些行。

在这三个工具中，我最喜欢 Ant，因为它可以让你完全控制构建过程，而不会隐藏太多。其他人非常擅长管理依赖项，但是他们的构建过程是用插件完成的，有时一些步骤是隐藏的，这可能是好的，但有时可能是坏的(特别是如果有些东西不工作)或者你想定制一些东西。

我在几个地方使用了 Eclipse JGit API 来访问本地和远程存储库。[https://www.eclipse.org/jgit/](https://www.eclipse.org/jgit/)

```
 SshSessionFactory.setInstance(new JschConfigSessionFactory()
    {
        @Override
        protected void configure(Host arg0, Session session)
        {
            session.setPassword("the password of a user");
        }
    });
    File file = new File("local directory");
    delete(file);
    Git.cloneRepository().setURI("user@someaddr:/remoteaddr")
    .setDirectory(file).call();
```

我已经使用 Java Zip API 将一些文件打包到 Zip 中。

```
 Path sourceDir = Paths.get(dirPath);
    String zipFileName = "some file name;
    try {
        ZipOutputStream outputStream = new 
ZipOutputStream(new FileOutputStream(zipFileName));
        Files.walkFileTree(sourceDir, new SimpleFileVisitor<Path>() {
            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attributes) {
                try {
                    Path targetFile = sourceDir.relativize(file);
                    outputStream.putNextEntry(new ZipEntry(targetFile.toString()));
                    byte[] bytes = Files.readAllBytes(file);
                    outputStream.write(bytes, 0, bytes.length);
                    outputStream.closeEntry();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                return FileVisitResult.CONTINUE;
            }
        });
        outputStream.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
```

使用 [Jenkins](https://jenkins.io/) 应用程序或工具中的一些命令(例如，我在某些情况下使用 Ant 来完成某些内容)，只需很少的配置就可以完成上面的一些内容，但是我发现将 Jenkins 功能与标准命令和 java 可执行应用程序混合起来是很有用的——由我编码，当我可以在这个工具中找到功能或插件时，做我需要做的事情。在这个 CI/CD app 的任务配置中我特别用了:

*   使用 Cron 任务调度器每隔 X 分钟/在某个时间段开始构建。
*   使用 git 提交挂钩执行构建[https://stack overflow . com/questions/12794568/how-to-configure-git-post-commit-hook](https://stackoverflow.com/questions/12794568/how-to-configure-git-post-commit-hook)
*   构建成功完成后，从终端执行命令

通过一些配置、一些小代码片段或一些额外的工具，可能有更多的东西可以自动化。但那是对我来说的。如果你知道并使用一些很酷的自动化工具，请告诉我。