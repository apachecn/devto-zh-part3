# 图寻找构建系统的新方法

> 原文：<https://dev.to/aexol/graphsourcing-new-way-to-build-systems-45a8>

## 简介

你听说过 graphsourcing 吗？你当然有。UML 系统是一个系统，在这个系统中，你可以用图形来定义你系统。然后您编写代码，这些代码必须满足您的 UML 图的要求。然后前端开发人员还必须编写符合 UML 图和后端的前端。图形设计师也应该了解一些 UML 技术。

这对我来说太多了。

## Graphsourcing 好办法

几个月前，我们创建了自己的 graphsourcing 工具，名为[slotthking](https://slothking.online)。学习使用它可能不是一件容易的事情，但我们从 graphsourcing 中获得的好处是巨大的。首先你需要创建一个定义你的系统的图表。然后生成前端、后端、app 等等类型定义。所以每个人都知道发生了什么

## 一个国家统治他们所有人

在 graphsourcing 的所有好处中，我最喜欢的是一个州。可以在后端和前端创建相同类型的状态。借助出色的 typescript 自动完成功能，您可以获得超快的工作流，前端和后端开发人员不必相互交流。

## 概念证明

[![](img/757083cfdc9506d72313b0496861fd5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P86CfheG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/graphsourced.png)

作为概念验证，我们在 [Aexol](https://aexol.com) 重写了我们的食品配送服务系统 [Foodeli](https://foodeli.pl) 。重写后端的第一阶段我们在 8 小时内完成(3 人团队),我们把所有东西都放在一个地方。

这是一个完美的例子，因为 Foodeli 生态系统包括:

*   管理员面板，Web
*   餐厅个人面板，网络
*   驱动程序应用，iOS，Android
*   客户端应用程序，Web

他们都必须使用相同的后端系统。

## 代码

那么 graphsourcing 在实践中看起来如何呢？

### 后端

生成命令:

```
sloth sync nodets foodeli api.ts 
```

Enter fullscreen mode Exit fullscreen mode

生成代码的一小部分

```
export type DriverType = {
  _id?: ObjectId
  name: string
  phone?: string
  Address?: ObjectId | AddressType
  pushToken?: string
  snsEndpoint?: string
  snsSubscriptiom?: string
  foodeliDriver?: boolean
  User: ObjectId | UserType
  PESEL?: string
  deliveryRate?: number
}
export type GPSType = {
  _id?: ObjectId
  geoLocation?: ObjectId | geoLocationType
  Driver?: ObjectId | DriverType
  created?: Date
} 
```

Enter fullscreen mode Exit fullscreen mode

还提供了端点和中间件的类型

### 前端

生成 api 和状态

```
sloth sync fetch-api foodeli api.ts 
```

Enter fullscreen mode Exit fullscreen mode

前端状态的一部分

```
const endpoints: {
    name: string;
    endpoints: {
      addOrder: (params:{props:         {
          comments?: string;
          paymentForm: string;
          totalPrice: number;
          readyin: number;
          phone: string;
          Address: Partial<AddressType> | string;
          token: string;
        },method?: 'GET'|'POST'|'PATCH'|'DELETE'|'PUT'}) => Promise<        {
          Order: OrderType;
        }>;
      myOrders: (params:{props:         {
          period?: string;
          token: string;
        },method?: 'GET'|'POST'|'PATCH'|'DELETE'|'PUT'}) => Promise<        {
          Orders: OrderType[];
        }>;
      restaurantOrderDetails: (params:{props:         {
          orderId?: string;
          token: string;
        },method?: 'GET'|'POST'|'PATCH'|'DELETE'|'PUT'}) => Promise<        {
          Order: OrderType;
          orderSteps: orderStepType[];
        }>;
      updateOrderStatus: (params:{props:         {
          status?: OrderStatus;
          orderId?: string;
          token: string;
        },method?: 'GET'|'POST'|'PATCH'|'DELETE'|'PUT'}) => Promise<        {
          Order: OrderType;
        }>;
    }
} = foodeli: {
    name: 'foodeli',
    endpoints: {
      addOrder: ({ props, method = 'POST' }) =>
        fn(`${host}foodeli/addOrder`, {
          props,
          method,
        }).then(res => res.json()),
      myOrders: ({ props, method = 'POST' }) =>
        fn(`${host}foodeli/myOrders`, {
          props,
          method,
        }).then(res => res.json()),
      restaurantOrderDetails: ({ props, method = 'POST' }) =>
        fn(`${host}foodeli/restaurantOrderDetails`, {
          props,
          method,
        }).then(res => res.json()),
      updateOrderStatus: ({ props, method = 'POST' }) =>
        fn(`${host}foodeli/updateOrderStatus`, {
          props,
          method,
        }),
    },
  },
}
//Small part of the state
export type foodeliExtensionState = {
  Order: OrderType;
  Orders: OrderType[];
  orderSteps: orderStepType[];
  Restaurant: RestaurantType;
  restaurantDeliveryZone: restaurantDeliveryZoneType[];
  geoParams: geoParams;
  deliveryCost: deliveryCost;
  dishCategoryList: dishCategoryType[];
  DishList: DishType[];
  dishVariantList: dishVariantType[];
  dishAdditionList: dishAdditionType[];
  Drivers: DriverType[];
  Address: AddressType;
  deliveryZones: deliveryZoneType[];
  Settlements: OrderType[];
}; 
```

Enter fullscreen mode Exit fullscreen mode

因此，您只需在前端使用某些参数调用生成的端点，并使用  将输入的响应自动注入到状态中。如果每个人都使用它，这将是对后端驱动开发的巨大推动

### Corona SDK 应用

是的，我们正在用 corona 编写应用程序，所以我们决定也应该为 Corona SDK 提供 Api 生成器。这是其中的一小部分。

```
local json = require 'json'
local library = {}

function library:init(host,errHandler)
    self.errorHandler = errHandler
    self.host = host
    self.requestTab = {}
end

function library:removeRequest(id)
  for i=1,#self.requestTab do
    if self.requestTab[i] == id then
      table.remove(self.requestTab,i)
    end
  end
end

function library:addRequest(id)
  table.insert(self.requestTab,id)
end

function library:cancelRequest(id)
  for i=1,#self.requestTab do
    if self.requestTab[i] == id then
      network.cancel( self.requestTab[i] )
      table.remove(self.requestTab,i)
    end
  end
end

function library:cancelAllRequests()
  for i=1,#self.requestTab do
    network.cancel( self.requestTab[i] )
  end
  self.requestTab = {}
end
function library:aws_sns_registerDevice(callback,token,platform,applicationArn, method)
  local requestId
  if method == nil then
    method = "POST"
  end
  local function networkListener( event )
    self:removeRequest(requestId)
      if event.isError then
            if self.errorHandler then
                self.errorHandler(event)
            else
                callback(event)
            end
      else
      if callback then
        callback(event)
      end
    end
  end
  local headers = {}
  headers["Content-Type"]="application/json"
  local params = {}
  params.headers = headers
  local bodyParams = {
    token=token;
    platform=platform;
    applicationArn=applicationArn;
  }
  params.body = json.encode(bodyParams)
  requestId = network.request(self.host.."/".."aws_sns".."/".."registerDevice", method, networkListener, params )
  self:addRequest(requestId)
end 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

Graphsourcing 是如此强大的人，所有这些代码都是从视觉图形生成的！

我只展示了它的一小部分，因为它很大！

## 阿尔法用户？

如果你想成为阿尔法用户，请给我发邮件寻求指导，或者你可以免费开始，只需使用少量的回购(因为这是开放源码技术)

[Slothking app](https://app.slothking.online)
[CLI](https://github.com/slothking-online/sloth)
[样板文件以后台](https://github.com/slothking-online/backend-nodets-boilerplate)
[状态管理器为基础](https://github.com/slothking-online/state)