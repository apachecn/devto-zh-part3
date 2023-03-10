# 如何对定制的 Ansible 模块运行简单的测试

> 原文：<https://dev.to/drewmullen/how-to-run-simple-tests-against-custom-ansible-modules-4875>

假设您正在编写一个模块来确保 cat.jpg 文件的存在，但是这个现成的模块并没有为您切割它！通常，你会拿出文档，开始钻研你的新的、特殊的模块！每当你达到一个重要的功能里程碑时，你会用新模块写一个小剧本，转到你的 CLI 并运行`ansible-playbook cat-module-test.yml`

```
$cat cat-module-test.yml

--------
- hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - cat_module:
        state: present
        src: cat.jpg
        dest: /tmp 
```

Enter fullscreen mode Exit fullscreen mode

虽然这确实可行，但有几个问题:

1.  您只是在测试模块是否工作；在执行过程中，您看不到任何调试输出
2.  在执行过程中无法监视变量
3.  无法设置断点
4.  输出是可解析的，测试时不容易阅读

相反，我将向您展示一种通过直接传递参数来轻松测试这段代码的新方法。首先我们需要创建一个 args 文件:

```
$ cat args.json

{ "ANSIBLE_MODULE_ARGS": {
    "state": "present",
    "src": "cat.jpg",
    "dest": "/tmp"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 通过 CLI 进行测试

使用这个`args.json`文件，你不用调用`ansible`就可以轻松测试！

`python ./cat_module.py args.json`

## 通过 VSCode 测试

或者，您可以使用 IDE，下面是如何在 VSCode 中使用的示例

1.  打开文件夹后，点击左侧窗格中的`debug`菜单选项
2.  创建一个新的调试配置，并调整`launch.json`中的适当部分

```
{
    "name": "Python: Terminal (integrated)",
    "type": "python",
    "request": "launch",
    "program": "/home/rmullen/git/library/cat_module/cat_module.py",
    "console": "integratedTerminal",
    "args":["/home/rmullen/git/library/cat_module/args.json"]
}, 
```

Enter fullscreen mode Exit fullscreen mode

**按下播放！**(字面意思，调试菜单中的播放按钮)

使用这种方法，您现在可以设置断点，查看 print()语句，并以编写 python 时所希望的方式单步执行代码！