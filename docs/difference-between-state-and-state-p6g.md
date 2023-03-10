# 州与州的区别

> 原文：<https://dev.to/caiorcferreira/difference-between-state-and-state-p6g>

*照片由[安妮·斯普拉特](https://unsplash.com/photos/hzdgFPz1V24?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/machine?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

今天，我们将试图解决国家概念的模糊性，强调关于国家的两个主要概念的差异。这篇文章描述了每一个的本质，一些用例，以及它们如何适应对象和函数范例。

# 简介

在过去的几个月里，我深入到了状态机的主题，以及我们如何用这个概念来设计 UI，以便为我们的应用程序提供更好的语义和可预测性。当阅读和谈论它的时候，我经常不得不停下来，澄清我所指的关于状态的两个观点中的哪一个:

*   在某个时间点收集数据的想法
*   将实体表示为状态机的概念。

为了便于理解，我们将用**状态**指代第一个，**状态**指代最后一个。

# 要求

我们将使用 Typescript 作为我们的 yummy 示例，所以对它有所了解会更好。

# 作为旅行包的状态

当我们学习软件开发中的状态时，我们习惯的第一个概念是实体“旅行包”。基本上，我们将状态视为特定时间点的数据集合。在整个应用程序生命周期中，这些数据被操纵和更改，以反映业务流程。例如:

```
 class Pizza {
        private dough: Dough; // it's an enum that could be traditional or thin
        private ingredients: Array<Ingredient>;

        // entity controls
        private isBeingnPrepared: boolean;
        private isBaking: boolean;
        private baked: boolean;

        constructor() {
            this.isBeingnPrepared = true;
            this.isBaking = false;
            this.baked = false;
        }

        // getters and setters

        async public bakePizza(): void {
            const oven = new OvenService();

            try {
                this.isBeingnPrepared = false;
                this.isBaking = true;

                await oven.bake(this);

                this.baked = true;
            } catch (error) {
                throw error;
            }
        }
    } 
```

从这一点开始，pizza 状态以及应用程序状态都发生了变化，因为它的数据被更新了。但是，两个布尔可以有四种不同的排列方式，其中一些是无效状态。在面向对象的范例中，我们将通过把这样的数据封装在一个对象中，并且只通过保证原子的和一致的变化的方法来建模它的操作，来避免这种情况。

直到这个用例，我们的模型似乎是好的。但是，是时候实施比萨饼店流程的下一步了，即交付。

```
 class Pizza {
        private dough: Dough;
        private ingredients: Array<Ingredient>;

        // entity controls
        private isBeingnPrepared: boolean;
        private isBaking: boolean;
        private baked: boolean;
        private isBeingDelivered: boolean;
        private hasBeenDelivered: boolean;

        constructor() {
            this.isBeingnPrepared = true;
            this.isBaking = false;
            this.baked = false;
            this.isBeingDelivered = false;
            this.hasBeenDelivered = false;
        }

            // getters and setters

            // bake behavior

        async public deliveryPizza() {
            if (!this.baked) {
                throw new PizzaNotBakedException();
            }

            const deliveryService = new DevelieryService();

            try {
                this.isBeingnPrepared = false;
                this.isBeingDelivered = true;

                await deliveryService.send(this);
            } catch (error) {
                throw error;
            }
        }

        public notifyDelivery(wasSuccessful) {
            if(wasSuccessful) {
                this.hasBeenDelivered = true;
            }
        }
    } 
```

在这段代码中引发一个标志的是在 delivery 函数开始时使用了一个 guard 条件，该条件检查比萨饼是否已经烤好。如果没有，它抛出一个异常。这看起来真的很简单，如果这是唯一的条件，那就好了。但是，披萨可能已经被送出去了，因此，我们不想再送了。因此，我们在函数中添加了另一个保护条件:

```
 class Pizza {
        private dough: Dough;
        private ingredients: Array<Ingredient>;

        // entity controls
        private isBeingPrepared: boolean;
        private isBaking: boolean;
        private baked: boolean;
        private isBeingDelivered: boolean;
        private hasBeenDelivered: boolean;

            // constructor

            // getters and setters

            // bake behavior

        async public deliveryPizza() {
            if (!this.baked) {
                throw new PizzaNotBakedException();
            }

            if (this.isBeingDelivered) {
                throw new PizzaAlreadyLeftException();
            }

            const deliveryService = new DevelieryService();

            try {
                this.isBeingPrepared = false;
                this.isBeingDelivered = true;

                await deliveryService.send(this, this.notifyDelivery);
            } catch (error) {
                throw error;
            }
        }

            // notify delivery behavior
    } 
```

如果我们详细描述披萨的所有场景，这种由`if/else`语句表达的包含大量分支和条件的实现会呈指数级增长。它增加了我们代码的圈复杂度，降低了可维护性，因为这样的代码更脆弱，更难阅读和理解。

当这种条件开始在代码中传播时，情况会变得更糟，就像在 bake 函数中一样，为了不尝试再次烘焙，需要更新 bake 函数。

```
 class Pizza {
        private dough: Dough;
        private ingredients: Array<Ingredient>;

        // entity controls
        private isBeingPrepared: boolean;
        private isBaking: boolean;
        private baked: boolean;
        private isBeingDelivered: boolean;
        private hasBeenDelivered: boolean;

        // constructor

            // getters and setters

        async public bakePizza(): void {
            if (this.baked) {
                throw new PizzaAlreadyBakedException();
            }

            const oven = new OvenService();

            try {
                this.isBeingPrepared = false;
                this.isBaking = true;

                await oven.bake(this);

                this.baked = true;
            } catch (error) {
                throw error;
            }
        }

        // delivery behavior

            // notify delivery behavior
    } 
```

尽管这种设计服务于多种提议，特别是在更简单或以数据为中心的场景中，但在快速发展和以流程为中心的领域中，它通过不同的函数在一堆代码执行路径和不同步的条件上发展。

状态作为一个实体旅行包有一个用途，它将相关信息携带到模型中。试图通过相同的概念来控制这个实体的行为，最终会使它承担过多的责任，并为我们的设计制造一个无声的陷阱。

这里面临的问题是，应用程序架构通过无效的**状态**允许无效的行为，当它最终出现时，一些用例将暴露这种自由所产生的错误。除此之外，这种方法采用系统不变量，在这种情况下，比萨饼烹饪流程，然后分散在许多实现点中，而不是在设计中强制它们。

附注:如果你精通代数数据类型，你可以把它看作是一种基数趋于无穷大的产品类型。

# 具象状态

一旦我们遇到了控制实体信息和行为的问题，而这些信息和行为是由同一个构造，即**状态**完成的，我们的反应就再简单不过了:让我们打破这些责任。

因此，我们需要一种新的模式来处理我们实体的行为。

但是，我们在设计应用程序时提出的替代模式一点也不新。它是状态模式，在许多关于 OO 的古书中都有描述。这本书同样会告诉你，状态模式试图将一个实体行为委托给一个特定的实现，该实现是当前状态，在方法结束时计算实体的下一个状态，该状态现在将代表实体，动态替换其行为实现。毕竟，这种模式是从状态机到名词的*习语的翻译。*披萨示例的另一个实现如下:

```
 interface IPizza {
        bakePizza();
        deliveryPizza();
        notifyDelivery(wasSuccessful: boolean);
    }

    type Pizza = PreparingPizza | BakedPizza | DeliveringPizza | DeliveredPizza;

    class PreparingPizza implements IPizza {
        private dough: Dough; // it is an enum that could be traditional or thin
        private ingredients: Array<Ingredient>;

        constructor(dough: Dough, ingredients: Array<Ingredient>) {
            this.dough = dough;
            this.ingredients = ingredients;
        }

        // setters

        getDough() {
            return this.dough;
        }

        getIngredients() {
            return this.ingredients;
        }

        public async bakePizza(): Promise<BakedPizza> {
            const oven = new OvenService();

            try {
                await oven.bake(this);

                return new BakedPizza(this);
            } catch (error) {
                throw error;
            }
        }

        public async deliveryPizza() {
            throw new PizzaNotReadyForDelivery();
        }

        public notifyDelivery(wasSuccessful) {
            throw new PizzaNotReadyForDelivery();
        }
    }

    class BakedPizza implements IPizza {
        private dough: Dough;
        private ingredients: Array<Ingredient>;

        // constructor
        constructor(pizza: PreparingPizza) {
            this.dough = pizza.getDough();
            this.ingredients = pizza.getIngredients();
        }

        // getters and setters

        public async bakePizza(): Promise<BakedPizza> {
            throw new PizzaAlreadyBakedException();
        }

        public async deliveryPizza(): Promise<DeliveringPizza> {
            const deliveryService = new DevelieryService();

            try {
                await deliveryService.send(this);

                return new DeliveringPizza(this);
            } catch (error) {
                throw error;
            }
        }

        public notifyDelivery(wasSuccessful) {
            throw new PizzaNotLeftForDeliveryYey();
        }
    }

    class DeliveringPizza implements IPizza {
        private dough: Dough;
        private ingredients: Array<Ingredient>;

        // constructor

        // getters and setters

        public async bakePizza(): Promise<BakedPizza> {
            throw new PizzaAlreadyBakedException();
        }

        public async deliveryPizza(): Promise<DeliveringPizza> {
            throw new PizzaAlreadyLeftForDeliveryException();
        }

        public notifyDelivery(wasSuccessful) {
            if(wasSuccessful) {
                return new DeliveredPizza(this);
            }
        }
    }

    class DeliveredPizza implements IPizza {
        private dough: Dough;
        private ingredients: Array<Ingredient>;

        // constructor

        // getters and setters

        public async bakePizza(): Promise<BakedPizza> {
            throw new PizzaAlreadyBakedException();
        }

        public async deliveryPizza(): Promise<DeliveringPizza> {
            throw new PizzaAlreadyLeftForDeliveryException();
        }

        public notifyDelivery(wasSuccessful) {
            throw new PizzaAlreadyDeliveredException();
        }
    } 
```

在这个实现中，我们通过接口和 Pizza union 类型来加强我们的类型系统的域不变量。有了它，我们获得了更少的圈复杂度，因为我们的代码中没有那么多的分支，而且，通过设计，我们不允许无效状态的发生。除此之外，每个状态都携带一个内部数据，它的*旅行包*。因此，这些模式并不排斥，而是可以组合的。

在前端的趋势中，我们通常看到的是状态机的功能范式方法。表示为状态机的实体现在只是每个状态的不同数据结构，可以由实现域行为的纯函数来解释。这些函数可以在内部将其调用委托给每个状态中的其他专用函数。行为的状态机实现的这种分离是自然的，因为它遵循功能架构的习语。

在这两种情况下，剩下的就是国家作为一个**实体的代表**的性质。它代表它工作，并限定它可能暴露的行为。

例如，一个比萨饼不可能同时处于烘烤和交付状态。现在，它不是一个保证它就是设计本身的实现。这种对领域建模的抽象，我们产品的核心，不能依赖于有效的实现细节，它们必须依赖于抽象本身。

*附注:如果你精通代数数据类型，你可以把它看作是一个基数不到 12 的联合类型。*

# 进化出抽象

人们可以通过使用简单的 enum、适当的状态机实现或更高级的概念，即状态图，来实现面向状态的设计。

的确，许多领域可以使用前两种方法来对状态进行编码，但是有时我们会面临一个高度复杂的场景，其中这种抽象实现不能随着应用程序的开发而扩展。

为此，David Harel 在 1987 年提出了一种新技术，扩展了状态机定义的基础，引入了状态层次、并行、聚类、历史等工具。他称之为状态图，它是一种形式主义，帮助我们扩展状态设计的发展，彻底地实现它，或者只是采用一些工具。

我强烈推荐多读一些关于状态图的书，因为它可以改变你处理问题的心态。

# 总结

现在，我们可以区分**状态**和**状态**，并通过使用正确的构造来建模我们的领域，从而避免意外的复杂性。如果我不说没有什么灵丹妙药，这些都是提供工作的工具，那就一文不值。我们已经在我的团队中试验了这种设计风格，它很有帮助，因为我们的场景非常复杂而且节奏很快。

如果你有任何问题或者想更深入地讨论这些和其他话题，请发表评论或者你可以通过 Twitter 联系我， [@caiorcferreira](https://dev.to/caiorcferreira) 。

感谢阅读！

# 参考文献

*   [状态设计模式](https://en.wikipedia.org/wiki/State_pattern)
*   [用户界面的状态驱动开发](https://dev.to/nimmo/state-driven-development-for-user-interfaces-part-1-an-introduction-27f1)
*   JS 上的状态图实现
*   状态图:复杂系统的可视化形式主义
*   [纯 UI 控件](https://medium.com/@asolove/pure-ui-control-ac8d1be97a8d)
*   [用状态图构建用户界面](https://www.amazon.com/Constructing-User-Interface-Statecharts-Horrocks/dp/0201342782%5D(https://www.amazon.com/Constructing-User-Interface-Statecharts-Horrocks/dp/0201342782))