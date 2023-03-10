# 自定义 git 凭据帮助程序

> 原文：<https://dev.to/pratham/custom-git-credential-helper-pfa>

### Git 凭证助手:

Git 凭据帮助器用于保存用户凭据，这样用户就不需要在每次 git 操作中输入凭据。
Git 提供了一些默认的 Git 凭证助手，[查看如何使用它们](https://git-scm.com/docs/gitcredentials)。
这篇博客将展示如何构建定制的 git 凭证助手。

**简单的 python cli:**

*   让我们编写一个名为`auth_helper.py`的 python cli

```
 #!/usr/bin/env python
  import argparse

  class Credential(object):
      def get(self):
          # logic to get username/password from auth file
          pass
      def store(self):
          # logic to store username/password to auth file
          # its better to encrypt password if its in plain text
          pass
      def erase(self):
          # logic to delete auth file
          pass

  def main():
      parser = argparse.ArgumentParser()
      parser.add_argument('operation', action="store", type=str,
              help="Git action to be performed (get|store|erase)")
      # parser all arguments
      arguments = parser.parse_args()
      # get credentials
      credentials = Credential()

      if arguments.operation == "get":
          creds = credentials.get()
          print("username={0}".format(creds.get("username")))
          print("password={0}".format(creds.get("password")))
      elif arguments.operation == "store":
          credentials.store()
          # if credentials are already stored do not store again
      elif arguments.operation == "erase":
          credentials.erase()
      else:
          print "Invalid git operation"

  if __name__ == "__main__":
      main() 
```

Enter fullscreen mode Exit fullscreen mode

*   这个 cli 将三个 git 操作作为 cli 参数`get`、`store`和`erase`。
*   这些参数不是重合的，它们都是 git 使用的。让我们更多地了解他们。

**Git 凭证存储:**

*   Git 凭证存储查找三个参数
    *   `get`:触发 git 拉取、git 取取、git 推取等时调用。
    *   `store`:触发 git 拉取、git 取取、git 推取等时调用。
    *   `erase`:如果我们提供的凭证失败，git 将退回到它自己的凭证提示，如果调用`erase`也失败。

**Git 助手配置:**

*   要将我们的 cli 配置为 git 助手，请触发以下命令

```
 git config --global credential.https://git.company.com.helper "./path/to/cli/auth_helper.py" 
```

Enter fullscreen mode Exit fullscreen mode

*   这将在`.gitconfig`文件(用户的主目录)的`credential`部分下定义`auth_helper.py`。
*   `https://git.company.com`是托管 git 的域。

**搞定！！**下次我们使用 git 时，我们的`auth_helper.py`应该为 git 授权提供凭证。