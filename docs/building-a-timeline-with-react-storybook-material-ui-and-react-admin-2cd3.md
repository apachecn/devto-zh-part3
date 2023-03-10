# 使用 React、故事书、材料用户界面和 React-Admin 构建时间线

> 原文：<https://dev.to/fzaninotto/building-a-timeline-with-react-storybook-material-ui-and-react-admin-2cd3>

对于一个客户项目，我必须在一个由 [react-admin](https://github.com/marmelab/react-admin) 支持的后端应用程序中添加一个最近用户操作的时间表。React-admin 没有提供现成的时间轴组件，所以我不得不使用纯 React 来实现它。在这个过程中，我使用了一些编码技巧，所以这是一个很好的教程。

## 用户故事:跟踪审计线索的时间表

这一切都是从 Acme 公司的一个用户故事开始的，该公司正在构建一个后端应用程序来简化它的一些业务任务。客户需求提到了故事要解决的最终用户问题，那就是:

> 作为一名经理，我想监控我的团队的活动，这样我就可以发现任何不当行为

在与客户讨论后，我们一致认为最好的解决方案是在*审计跟踪*中记录团队成员的大部分行动，并在*时间线*中按时间倒序(最新行动在前)向经理显示这些行动。

## UX:别多此一举

通常情况下，在这一点上，UX 的专家应该与实际的经理交谈，为时间线设计一个完美的用户体验。但幸运的是，著名的 UX 从业者 Jakob Nielsen 发现了一个可以减少 90% UX 工作量的定律:

> 用户大部分时间花在其他网站上。这意味着用户更喜欢你的网站像他们已经知道的其他网站一样工作。
> 
> *雅各布定律*

如果 Acme 必须为其经理设计一个时间表，它应该看起来像经理可能已经使用的其他时间表。他们确实在推特、脸书和其他网站上看到了很多这样的内容。

因此，没有必要在 UX 问题上重新发明轮子:为了设计 UX 的时间线，我明目张胆地复制了脸书的 Feed 页面，通过按天分组事件添加了一点我的想法。以下是生成的实体模型:

[![Loaded Timeline Mockup](img/47e6c3d53c856c51fa996d014522f824.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2-1IuCKt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.com/static/78237c028ee0f45ec2b4f067e01683b7/72ea3/Timeline.webp)

**提示**:雅各布定律和其他 18 个 UX 定律一起出现在 UX 网站的优秀[定律中。我强烈推荐它来获得对 UX 原则的基本理解。](https://lawsofux.com/)

## 写假数据

为了写一个模型，我必须创建假数据。这让我发现了时间线应该处理的一些关键案例:

*   有些事件没有作者。在这种情况下，事件作者应该被命名为“匿名”
*   有些事件可能有一个很长的标签，并且可能跨越几行。这也意味着标题应该使用省略号来避免破坏布局。
*   事件标签似乎不容易自动化。例如，最终用户更喜欢看到“XXX 评论”而不是“XXX 添加了评论”。
*   活动日期应该使用用户的区域设置。幸运的是，现代浏览器可以自己做到这一点。

生成虚假数据始终是设计阶段非常重要的一部分。它帮助我发现极限情况，并起草在开发过程中应该使用的数据结构。

## 设计不完整状态

从 UX 的角度来看，这项工作已经接近尾声。我只需要设计当时间线不包含事件时，经理应该看到什么，这可能发生在三种情况下:

*   **加载**:应用程序已经加载并请求了事件，但是服务器还没有响应。这是一种过渡状态，但可能会持续几秒钟。
*   **空**:当服务器用一个空的事件列表来回答的时候——大概是经理团队的早期。
*   **错误**:服务器超时或响应出错。是的，那也会发生。

对于加载页面，我使用了一个“框架”屏幕:

[![Loading Timeline Mockup](img/b78970899ba7a1af141ed1854f06423b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K7eF8fT---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.com/static/000e4cdf5551750070cc600c94788c44/55534/Loading.png)

对于空白页面，邀请开始使用应用程序:

[![Empty Timeline Mockup](img/0262d9fcf8a055a8f3f54d0ff0d9caa0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vbGjyIv---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.com/static/3ea3232fe2fcc859a553ad042f6c4a4e/55534/Empty.png)

对于这个特定的应用程序，错误已经被全局处理(通过通知)，所以我不需要设计错误页面。

至于 UI，客户已经选择了材料设计，所以我只需要将模型翻译成在 [material.io](https://material.io/design/) 展示的一些组件。

## 最难的部分是给事物命名

基于模型，我可以直接进入代码，但我喜欢先花点时间在白板上写东西。这是因为很容易迷失在实现细节中，而这些细节会导致糟糕的代码架构。最好从领域开始，列出职责，并决定代码的哪一部分将对每一项负责。是的，这就是实践中的[领域驱动设计](https://en.wikipedia.org/wiki/Domain-driven_design)。

所以我在模型上画了一些方框，把时间线分解成一个组件的树形结构，每个组件都有自己的职责。我禁止自己为每个组件添加注释来解释它的作用。相反，我花了尽可能多的时间来明确地用**命名组件**，这样就不需要更多的注释了。这是因为卡尔顿定律:

> 计算机科学只有两个硬东西:缓存失效和事物命名。
> 
> 菲尔·卡尔顿

所以我们开始吧:

*   `Timeline`
    *   `TimelineLoaded`
    *   `EventList`
        *   `EventItem`
        *   `Avatar`
    *   `TimelineLoading`
    *   `TimelineEmpty`

我在另一个具有相同领域知识的开发人员身上测试了这些名称，以确保它们都有意义，并且具有正确的含义。一旦名字被验证，我就可以开始写代码了。

## 由树叶开始

我必须开发 7 个组件。那太多了。我应该从哪里开始？

我通常从树结构中最深的部分开始，那些没有孩子的部分。这有四个原因:

*   叶组件通常只是表示性的。因此，在我开始考虑用实际数据填充之前，我就可以快速地与客户一起迭代设计。
*   比较好考。叶组件没有依赖关系，只需要少量数据即可呈现。
*   叶组件通常更简单(不需要考虑获取数据，链中的“连接”组件更高)，所以我不会从一开始就冒被阻塞的风险
*   我可以设计要传递给组件的数据的形状，而不需要对子组件做任何假设

总的来说，从树叶开始有助于以正确的方式划分责任/关注点

所以让我们从`Avatar`组件开始。它依赖于 material-ui 的`Avatar`，并使用[Gravatar 服务](https://gravatar.com/)来显示基于用户电子邮件的用户图片。这只是一个普通的 React.js 组件:

```
// in src/Avatar.js
import React from 'react';
import MuiAvatar from '@material-ui/core/Avatar';
import { withStyles } from '@material-ui/core/styles';
import md5 from 'blueimp-md5';

const styles = {
    avatar: {
        width: 25,
        height: 25,
    },
};

const AvatarView = ({ user, classes }) => (
    <MuiAvatar
        className={classes.avatar}
        src={
            user
                ? `https://www.gravatar.com/avatar/${md5(user.email)}?d=retro`
                : `https://www.gravatar.com/avatar/?d=mp`
        }
    />
);

const Avatar = withStyles(styles)(AvatarView);

export default Avatar; 
```

## 利用故事书孤立地发展

现在，为了单独渲染`Avatar`，我设置了一本[故事书](https://github.com/storybooks/storybook)并写了一个*故事*:

```
// in src/Avatar.stories.js
import React from 'react';
import { storiesOf } from '@storybook/react';
import Avatar from './Avatar';

const userWithAvatar = {
    email: 'jitewaboh@lagify.com',
};

const userWithNoAvatar = {
    email: 'lelafeng@example.com',
};

storiesOf('Avatar', module)
    .add('basic', () => <Avatar user={userWithAvatar} />)
    .add('using fallback', () => <Avatar user={userWithNoAvatar} />)
    .add('anonymous', () => <Avatar />); 
```

我可以检查故事书是否显示了一个化身，即使`user`没有定义。

[![Storybook Avatar](img/32f9a1bae6b54d494873109f85fa3f21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s_AoEUFg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_Avatar-f5a9b7a0b5c89afd5be68a7c04c7e53a.gif)

`Avatar`，检查！该组件不包含逻辑，所以我不会为它编写单元测试。

## 以一个故事开始

现在，让我们继续讨论`EventItem`组件。这次，我先写故事。它迫使我思考组件应该期望的数据形式。现在，让我们考虑用户数据包含在事件中:

```
// in src/EventItem.stories.js
import React from 'react';
import { storiesOf } from '@storybook/react';
import EventItem from './EventItem';

const event = {
    label: 'modified post "Hello World"',
    createdAt: '2019-03-11T12:34:56.000Z',
    author: {
        name: 'John Doe',
        email: 'jitewaboh@lagify.com',
    },
};

const anonymousEvent = {
    label: 'liked "Lorem Ipsum"',
    createdAt: '2019-03-11T12:34:56.000Z',
};

const eventWithLongName = {
    label:
        'commented "I don\'t agree. You should never try to do things this way, or you\'ll end up in a bad place."',
    createdAt: '2019-03-11T12:34:56.000Z',
    author: {
        name: 'Lela Feng',
        email: 'lelafeng@example.com',
    },
};

storiesOf('EventItem', module)
    .add('basic', () => <EventItem event={event} />)
    .add('anonymous', () => <EventItem event={anonymousEvent} />)
    .add('long event name', () => <EventItem event={eventWithLongName} />); 
```

然后就是开发`EventItem`本身的时候了。这里没有什么特别的，只是使用了基于`ListItem`组件的标准 material-ui 代码:

```
// in src/EventItem.js
import React from 'react';
import ListItem from '@material-ui/core/ListItem';
import ListItemText from '@material-ui/core/ListItemText';
import ListItemAvatar from '@material-ui/core/ListItemAvatar';
import { withStyles } from '@material-ui/core/styles';
import Avatar from './Avatar';

const styles = {
    truncate: {
        width: 500,
        whiteSpace: 'nowrap',
        overflow: 'hidden',
        textOverflow: 'ellipsis',
    },
};

const EventItemView = ({ event, classes }) => (
    <ListItem>
        <ListItemAvatar>
            <Avatar user={event.author} />
        </ListItemAvatar>
        <ListItemText
            primary={
                <div className={classes.truncate}>
                    <strong>
                        {event.author ? event.author.name : 'Anonymous'}
                    </strong>{' '}
                    {event.label}
                </div>
            }
            secondary={new Date(event.createdAt).toLocaleString()}
        />
    </ListItem>
);

const EventItem = withStyles(styles)(EventItemView);

export default EventItem; 
```

故事书帮助我验证代码。

[![Storybook EventItem](img/fd9244346f3904c8d8e286702b2ead99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vl_Uso3G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_EventItem-cf50100ae4b7c0239f272c4756563a96.gif)

## 爬构件树

现在`EventItem`已经完成了，我可以移到组件树并编写`EventList`组件了。我先写一个故事，测试数据:

```
// in src/EventList.stories.js
import React from 'react';
import { storiesOf } from '@storybook/react';
import EventList from './EventList';

const events = [
    {
        id: 1234,
        label: 'modified post "Hello World"',
        createdAt: '2019-01-10T17:15:56.000Z',
        author: {
            name: 'John Doe',
            email: 'jitewaboh@lagify.com',
        },
    },
    {
        id: 1233,
        label: 'created new post "Hello World"',
        createdAt: '2019-01-10T08:54:00.000Z',
        author: {
            name: 'John Doe',
            email: 'jitewaboh@lagify.com',
        },
    },
];

storiesOf('EventList', module)
    .add('basic', () => <EventList events={events} />)
    .add('empty', () => <EventList />); 
```

我必须为每个事件添加一个`id`字段，因为列表呈现了一个`EventItem`组件的数组，而 react 期望列表中的元素有一个惟一的标识符。列表本身很简单:

```
// in src/EventList.js
import React from 'react';
import Card from '@material-ui/core/Card';
import List from '@material-ui/core/List';
import { withStyles } from '@material-ui/core/styles';
import EventItem from './EventItem';

const styles = {
    root: {
        width: 600,
    },
};

const EventListView = ({ events = [], classes }) => (
    <Card className={classes.root}>
        <List>
            {events.map(event => (
                <EventItem event={event} key={event.id} />
            ))}
        </List>
    </Card>
);

const EventList = withStyles(styles)(EventListView);

export default EventList; 
```

[![Storybook EventList](img/8b1b0416cf817f6d825f83a4cf7bb325.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DivanuoR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_EventList-7673864938f88bd2e450677215c9e5aa.gif)

## 提取逻辑到非组件代码

仍然在组件层次结构中向上移动，我现在考虑`<TimelineLoaded>`组件。它应该显示按天分组的事件。我假设服务器只会发送一组事件，所以由客户机按天汇总它们。

我可以在一个组件中编写聚合代码，但是因为它是纯 JS 代码，而且因为我想单独测试它，所以我决定将它作为纯函数编写在一个独立的文件中。

```
// in src/groupByDay.js
const sortByDate = (a, b) => new Date(b).valueOf() - new Date(a).valueOf();

const getDayForEvent = event => {
    const date = new Date(event.createdAt);
    date.setMilliseconds(0);
    date.setSeconds(0);
    date.setMinutes(0);
    date.setHours(0);
    return date.toISOString();
};

const groupByDay = events => {
    const groups = events.reduce((days, event) => {
        const day = getDayForEvent(event);
        if (!days[day]) {
            days[day] = [];
        }
        days[day] = days[day].concat(event);
        return days;
    }, {});
    return {
        days: Object.keys(groups).sort(sortByDate),
        eventsByDay: groups,
    };
};

export default groupByDay; 
```

因为它是普通的 JavaScript，所以这段代码很容易用 Jest 测试——不需要启动`enzyme`或`react-testing-library`:

```
// in src/groupByDay.test.js
import groupByDay from './groupByDay';

describe('groupByDay', () => {
    it('should aggregate events by day', () => {
        const events = [
            { createdAt: '2019-01-05T12:56:31.039Z', label: 'foo1' },
            { createdAt: '2019-01-05T09:12:43.456Z', label: 'foo2' },
            { createdAt: '2019-01-04T12:34:56.789Z', label: 'foo3' },
        ];
        expect(groupByDay(events)).toEqual({
            days: ['2019-01-04T23:00:00.000Z', '2019-01-03T23:00:00.000Z'],
            eventsByDay: {
                '2019-01-04T23:00:00.000Z': [
                    {
                        createdAt: '2019-01-05T12:56:31.039Z',
                        label: 'foo1',
                    },
                    {
                        createdAt: '2019-01-05T09:12:43.456Z',
                        label: 'foo2',
                    },
                ],
                '2019-01-03T23:00:00.000Z': [
                    {
                        createdAt: '2019-01-04T12:34:56.789Z',
                        label: 'foo3',
                    },
                ],
            },
        });
    });
}); 
```

这是我需要编写的唯一一个单元测试。

我编写的大多数组件纯粹是表示性的(不需要测试)，因为我习惯于将所有可以测试的东西提取到独立的函数中。这样，我避免了单元测试的陷阱。

## 保持表象成分小

组件应该显示按天分组的事件。和以前一样，我从写一个故事开始——实际上，大部分工作是写测试数据。幸运的是，我之前已经为模型设想了测试数据，所以用 JSON 编写它们只是一个问题。

```
// in src/TimelineLoaded.stories.js
import React from 'react';
import { storiesOf } from '@storybook/react';
import TimelineLoaded from './TimelineLoaded';

const events = [
    {
        id: 1234,
        label: 'modified post "Hello World"',
        createdAt: '2019-01-10T17:15:56.000Z',
        author: {
            name: 'John Doe',
            email: 'jitewaboh@lagify.com',
        },
    },
    {
        id: 1233,
        label: 'created new post "Hello World"',
        createdAt: '2019-01-10T16:34:00.000Z',
        author: {
            name: 'John Doe',
            email: 'jitewaboh@lagify.com',
        },
    },
    {
        id: 1232,
        label:
            'commented "I don\'t agree. You should never try to do things this way, or you\'ll end up in a bad place."',
        createdAt: '2019-01-09T15:53:56.000Z',
        author: {
            name: 'Lela Feng',
            email: 'lelafeng@example.com',
        },
    },
    {
        id: 1231,
        label: 'deleted comment "Totally."',
        createdAt: '2019-01-09T11:04:56.000Z',
        author: {
            name: 'Brandon Hood',
            email: 'brandon@example.com',
        },
    },
    {
        id: 1230,
        label: 'liked "Lorem Ipsum"',
        createdAt: '2019-01-09T09:12:56.000Z',
    },
];

storiesOf('TimelineLoaded', module)
    .add('basic', () => (
        <TimelineLoaded events={events.slice(0, -1)} total={5} />
    ))
    .add('fully loaded', () => <TimelineLoaded events={events} />)
    .add('empty', () => <TimelineLoaded />); 
```

同样，下面的`<TimelineLoaded>`组件中的逻辑非常少。只有两种情况测试极限情况(空事件列表，这是不正常的，以及满载事件列表，这是正常的)。剩下的都是表象。

```
// in src/TimelineLoaded.js
import React from 'react';
import Typography from '@material-ui/core/Typography';
import Button from '@material-ui/core/Button';
import { withStyles } from '@material-ui/core/styles';

import EventList from './EventList';
import groupByDay from './groupByDay';

const styles = {
    root: {
        width: 600,
        margin: 'auto',
    },
    day: {
        marginBottom: '1em',
    },
};

const getDayString = date =>
    new Date(date).toLocaleDateString(undefined, {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric',
    });

const TimelineLoadedView = ({
    events = [],
    handleLoadMore,
    total,
    classes,
}) => {
    const { days, eventsByDay } = groupByDay(events);
    if (days.length === 0) {
        return (
            <Typography color="error">
                Error: This list should not be empty.
            </Typography>
        );
    }
    return (
        <div className={classes.root}>
            {days.map(day => (
                <div key={day} className={classes.day}>
                    <Typography variant="subheading" gutterBottom>
                        {getDayString(day)}
                    </Typography>
                    <EventList events={eventsByDay[day]} />
                </div>
            ))}
            {events.length < total && (
                <Button variant="contained" onClick={handleLoadMore}>
                    Load more events
                </Button>
            )}
        </div>
    );
};

const TimelineLoaded = withStyles(styles)(TimelineLoadedView);

export default TimelineLoaded; 
```

[![Storybook TimelineLoaded](img/6adfdddc1eb5e55513967f37a7ea73ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U0qEn9vK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_TimelineLoaded-755ae6add8b68bcef9080b1c8387d897.gif)

保持组件小使得对代码进行推理变得容易。

请注意，我还没有编写一行获取事件的代码。因此，上面的所有代码都是纯 react 和 material-ui。没有冗余，没有反应-管理。

## 管理 UI 状态

现在是处理`<Timeline>`组件的时候了。该组件获取数据，并决定显示以下三个组件之一:

*   `<TimelineLoading>`
*   `<TimelineLoaded>`
*   `<TimelineEmpty>`

**提示**:我在本教程中没有包括`<TimelineLoaded>`和`<TimelineEmpty>`的代码，但是你可以在源代码中找到它们，链接在教程的最后。

我的第一反应是使用 react-admin 的`<List>`组件，让 react-admin 获取事件。这样，我只需根据 react-admin 获取的数据决定呈现哪个`<TimelineXXX>`组件。这意味着我最初编写了如下的`Timeline`组件:

```
// in src/Timeline.js
import React from 'react';
import { List } from 'react-admin';

import TimelineLoaded from './TimelineLoaded';
import TimelineLoading from './TimelineLoading';
import TimelineEmpty from './TimelineEmpty';

export const TimelineView = ({ ids, data, total, loadedOnce }) =>
    !loadedOnce ? (
        <TimelineLoading />
    ) : ids.length > 0 ? (
        <TimelineLoaded
            events={ids.map(id => data[id])}
            total={total}
        />
    ) : (
        <TimelineEmpty />
    );

const Timeline = props => (
    <List {...props}>
        <TimelineView />
    </List>
);

export default Timeline; 
```

事实上，这个脚本包含两个组件:一个视图(`TimelineView`)和一个控制器(`Timeline`)。

`TimelineView`组件独立于 react-admin，所以很容易用 Storybook 进行测试。我重用了`TimelineLoaded`故事中的假时间轴数据:

```
// in src/Timeline.stories.js
import React from 'react';
import { storiesOf } from '@storybook/react';
import { TimelineView } from './Timeline';
import { events } from './TimelineLoaded.stories.js';

storiesOf('Timeline', module)
    .add('loading', () => <TimelineView />)
    .add('loaded', () => (
        <TimelineView
            ids={events.map(event => event.id)}
            data={events.reduce(
                (data, event) => ({ ...data, [event.id]: event }),
                {}
            )}
            total={10}
            loadedOnce={true}
        />
    ))
    .add('empty', () => <TimelineView ids={[]} data={{}} loadedOnce={true} />); 
```

[![Storybook TimelineView](img/587fd74b8c2e4bdbef6cdb1e753cacf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bC4GtgFR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_Timeline-bbadcf7638540cf5a0079a77d43c4ab6.gif)

## 与 React-Admin 集成

在`Timeline`组件中，react-admin 的`<List>`组件获取、计算并向其子组件注入`ids`、`data`、`total`和`loadedOnce`道具。

为了测试`Timeline`，我不得不使用 is 作为 react-admin 中`<Resource name="events" />`的`list`道具:

```
// in src/App.js
import React, { Component } from 'react';
import { Admin, Resource } from 'react-admin';

import dataProvider from './dataProvider';
import Timeline from './Timeline';

const App = () => (
    <Admin dataProvider={dataProvider}>
        <Resource name="events" list={Timeline} />
    </Admin>
);

export default App; 
```

我可以用故事书进行视觉测试:

```
// in src/App.stories.js
import React from 'react';
import { storiesOf } from '@storybook/react';
import App from './App';

storiesOf('App', module).add('basic', () => <App />); 
```

这样做很好:首先出现`<TimelineLoading>`(而`<List>`从`dataProvider`获取事件)，然后事件出现在`<TimelineLoaded>`中。

[![Storybook App1](img/39d06ffa90d0b42c664edc9fb07f92b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u1_HR51i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_App1-933ac0133d401d58a42bac946d64684b.gif)

但是这种方法有一个 UI 问题:react-admin 的`<List>`呈现一个材质 ui `<Paper>`，所以`<Timeline>`显示的是纸中之纸。不满意。

## 使用 React-Admin ListController 组件自定义列表布局

所以我决定更深入一点，使用 react-admin 的`<List>`的控制器部分，而不是 UI。事实上，react-admin 在一个名为`<ListController>`的组件中获取所有数据，该组件将渲染委托给其子组件(使用[渲染道具模式](https://reactjs.org/docs/render-props.html))。因此，我可以通过编写以下代码来克服“纸上谈兵”的问题:

```
// in src/Timeline.js
import React from 'react';
import { ListController } from 'react-admin';

// ...

const Timeline = props => (
    <ListController {...props}>
        {controllerProps => <TimelineView {...controllerProps} />}
    </ListController>
); 
```

这有点太激烈了，因为`<List>`组件过去负责页面标题。仅使用`<ListController>`，页面标题为空。所以我还需要一个改变来使它工作，那就是使用 react-admin 的``组件:

```
// in src/Timeline.js
import React from 'react';
import { ListController, Title } from 'react-admin';

// ...

const Timeline = props => (
    <ListController {...props}>
        {controllerProps => (
            <>
                
                <TimelineView {...controllerProps} />
            </>
        )}
    </ListController>
); 
```

现在 UI 在灰色背景上显示时间线，而不是在纸上。从 UI 的角度来看，那是成功的！

[![Storybook App2](img/2a114311bee819bfaa8f3d06a5264988.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7wnXUV5X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_App2-8155c521fe9aed9b7e0aa8b1fe4159af.gif)

## 使分页工作

“加载更多事件”按钮暂时不起作用。组件需要一个我还没有包含的属性。我可以用`<ListController>`准备的`controllerProps`——它们包括一个`page`和一个`setPage`道具。

但是`<ListController>` *用下一页替换*当前页，而在时间线中，当用户点击“加载更多事件”时，他们期望看到除了之前的事件之外的新事件*出现。我不得不使用一个本地状态技巧来继续依赖`<ListController>`。在这个过程中，我不得不把`<TimelineView>`变成一个类组件:*

```
// in src/Timeline.js
import React, { Component } from 'react';
import { ListController, Title } from 'react-admin';

import TimelineLoaded from './TimelineLoaded';
import TimelineLoading from './TimelineLoading';
import TimelineEmpty from './TimelineEmpty';

class TimelineView extends Component {
    state = {
        events: [],
        latestId: null,
    };

    static getDerivedStateFromProps(props, state) {
        const { ids, data } = props;
        const latestId = ids[ids.length - 1];
        if (latestId !== state.latestId) {
            const newEvents = ids.map(id => data[id]);
            return {
                events: state.events.concat(newEvents),
                latestId,
            };
        }

        return null;
    }

    handleLoadMore = () => {
        this.props.setPage(this.props.page + 1);
    };

    render() {
        const { events } = this.state;
        const { total, loadedOnce } = this.props;
        return !loadedOnce ? (
            <TimelineLoading />
        ) : events.length > 0 ? (
            <TimelineLoaded
                events={events}
                total={total}
                handleLoadMore={this.handleLoadMore}
            />
        ) : (
            <TimelineEmpty />
        );
    }
}

const Timeline = props => (
    <ListController {...props}>
        {controllerProps => (
            <>
                
                <TimelineView {...controllerProps} />
            </>
        )}
    </ListController>
);

export default Timeline; 
```

“加载更多事件”按钮现在工作，但有一个警告。如果用户点击“加载更多事件”，`page`从 1 增加到 2，第 2 页的事件出现在初始事件的下面。但是，如果用户刷新列表，屏幕上只显示第 2 页中的事件。这是为什么呢？

[![Storybook App3](img/7165620b33b14e0c42fe6948a7d94917.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ChWImVBP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_App3-59b265d44e60566d74f2ddacead0514c.gif)

跟踪当前页面，这样下次用户重新加载列表时，他们会看到离开列表前在屏幕上看到的页面。所以在加载更多事件后，`<ListController>`默认加载第 2 页。我的把戏不太管用。

## 使用自定义列表控制器

事实上，`<ListController>`做了很多我不需要的事情。它处理过滤器、自定义排序顺序和查询字符串。由于它不能按照我需要的方式处理分页，也许我可以用我自己的定制组件替换`ListController`？

我的新组件必须复制两件事:

*   分派一个 Redux 操作(`crudGetList`)来获取事件
*   从状态中抓取`data`和`ids`。

嗯，应该不会太难写吧？

```
// in src/Timeline.js
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { Title, crudGetList } from 'react-admin';
import TimelineLoaded from './TimelineLoaded';
import TimelineLoading from './TimelineLoading';
import TimelineEmpty from './TimelineEmpty';

export class Timeline extends Component {
    page = 1;
    constructor(props) {
        super(props);
        this.state = {
            events: props.ids.map(id => props.data[id]),
            latestId: props.ids[props.ids.length - 1],,
        };
    }

    updateData = () => {
        this.props.crudGetList(
            'events',
            { page: this.page, perPage: 10 },
            { field: 'id', order: 'DESC' }
        );
    };

    componentDidMount() {
        this.updateData();
    }

    componentDidUpdate(prevProps, prevState) {
        if (this.props.ids !== prevProps.ids) {
            const { ids, data } = this.props;
            const latestId = ids[ids.length - 1];
            if (latestId && latestId !== prevState.latestId) {
                const newEvents = ids.map(id => data[id]);
                this.setState(state => ({
                    events: state.events.concat(newEvents),
                    latestId,
                }));
            }
        }
    }

    handleLoadMore = () => {
        this.page = this.page + 1;
        this.updateData();
    };

    render() {
        const { events } = this.state;
        const { total, loadedOnce } = this.props;
        return (
            <>
                
                {!loadedOnce ? (
                    <TimelineLoading />
                ) : events.length === 0 ? (
                    <TimelineEmpty />
                ) : (
                    <TimelineLoaded
                        events={events}
                        total={total}
                        handleLoadMore={this.handleLoadMore}
                    />
                )}
            </>
        );
    }
}

Timeline.defaultProps = {
    ids: [],
    data: {},
    crudGetList: () => null,
};

const mapStateToProps = state => ({
    ids: state.admin.resources.events.list.ids,
    data: state.admin.resources.events.data,
    total: state.admin.resources.events.list.total,
    loadedOnce: state.admin.resources.events.list.loadedOnce,
});

export default connect(
    mapStateToProps,
    { crudGetList }
)(Timeline); 
```

无需更改应用程序代码，它就能工作:

```
import React, { Component } from 'react';
import { Admin, Resource } from 'react-admin';

import dataProvider from './dataProvider';
import Timeline from './Timeline';

const App = () => (
    <Admin dataProvider={dataProvider}>
        <Resource name="events" list={Timeline} />
    </Admin>
);

export default App; 
```

[![Storybook App4](img/776bf1ecf29ae0f0c828db151722acc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZSAiDrzf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_App4-5ec7887bee0588cf741db6dab27526f0.gif)

不过，敏锐的观察者可能会注意到一个小问题。当用户点击“加载更多”，然后浏览到另一个页面，再浏览回时间线时，他们会在看到第 1 页的事件之前简要地看到第 2 页的事件。这是因为当时间轴挂载时，它从 Redux 存储中获取事件列表。最后一次安装时间线是为了第 2 页。因此，页面 2 中的事件会在获取页面 1 时出现。

要解决这个问题，我只需在时间轴卸载时重置存储中的事件列表:

```
export class Timeline extends Component {
    // ...
    componentWillUnmount() {
        this.page = 1;
        this.updateData();
    }
} 
```

现在用户体验完美无瑕。

[![Storybook App5](img/afe6202bb1ffb09b23672fab13750a22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UN-EvmeQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marmelab.com/storybook_App5-d9b4cf0e7faf3ed208c102f8b2334409.gif)

这个版本的`<Timeline>`更清晰，因为它只用了 Redux。它还表明，用自己的组件替换像`<List>`这样重要的组件是可能的。React-admin 实际上被设计成允许容易地替换它的任何组件。React-admin 试图做好一件事，并在用例需要更具体的东西时让您使用自己的组件。

这个组件也很少使用 react-admin——事实上，只使用了 Redux 存储和一个 action creator。但是这些是 react-admin 的核心，如果你喜欢的话，是骨架。在许多情况下，当我想用 react-admin 构建一些特定的东西时，我最终只使用了那个核心。了解 react-admin 核心将让你自由地做任何你想做的事情。

## 利用国际化

在制造假事件时，我有点过于乐观了。我认为服务器可以返回事件*标签*，如下:

```
const events = [
    {
        id: 1234,
        label: 'modified post "Hello World"',
        createdAt: '2019-01-10T17:15:56.000Z',
        author: {
            name: 'John Doe',
            email: 'jitewaboh@lagify.com',
        },
    },
    {
        id: 1233,
        label: 'created new post "Hello World"',
        createdAt: '2019-01-10T16:34:00.000Z',
        author: {
            name: 'John Doe',
            email: 'jitewaboh@lagify.com',
        },
    },
    ...
]; 
```

但是客户希望应用程序可以在多种语言中使用，并在前端处理 i18n 逻辑。这意味着 API 必须以一种语言不可知的方式返回事件，它们实际上如下所示:

```
const events = [
    {
        id: 1234,
        object: 'post',
        objectName: 'Hello World',
        type: 'modify',
        createdAt: '2019-01-10T17:15:56.000Z',
        author: {
            name: 'John Doe',
            email: 'jitewaboh@lagify.com',
        },
    },
    {
        id: 1233,
        object: 'post',
        objectName: 'Hello World',
        type: 'create',
        createdAt: '2019-01-10T16:34:00.000Z',
        author: {
            name: 'John Doe',
            email: 'jitewaboh@lagify.com',
        },
    },
    ...
]; 
```

因此`EventItem`不能再使用`event.label`的数据。我选择使用[的 react-admin 翻译系统](https://marmelab.com/react-admin/Translation.html)将结构化的事件数据转换成本地化的字符串。这个想法是为一个事件生成一个翻译关键字，例如`event.post.modify`或`event.post.create`，并将这个标识符转换成地区词典中特定于语言的字符串。

```
import React from 'react';
import ListItem from '@material-ui/core/ListItem';
import ListItemText from '@material-ui/core/ListItemText';
import ListItemAvatar from '@material-ui/core/ListItemAvatar';
import { withStyles } from '@material-ui/core/styles';
+import { translate } from 'react-admin'; import Avatar from './Avatar';

// ...

-const EventItemView = ({ event, classes }) => ( +const EventItemView = ({ event, translate, classes }) => (
    <ListItem>
        <ListItemAvatar>
            <Avatar user={event.author} />
        </ListItemAvatar>
        <ListItemText
            primary={
                <div className={classes.truncate}>
                    <strong>
                        {event.author ? event.author.name : 'Anonymous'}
                    </strong>{' '}
-                   {event.label} +                   {translate(`event.${event.object}.${event.type}`, {
+                       name: event.objectName,
+                   })}
                </div>
            }
            secondary={new Date(event.createdAt).toLocaleString()}
        />
    </ListItem>
);

-const EventItem = withStyles(styles)(EventItemView); +const EventItem = translate(withStyles(styles)(EventItemView)); 
export default EventItem; 
```

我在 react-admin 字典文件中添加了翻译:

```
// in src/i18n/en.js
import englishMessages from 'ra-language-english';

export default {
    ...englishMessages,
    event: {
        post: {
            create: 'created a new post "%{name}"',
            modify: 'modified post "%{name}"',
        }
    }
} 
```

翻译字符串中的变量替换使翻译看起来更自然。

## 结论

我花了大约三个小时编写时间线代码，并将其插入 react-admin。你可以在 GitHub 上的[marmelab/timeline-react-admin 库](https://github.com/marmelab/timeline-react-admin)中找到最终代码。我真的很高兴我只需要写这么少的 react-admin 代码——事实上，我的大部分代码都是纯 react 的。我在`Timeline`组件中使用了 Redux，在`EventItem`中使用了 react-admin 的 i18n 实用程序。

React-admin 从不碍事:当我了解到`<List>`组件不符合用户故事时，我立刻替换了它。

唯一的困难是正确地给每个组件分配职责，并根据数据处理`Timeline`状态。

我希望这篇教程能帮助你设计自己的定制列表组件，并提供比 react-admin 的默认`<Datagrid>`组件更好的用户体验。