# 如何在使用 TypeScript+mongose 的同时减少样板代码的数量🙈

> 原文：<https://dev.to/nuclight/how-can-i-reduce-amount-of-boilerplate-code-while-working-with-typescriptmongoose--26do>

假设我有两个实体:*车辆*和*关税*。它们是一对多的关系(车辆可以有多个关税)。

这是我对对象的基本类型定义，我想使用:

```
interface Tariff {
    id: string;         // <-- ID of entity
    createdAt: Date;    // <-- some meta data fields for sorting and etc.

    days: number;       // <-- userful info
    price: number;      // <-
}

interface Vehicle {
    id: string;          // <-- ID of entity
    createdAt: Date;     // <-- some meta data fields for sorting and etc.

    model: string;       // <-- userful info
    year?: string;       // <-
    seatsCount?: number; // <-
    tariffs: Tariff[];   // <-- array of tariffs
} 
```

看起来很棒。不，我们需要创建数据库模型。首先让我们定义 mongoose 模式:

```
import mongoose from "mongoose";

const vehicleSchema = new mongoose.Schema({
    createdAt: {type: Date, required: true, default: () => new Date()},

    model: {type: String, required: true},
    year: String,
    seatsCount: Number,
}); 
```

太棒了。我决定不把关税放在车辆文件中，它们将被分开存放。

接下来，我们需要创建猫鼬模型。但是我们需要提供扩展`mongoose.Document`到`mongoose.model`的泛型类型。这就是代码复制开始的地方:

```
// First we need to define TypeScript schema equalent of mongoose schema:
interface VehicleSchema {
    createdAt: Date;

    model: string;
    year?: string;
    seatsCount?: number;
}

// Then we need to defined a new type of out vehicle models:
type VehicleModel = VehicleSchema && mongoose.Document;

// And finaly we can create model:
const VehicleModel = mongoose.model<VehicleModel>("Car", carSchema);

// Look, here is a type named "VehicleModel" and a constant named "VehicleModel" 
```

现在我们可以为 CRUD 操作编写函数:

```
namespace vehicle {
    export const create = async (form: VehicleCreateForm): Promise<Vehicle> => {
        const fields = toDb(form);

        // assume form is prevalidated
        let dbVehicle = new VehicleModel(form);
        dbVehicle = await model.save();

        // tariff - is the separate module, like this one, 
        // createAllFor methods retuns Tariff[]
        const tariffs = await tariff.createAllFor(dbVehicle.id, form.tariffs);

        return fromDb(dbVehicle, tariffs);
    }

    //...

    // export const update = ...
    // export const list = ...
    // export const get = ... 
    // etc..
} 
```

这里我们引入一个额外的类型:`VehicleCreateForm`，它描述了创建车辆所需的所有字段:

```
interface VehicleCreateForm {
    model: string;
    year?: string;
    seatsCount?: number;
    tariffs: TariffCreateFields[]; // <-- here is another one special type
}

interface TariffCreateFields {
    days: number;
    price: number;
} 
```

我们还需要定义两个函数:`toDb`来防止一些字段，例如`tariffs`被传递给模型。并且`fromDb`将模型“翻译”成`Vehicle`实体，删除或转换一些字段:

```
const u = <T>(value: T | undefined | null): (T | undefined) => value === null ? undefined : value;

const toDb = (f: VehicleCreateForm): VehicleCreateFields => ({
    model: f.model,
    year: f.year,
    seatsCount: f.seatsCount,
});

const fromDb = (m: VehicleModel, tariffs: Tariff[]): Vehicle => ({
    id: m.id,
    createdAt: m.createdAt,

    model: m.model,
    year: u(m.year),
    tariffs,
}); 
```

而且，yaaa，我们还需要多一种类型:`VehicleCreateFields`。我们要传递给模型构造函数的字段。

```
interface VehicleCreateFields {
    model: string;
    year?: string;
    seatsCount?: number;
} 
```

似乎这里已经结束了。

我们还需要定义类似于`vehicle`的`tariff`名称空间。所有类型都将被复制:`TariffSchema`、`TariffModel`、`TariffCreateForm`、`TariffCreateDocument`。

每一个新的实体都会这样。在当前情况下，我可以避免创建`VehicleUpdateFields`类型，而使用`VehicleCreateFields`来创建和更新。也可能有`VehicleUpdateDocument`等等。

我如何减少代码重复的数量？你如何处理打字稿/猫鼬？

当然，我可以提取公共字段到“公共 chunck”接口，但是我不知道如何命名它们。我试图使用接口继承，结果却是一堆乱七八糟的小字体。喜欢:

```
interface Entity {
    id: string;
    createdAt: Date;
}

interface VehicleOwnFields {
    model: string;
    year?: string;
    seatsCount?: number;
}

interface VehicleOwnFields {
    model: string;
    year?: string;
    seatsCount?: number;
}

interface VehicleExternalFields {
    tariffs: Tariff[];
}

type Vehicle = Entity && VehicleOwnFields && VehicleExternalFields;
// and so on... 
```

我认为这不是最好的解决方案。

也可能存在实体和模式中的字段具有不同类型的情况。也就是说，我可以决定将`Tariff[]`序列化为 JSON，并作为字符串字段存储在`VehicleModel`中。

我认为这是打字稿和 ORM 的普遍问题。如果你知道描述这个问题的好文章，请给我链接。