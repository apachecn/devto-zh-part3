# 模板设计模式[行为]

> 原文：<https://dev.to/itscoderslife/template-design-pattern-behavioral-9fk>

**上下文**:当您需要允许第三方提供或替换算法的某些步骤，而其他部分保持不变时，模板方法模式非常有用。

*   固定功能由基类型提供，可以被覆盖。
*   基类型可以提供所谓的挂钩方法。
*   钩子方法在基类型中可能有默认的或空的实现。
*   客户端可以扩展或覆盖钩子方法提供的行为。
*   钩子操作在特定的点被调用。

模板方法模式只允许在这些预定义的点上进行扩展，这样客户端就可以覆盖算法的某些步骤，而无需修改其原始结构。

> 模板方法允许客户端替换算法中的特定步骤，而无需修改其原始结构。

模板方法定义了算法的步骤，并允许修改一些必需的步骤。

例如:组织的一名员工。employee 类将是基本模板类。这个模板类将定义钩子，如**签到、休息、工作、签出**等。，以及它们的默认实现。然后还会有子类，比如培训生、软件开发人员、领导、架构师、经理。根据工作需要，每个子类都可以覆盖**工作**挂钩。

与策略不同，这种模式不是要替换整个算法，而是要改变算法的某些步骤。固定功能保留在基本类型中，不能被子类或相容类型修改。对于可能变化的步骤，有一些扩展点，也称为钩子操作。

如果你需要允许改变整个算法，不要使用这种模式。对于后者，请使用策略模式。