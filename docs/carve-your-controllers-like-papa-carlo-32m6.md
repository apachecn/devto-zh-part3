# 像卡洛爸爸一样雕刻你的控制器

> 原文：<https://dev.to/evilmartians/carve-your-controllers-like-papa-carlo-32m6>

我们，Rails 开发人员，倾向于让我们的控制器*瘦*(和[模型胖](http://weblog.jamisbuck.org/2006/10/18/skinny-controller-fat-model)——哦，等等，[是](https://blog.makersacademy.com/forget-fat-models-its-time-for-skinny-controllers-and-skinny-models-a9b84ec481b7) [不再是真的](https://medium.com/@devlob/fat-models-and-skinny-controllers-well-not-exactly-918585b02348)；现在我们有了[脂肪服务](https://medium.com/marmolabs/skinny-models-skinny-controllers-fat-services-e04cfe2d6ae)😉).

我们添加了不同的抽象层:[交互者](https://github.com/collectiveidea/interactor)、[策略](https://github.com/palkan/action_policy)、查询对象、[表单对象](https://github.com/trailblazer/reform)，凡是你能想到的。

在处理基于查询参数的过滤器时，我们仍然需要编写类似这样的代码:

```
class EventsController < ApplicationController
  def index
    events = Event.all.
      page(params[:page] || 1).
      order(sort_params)

    events = events.where(
      type: params[:type_filter]
    ) if params[:type_filter].in?(%w[published draft])

    events = events.future if params[:time_filter] == "future"
    # NOTE: `searched` is a scope or class method defined on the Event model
    events = events.searched(params[:q]) if params[:q].present?

    render json: events
  end

  def sort_params
    sort_by = params[:sort_by].in?(%w[id name started_at]) ?
              params[:sort_by] :
              :started_at
    sort_order = params[:sort].in?(%w[asc desc]) ? params[:sort] : :desc
    { sort_by => sort_order }
  end
end 
```

尽管有一个非瘦控制器，我们有很难**读取**、**测试**、**维护**的代码。

我想展示我们如何使用新宝石 [Rubanok](https://github.com/palkan/rubanok) (在俄语中是“手刨”的意思)来*雕刻*这个控制器(就像*爸爸卡洛*用木头雕刻*布拉蒂诺*—[俄罗斯匹诺曹](https://en.wikipedia.org/wiki/Buratino))

Rubanok 是一个通用的数据转换工具，由基于散列的参数驱动。

好吧，这听起来很奇怪😕

让我们看看上面的例子:我们取我们的*数据*(活动记录关系，`Event.all`)并根据用户的*输入* ( `params`对象)对其进行转换。

如果我们可以从控制器的某个地方提取这个*转换*会怎么样？

你可能会问:“这种抽象有什么意义”？

有[几个原因](https://gemcheck.evilmartians.io):

*   让我们的代码更易读(更少的逻辑分支)
*   让我们的代码更容易测试(并且[让测试更快](https://evilmartians.com/chronicles/testprof-a-good-doctor-for-slow-ruby-tests)
*   使我们的代码可重用(例如，排序和分页逻辑也可能在其他控制器中使用)。

让我首先向您展示一下当我们添加 Rubanok:
时，上面的控制器是什么样子的

```
class EventsController < ApplicationController
  def index
    events = planish Event.all
    render json: events
  end
end 
```

就是这样。再瘦不过了(好吧，我们可以做`render json: planish(Event.all)`)。

`planish`方法下隐藏着什么？

这是一种特定于 Rails 的方法(顺便说一句，Rubanok 本身是 **Rails-free** ),它利用了约定优先于配置的原则，并且可以展开为如下:

```
def index
  events = EventsPlane.call(Event.all, params.to_unsafe_h)
  render json: events
end 
```

而`EventsPlane`类是所有~~魔法~~转换发生的地方:

```
class EventsPlane < Rubanok::Plane
  TYPES = %w[draft published].freeze
  SORT_FIELDS = %w[id name started_at].freeze
  SORT_ORDERS = %w[asc desc].freeze

  map :page, activate_always: true do |page: 1|
    raw.page(page)
  end

  map :type_filter do |type_filter:|
    next raw.none unless TYPES.include?(type_filter)

    raw.where(type: type_filter)    
  end

  match :time_filter do
    having "future" do
      raw.future
    end

    default { |_time_filter| raw.none }
  end

  map :sort_by, :sort do |sort_by: "started_at", sort: "desc"|
    next raw unless SORT_FIELDS.include?(sort_by) &&
      SORT_ORDERS.include?(sort)
    raw.order(sort_by => sort)
  end

  map :q do |q:|
    raw.searched(q)
  end
end 
```

*平面*类描述了如何根据传递的`params`转换数据(可通过`raw`方法访问):

*   如果相应的值不为空(即空字符串被忽略)，使用`map`提取关键字并应用转换；这里你可以依赖 Ruby 关键字参数默认值——很酷，对吧？
*   使用`match`选择*变压器*时也要考虑数值。

现在我们可以单独为我们的*平面*编写测试:

```
describe EventsPlane do
  let(:input) { Event.all }
  # add default transformations
  let(:output) { input.page(1).order(started_at: :desc) }
  let(:params) { {} }

  # we match the resulting SQL query and do not make real queries
  # at all–our tests are fast!
  subject { described_class.call(input, params).to_sql }

  specify "q=?" do
    params[:q] = "wood"

    expect(subject).to eq(output.searched("wood").to_sql)
  end

  specify "type_filter=<valid>" do
    params[:type_filter] = "draft"

    expect(subject).to eq(output.where(type: "draft").to_sql)
  end

  specify "type_filter=<invalid>" do
    params[:type_filter] = "unpublished"

    expect(subject).to eq(output.none.to_sql)
  end

  # ...
end 
```

在你的控制器/请求测试中，你需要做的就是检查一个特定的平面是否被使用:

```
describe EventsController do
  subject { get :index }

  specify do
    expect { subject }.to have_planished(Event.all).
      with(EventsPlane)
  end
end 
```

所以，Rubanok 适合于*雕刻*控制器，但是我们说它是通用的——让我们用 GraphQL 例子来证明它！

```
module GraphAPI
  module Types
    class Query < GraphQL::Schema::Object
      field :profiles, Types::Profile.connection_type, null: false do
        argument :city, Int, required: false
        argument :home, Int, required: false
        argument :tags, [ID], required: false
        argument :q, String, required: false
      end

      def profiles(**params)
        ProfilesPlane.call(Profile.all, params)
      end
    end
  end
end 
```

看起来我们刚刚发明了*瘦子*🙂

查看 [Rubanok](https://github.com/palkan/rubanok) repo 了解更多信息，并随时提出您的想法！

**P.S.** 还有一个更老的 gem [`filterer`](https://github.com/dobtco/filterer) 也实现了类似的想法(虽然是以 PORO 的方式)，但是侧重于 ActiveRecord，缺乏测试支持。

想知道在大型应用程序中我们还用什么抽象来组织代码吗？查看我的其他帖子，如[“在主动交付的 Rails 中制作用户通知”](https://dev.to/evilmartians/crafting-user-notifications-in-rails-with-active-delivery-5cn6)或[“Clowne:微笑着克隆 Ruby 模型”](https://evilmartians.com/chronicles/clowne-clone-ruby-models-with-a-smile)，以及项目，如 [Action Policy](https://actionpolicy.evilmartians.io) 和 [Anyway Config](https://github.com/palkan/anyway_config) 。

* * *

阅读更多关于 https://evilmartians.com/chronicles 的文章！