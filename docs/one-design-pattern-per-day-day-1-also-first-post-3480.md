# 每天一个设计模式(第一天)；也是第一篇文章

> 原文：<https://dev.to/danielmwakanema/one-design-pattern-per-day-day-1-also-first-post-3480>

## 为的是什么？

嗯，设计模式并不是我的强项，自然地，我们必须做得更好。它不会尽善尽美，所以欢迎您的批评。

## 问题为天？

最近，我有机会观看另一个开发人员的工作。开发人员正在编写一个与特定 API 对话的应用程序。API 最近进行了修订，上层管理人员希望应用程序能够根据部署配置与两个版本进行对话。

## 开发者是如何解决问题的？

开发人员使用应用程序中每条路线下的`if`控制结构来执行三个版本 API 的适当代码。

## 感知问题与方法？

1.  非常长且臃肿的函数
2.  难以理解的代码
3.  每条路线下有太多的抽象层次

## 我的想法？

从我的角度来看，问题涉及到选择一个 API 版本，并从中创建适当的 API 实例。似乎它可以归入类实例化和对象创建。

## 我是如何做到的(至少是实例化)？

1.  创建一个定义 API 的接口
2.  让任何版本实现它
3.  创建工厂以创建给定版本的适当实例

所以，呃，代码看起来像下面这样(可能有错误，不要在意):

### 界面:

```
 public interface API
  {
    public void doSomething();
    public void doOneMoreThing();
  } 
```

### API 实例:

```
 public class ApiV1 implements API
  {
    public void doSomething()
    {
      // doing something
    }

    public void doOneMoreThing()
    {
      // doing one more thing
    }
  }

  public class ApiV2 implements API
  {
    public void doSomething()
    {
      // doing something but slightly differently
    }

    public void doOneMoreThing()
    {
      // doing one more thing but slightly differently
    }
  } 
```

### 工厂:

```
 public class APIFactory
  {
    private Map<String, API> apiMap =  Map.ofEntries(entry("v1", ApiV1),
                                                     entry("v2", ApiV2));

    public API getApiIstance (string version)
    {
      if (!this.apiMap.containsKey(version)) {
        throw new IllegalArgumentException("Passed API version does not exist.");
      }
      return this.apiMap.get(version)();
    }
  } 
```

### 用法:

```
 // assuming there is a config somewhere
  API apiv1 = APIFactory().getApiInstance(config.apiVersion); 
```

### 结论:

所以，嗯，是的。虽然不完美，但也许是朝着正确方向迈出的一步。