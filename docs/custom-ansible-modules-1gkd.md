# 自定义可转换模块

> 原文：<https://dev.to/pratham/custom-ansible-modules-1gkd>

### 如何编写 ansible 自定义模块

Ansible 模块是在现有应用程序和 ansible 剧本之间进行交互的简单方法。这篇博客讲述了如何编写自己的 ansible 模块。

**模块**

*   创建一个名为`greet.py`的 python 文件，内容如下

```
 def main():
      module = AnsibleModule(
          argument_spec = dict(
              message = dict(required=True, type="str"),
          ),
          supports_check_mode=True
      )

      # get module params
      message = module.params.get("message")

      try:
          module.exit_json(changed=True, msg=message)
      except:
          module.fail_json(msg="It's bad not to greet someone")

  from ansible.module_utils.basic import AnsibleModule
  if __name__ == "__main__":
      main() 
```

Enter fullscreen mode Exit fullscreen mode

*   文件名`greet.py`被认为是可转换的模块。
*   ansible 剧本中定义的参数将由`AnsibleModule`类解析。
*   Ansible 模块总是返回 json，为了方便，ansible 提供了两种方法，一种表示成功，另一种表示失败

```
 # For success
  module.exit_json(changed=True, msg=message)

  # For failure
  module.fail_json(msg="It's bad not to greet someone") 
```

Enter fullscreen mode Exit fullscreen mode

**用途**

*   创建一个名为`playbook.yml`的可翻译剧本

```
 touch playbook.yml 
```

Enter fullscreen mode Exit fullscreen mode

*   在`playbook.yml`旁边创建目录`library`，并将`greet.py`复制到`library`

```
 mkdir library
  cp greet.py library 
```

Enter fullscreen mode Exit fullscreen mode

*   现在我们的目录结构应该看起来像这样

```
 library
      |- greet.py
  playbook.yml 
```

Enter fullscreen mode Exit fullscreen mode

*   我们现在可以将`greet`模块与我们的`playbook.yml`一起使用

```
 ---
  - hosts: localhost
    tasks:
    - name: Lets start greeting
      greet:
          message: "Good  morning" 
```

Enter fullscreen mode Exit fullscreen mode