# 固有星系

> 原文：<https://dev.to/pratham/on-prem-ansible-galaxy-6b>

通用的 ansible 角色可以通过 [ansible galaxy](https://galaxy.ansible.com) 共享，但是，很难从企业网络内部访问 ansible-galaxy。一个好的替代方案是在本地托管类似 ansible-galaxy 的东西。

**本地星系库**

*   对于这个博客存储被认为([https://stash.company.com](https://stash.company.com))，然而，gitlab，gogs 或类似的应该也可以
*   用键`AG`在存储库`Ansible Galaxy`上创建一个新项目
*   为每个通用可移动角色创建 repo。
*   在[https://stash.company.com/projects/AG](https://stash.company.com/projects/AG)可以浏览所有通用的角色
*   现在，这些角色可以用于多个可翻译的行动手册。

**用途**

*   在 ansible `playbook.yml`旁边创建一个新文件`requirements.yml`。
*   出于这个博客的目的，我认为`awscli`是一个有自己的 repo 的普通的可负责的角色，驻留在我们新创建的 stash 项目`Ansible Galaxy (AG)`中
*   将以下内容复制/粘贴到`requirements.yml`

```
 ---
  name: awscli
  src: git+https://stash.company.com/scm/ag/awscli
  version: master
  path: ~/.ansible/roles

  # name - name of role
  # src - location of role
  # version - which branch the role should be installed from
  # path - where we want to install roles 
```

Enter fullscreen mode Exit fullscreen mode

*   `requirements.yml`文件充当我们的`playbook.yml`的角色依赖列表

**安装**

*   一旦我们定义了可行的剧本依赖项，就该安装它们了。

```
 sudo ansible-galaxy install -r requirements.yml
  # NOTE: no need to install ansible-galaxy seperately, it ships with default ansible installation 
```

Enter fullscreen mode Exit fullscreen mode

*   搞定了。所有的角色现在都已安装好，并准备好供我们的`playbook.yml`使用