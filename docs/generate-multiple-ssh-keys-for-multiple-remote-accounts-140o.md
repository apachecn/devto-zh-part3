# 为多个远程帐户生成多个 SSH 密钥

> 原文：<https://dev.to/l579/generate-multiple-ssh-keys-for-multiple-remote-accounts-140o>

如果您有多个 Bitbucket 帐户(假设一个用于您的公司，一个用于私人)，另外还有一个 Github 帐户，您都希望使用 SSH 进行身份验证，您可以按照这个指南将所有帐户添加到您的本地机器，这样您就不必再为不同的帐户而烦恼了。

## 生成 SSH 密钥

打开您的终端并导航到您的主目录。首先生成您的第一个默认 SSH 密钥。

```
ssh-keygen -t rsa
ssh-add ~/.ssh/id_rsa 
```

将密钥存储在默认位置。为每个额外的帐户生成新的 SSH 密钥，但是注意不要通过给它一个新名称来覆盖先前生成的密钥。

```
ssh-keygen -t rsa -C "companyName"
ssh-add ~/.ssh/companyName 
```

将此密钥存储在`companyName`下，这样当询问您将它存储在哪里时，请输入位置，但要交换文件名。

```
/Users/user/.ssh/companyName 
```

## 配置

创建一个配置文件或修改现有文件。

```
nano ~/.ssh/config 
```

在 YAML 语法中，为您的键添加以下条目。

```
Host bitbucket.org
    HostName bitbucket.org
    IdentityFile ~/.ssh/id_rsa
    IdentitiesOnly yes

Host companyName.bitbucket.org
    HostName bitbucket.org
    IdentityFile ~/.ssh/companyName
    IdentitiesOnly yes 
```

用名称`config`保存配置文件。

## 配置远程回购

为您想要使用的帐户拷贝您的 SSH 密钥。

```
pbcopy < ~/.ssh/id_rsa.pub 
```

```
pbcopy < ~/.ssh/companyName.pub 
```

在 Bitbucket 中，登录到您想要添加复制密钥的帐户，导航到`Bitbucket Settings - Security - SSH Keys`并添加密钥。对您为其创建密钥的所有帐户重复上述步骤。在 Github 中的
导航到`Settings - SSH and GPG keys`并在那里添加您的 Github 密钥。

## 从公司账户克隆

如果您从您的默认帐户克隆，只需在您的远程存储库中单击`Clone`即可复制输出。
如果您从您的公司帐户克隆，您需要用您在`config`中指定的主机更改主机。

```
git clone git@companyName.bitbucket.org:user/repo.git 
```