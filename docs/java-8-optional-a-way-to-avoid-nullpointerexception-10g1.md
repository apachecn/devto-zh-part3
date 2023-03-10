# Java 8 可选:避免 NullPointerException 的方法

> 原文：<https://dev.to/arpitmandliya/java-8-optional-a-way-to-avoid-nullpointerexception-10g1>

在本帖中，我们将看到关于 Java 8 可选的内容。

作为 Java 开发人员，你有没有遇到过[**NullPointerException**](https://java2blog.com/java-lang-nullpointerexception/)的情况？如果您是经验丰富的 Java 开发人员，您可能在某个时候遇到过 NullPointerException。
你可能同意 NullPointerException 对于新手或专业的核心 java 开发人员来说是痛苦的。如果你想避免 NullPointerException，你必须编写大量的防御性代码。
让我们借助一个例子来看看。

```
package org.arpit.java2blog;

public class Employee {
    private String name;
    private int age;

    public Employee(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
}

```

创建一个名为“JavaFindEmployeeMain.java”的主类

```
package org.arpit.java2blog;

import java.util.ArrayList;
import java.util.List;

public class JavaFindEmployeeMain {

    public static void main(String[] args)
    {
        List<Employee> employeeList = createEmployeeList();
        Employee employee = findEmployee(employeeList,"Adam");
        System.out.println("Employee name: "+employee.getName());
    }

    public static Employee findEmployee(List<Employee> employeeList,String name)
    {
        for(Employee e:employeeList)
        {
            if(e.getName().equalsIgnoreCase(name))
            {
                return e;
            }
        }
        return null;
    }
    public static List<Employee> createEmployeeList()
    {
        List<Employee> employeeList=new ArrayList<>();

        Employee e1=new Employee("John",21);
        Employee e2=new Employee("Martin",22);
        Employee e3=new Employee("Mary",31);
        Employee e4=new Employee("Stephan",18);
        Employee e5=new Employee("Gary",26);

        employeeList.add(e1);
        employeeList.add(e2);
        employeeList.add(e3);
        employeeList.add(e4);
        employeeList.add(e5);

        return employeeList;

    }
}

```

当您运行上面的程序时，您将得到下面的输出:

org . arpit . Java 2 blog . javaoptionalmain . main 处的线程“main”Java . lang . nullpointerexception
出现异常(JavaOptionalMain.java:12)

如您所见，“Adam”在 employeeList 中不存在，这就是我们在这里得到 NullPointerException 的原因。

你看到这里的问题了吗？当我们试图在列表中查找雇员时，我们忘记了检查 null。当你调用库函数并且它返回 null 而你忘记检查它的时候，这种情况会更常见。

* * *

## Java 8 可选

你可以使用 Optional 来解决这个问题。您可以如下更改“JavaOptionalMain”。

```
package org.arpit.java2blog;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class JavaOptionalMain {

    public static void main(String[] args)
    {
        List<Employee> employeeList = createEmployeeList();
        Optional<Employee> employeeOpt = findEmployee(employeeList,"Adam");
        if(employeeOpt.isPresent())
        {
            Employee employee = employeeOpt.get();
            System.out.println("Employee name: "+employee.getName());
        }
        else
        {
            System.out.println("There is no employee with name Adam");
        }
    }

    public static Optional<Employee> findEmployee(List<Employee> employeeList,String name)
    {
        for(Employee e:employeeList)
        {
            if(e.getName().equalsIgnoreCase(name))
            {
                return Optional.of(e);
            }
        }
        return Optional.empty();
    }
    public static List<Employee> createEmployeeList()
    {
        List<Employee> employeeList=new ArrayList<>();

        Employee e1=new Employee("John",21);
        Employee e2=new Employee("Martin",22);
        Employee e3=new Employee("Mary",31);
        Employee e4=new Employee("Stephan",18);
        Employee e5=new Employee("Gary",26);

        employeeList.add(e1);
        employeeList.add(e2);
        employeeList.add(e3);
        employeeList.add(e4);
        employeeList.add(e5);

        return employeeList;
    }
}

```

当您运行上面的程序时，您将得到下面的输出:

没有叫亚当的雇员

您可能认为您也可以在 JavaFindEmployeeMain 中处理 null，但是当您从方法中返回 Optional 时，这意味着方法中可能会缺少值。
[![Optional Java 8](img/7ddb052e61a122f3364e859f0a979343.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--RuRwJGBF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://java2blog.com/wp-content/uploads/2018/05/Optional.jpg)

* * *

## 创建可选方式

有多种方法可以创建可选的。

### 空可选

您可以使用静态工厂方法“empty”创建空的可选对象

```
Optional<Employee> optCar = Optional.empty();

```

### 非空值中的可选值

您可以使用静态工厂方法“of”从非空值创建可选值

```
Optional<Employee> optCar = Optional.of(employee);

```

如果 employee 为 null，那么上述方法将抛出 NullPointerException。

### 可选空值或非空值

您可以使用静态工厂方法“ofNullable”从空值或非空值创建可选值

```
Optional<Employee> optCar = Optional.ofNullable(employee);

```

* * *

## 从可选的中获取值

您可以使用 get()方法从 Optional 中检索值，但这是最不安全的。如果值不存在，那么它将抛出 NoSuchElementException，所以您需要确保在调用 get()方法之前调用 isPresent()方法。

* * *

## 检查可选中的值

您可以使用 isPresent 方法检查 Optional 内部是否包装了值。

```
    public static void main(String[] args)
    {
            List<Employee> employeeList = createEmployeeList();
        Optional<Employee> employeeOpt = findEmployee(employeeList,"Adam");
        if(employeeOpt.isPresent())
        {
            Employee employee = employeeOpt.get();
            System.out.println("Employee name: "+employee.getName());
        }
        else
        {
            System.out.println("There is no employee with name Adam");
        }
    }

```

* * *

## 可选中的条件动作

如果可选中存在值，则可以使用 ifPresent 方法来执行操作。
将 JavaOptionalMain 中的 main 方法更改如下

```

    public static void main(String[] args)
    {
        List<Employee> employeeList = createEmployeeList();
        Optional<Employee> employeeOpt1 = findEmployee(employeeList,"Adam");
        Optional<Employee> employeeOpt2 = findEmployee(employeeList,"John");

        employeeOpt1.ifPresent((employee)->System.out.println("Employee name: "+employee.getName()+" found in list"));
        employeeOpt2.ifPresent((employee)->System.out.println("Employee name: "+employee.getName()+" found in list"));

    }

```

当您运行这个程序时，您将得到下面的输出:

员工姓名:Dummy
员工姓名:John 在列表中找到

正如您在这里看到的，如果员工姓名出现在列表中，那么只有我们在打印员工姓名，否则它不会执行任何操作。

* * *

## 默认值在可选使用 orElse

如果 Optional 中没有值，可以使用 orElse 方法返回默认值。
将 JavaOptionalMain 中的 main 方法更改如下

```

    public static void main(String[] args)
    {
        List<Employee> employeeList = createEmployeeList();
        Optional<Employee> employeeOpt = findEmployee(employeeList,"Adam");
        Employee employee1 = employeeOpt.orElse(new Employee("Dummy",0));
        System.out.println("Employee name: "+employee1.getName());

        Optional<Employee> employeeOpt2 = findEmployee(employeeList,"Martin");
        Employee employee2= employeeOpt2.orElse(new Employee("Dummy",0));
        System.out.println("Employee name: "+employee2.getName());

    }

```

当您运行这个程序时，您将得到下面的输出:

雇员姓名:假人
雇员姓名:马丁

请注意，即使值出现在 Optional 中，也将创建默认对象。

* * *

## 默认值在可选使用 orElseGet

orElseGet 是 orElse 的惰性计数器部分。它以[供应商](https://java2blog.com/java-8-supplier-example/)为参数，只有在可选中没有值时才会被调用。
将 JavaOptionalMain 中的 main 方法更改如下

```

    public static void main(String[] args)
    {
        List<Employee> employeeList = createEmployeeList();
        Optional<Employee> employeeOpt = findEmployee(employeeList,"Adam");
        Employee employee1 = employeeOpt.orElseGet(()->new Employee("Dummy",0));
        System.out.println("Employee name: "+employee1.getName());

        Optional<Employee> employeeOpt2 = findEmployee(employeeList,"Martin");
        Employee employee2 = employeeOpt2.orElseGet(()->new Employee("Dummy",0));
        System.out.println("Employee name: "+employee2.getName());

    }

```

当您运行这个程序时，您将得到下面的输出:

雇员姓名:假人
雇员姓名:马丁

* * *

## 从可选的抛出异常

如果 Optional 为空，可以使用 orElseThrow 抛出异常。它类似于 get()方法，但是在这种情况下，您可以选择抛出任何异常，而不是 NoSuchMethodException。
将 JavaOptionalMain 中的 main 方法更改如下

```
    public static void main(String[] args)
    {
        List<Employee> employeeList = createEmployeeList();
        Optional<Employee> employeeOpt = findEmployee(employeeList,"Adam");
        Employee employee1 = employeeOpt.orElseThrow(() -> new RuntimeException("Employee not found"));
        System.out.println("Employee name: "+employee1.getName());

        Optional<Employee> employeeOpt2 = findEmployee(employeeList,"Martin");
        Employee employee2 = employeeOpt2.orElseThrow(() -> new RuntimeException("Employee not found"));
        System.out.println("Employee name: "+employee2.getName());

    }

```

## 在 Java 8 APIs 中使用可选的

Java 8 APIs 中有很多可选的用法。让我给你举一个例子。
Stream.findFirst()方法返回该流的第一个元素的可选值，如果该流为空，则返回空可选值。

这就是 Java 8 中的可选性。

快乐学习！！