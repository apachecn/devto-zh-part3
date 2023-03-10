# 使用 WebRTC 的可伸缩广播

> 原文：<https://dev.to/aminarria/scalable-broadcasting-using-webrtc-2984>

# 想法

> WebRTC 是一个免费的开放项目，它通过简单的 API 为浏览器和移动应用程序提供实时通信(RTC)功能。WebRTC 组件已经过优化，可以最好地满足这一目的。

我们的想法非常简单:使用 WebRTC 创建视频广播(1 个用户向 N 个用户发送视频)。为此，我们将首先研究如何设计它，创建一个概念证明，并最终在 Erlang 中实现它。

## WebRTC 问题

您可能知道，WebRTC 使用对等连接来传输视频。这对于一对一的通话或者甚至是小组通话来说都是难以置信的。

[![WebRTC group call](img/252a209cdd46966dc48de0e1fe6db61d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xSrlMocB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z4qwcpji3tq5pkvzn67j.jpg)

问题是我们正在建立一个广播功能，这意味着可能有 100，1000，甚至 70 亿用户连接。因此，如果我们尝试使用 WebRTC，这意味着广播公司将不得不建立许多对等连接，并向每个人发送他想要广播的视频。因此，除非广播公司有足够的 CPU 能力和带宽，否则它将会崩溃。

[![WebRTC broadcast](img/9af15cf2c9b91bd4d8beb35f52c79cdd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tjh31kn0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ugt6nmufox9npaw8dx5l.jpg)

我们如何解决这个问题？嗯，我们所有的研究给了我们相同的基本解决方案:广播公司将视频发送到中央服务器，该服务器将视频发送给所有听众，中央服务器是 CPU/带宽负担重的服务器。听起来很简单，对吧？不幸的是，使用 WebRTC 做到这一点并不那么简单，因为它意味着点对点。这意味着我们不能简单地创建一个中央服务器，然后将视频传送给客户端。

[![scalable broadcast architecture](img/ffe4748cef9e4227cf2e8cb6b2ea4ea8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rrWjOGnW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rvu2sdnoqxqgijnot97f.jpg)

## 解

> Janus 是由 Meetecho 开发的 WebRTC 服务器，被认为是一个通用服务器。因此，除了实现与浏览器建立 WebRTC 媒体通信的方法、与浏览器交换 JSON 消息以及在浏览器和它们所连接的服务器端应用程序逻辑之间中继 RTP/RTCP 和消息之外，它本身不提供任何功能。任何特定的功能/应用程序都是由服务器端插件提供的，浏览器可以通过骏利联系以利用它们提供的功能。

太好了！我们有一些东西看起来可以解决我们关于使用 WebRTC 的中央服务器的所有问题，但在宣布这是“治愈所有疾病”之前，我们决定创建一个 PoC 来检查两件事:

*   验证广播公司/听众 WebRTC 连接是否如预期的那样运行(骏利的每个对等方只有一个连接)。
*   我们可以使用没有 Javascript 库的 Janus。

为什么专注于不使用 Janus 的 JS 库？因为我们计划使用我们的后端作为所有骏利产品的包装。使前端不必处理骏利的额外复杂性。

## 制作 PoC

现在，这个新旅程的第一个障碍是:为 Janus 创建一个 JS 客户端。通过查看[演示](https://janus.conf.meetecho.com/demos.html)和文档，我们发现我们需要使用[视频室插件](https://janus.conf.meetecho.com/docs/videoroom.html)。

首先，Janus 通过创建一个识别客户端的会话和一个与特定插件交互的句柄(类似于会话中的会话)来工作，所以让我们创建它们:

```
function start_session() {
  msg = JSON.stringify({'janus': 'create', 'transaction': 'create_session'});
  janusConnection.send(msg);
}

function start_handle() {
  msg = JSON.stringify({'janus': 'attach', 'transaction': 'create_handle', "plugin": "janus.plugin.videoroom", 'session_id': sessionId});
  janusConnection.send(msg);
} 
```

**重要:** Janus websocket 使用 JS 中的子协议`janus-protocol`只是另一个参数，但是如果你需要它，你可以根据 [RFC 6455](https://tools.ietf.org/html/rfc6455#section-11.3.4) 在报头`Sec-WebSocket-Protocol`字段中设置它

接下来，我们的广播员创建房间，加入房间，并开始广播:

```
function make_room() {
  msg = JSON.stringify({janus: 'message', transaction: 'create_room', body: {request: 'create'}, session_id: sessionId, handle_id: handleId});
  janusConnection.send(msg);
}

function join_room_publisher() {
  roomId = parseInt(roomInput.value);
  msg = JSON.stringify({janus: 'message', transaction: 'join_publisher', body: {request : 'join', ptype: 'publisher', room: roomId}, session_id: sessionId, handle_id: handleId});
  janusConnection.send(msg);
}

function start_broadcast() {
  peerConnection = new RTCPeerConnection(CONFIG);
  peerConnection.onicecandidate = on_ice_candidate;
  peerConnection.ontrack = on_track;

  navigator.mediaDevices.getUserMedia(CONFIG).then(function(stream) {
    localVideo.srcObject = stream;
    peerConnection.addStream(stream);

    peerConnection.createOffer().then(function(offer) {
      peerConnection.setLocalDescription(offer);
      msg = JSON.stringify({janus: 'message', transaction: 'publish', body: {request: 'publish'}, jsep: offer, session_id: sessionId, handle_id: handleId});
      janusConnection.send(msg);
    });
  });
} 
```

正如你所看到的，我们已经开始使用 WebRTC，知道骏利在`jsep`字段中用 WebRTC 回答来响应`publish`请求是很重要的。此外，默认情况下，骏利在 SDP 中包含 ICE 候选，您可以更改它以使用 trickle 发送它们，但是对于这个 PoC 来说，这样很好。

对于 broadcaser 方面的事情来说，这就差不多了，只是缺少两件事情(发送 ICE 候选和 keepalive)，但稍后将讨论它们，因为它们对于广播公司和观众来说是相同的。

现在，观众们。我们已经进行了会话并处理好了，所以我们只需要进入房间并开始接收来自广播公司的反馈。要做到这一点，我们需要两件事:房间 ID 和该广播公司的提要 ID。为什么有订阅源 ID？因为骏利没有对广播员的数量做出实际限制，你可以在创建房间的时候设置。

```
function join_room_subscriber() {
  roomId = parseInt(roomInput.value);
  feedId = parseInt(feedInput.value);

  peerConnection = new RTCPeerConnection(CONFIG);
  peerConnection.onicecandidate = on_ice_candidate;
  peerConnection.ontrack = on_track;

  msg = JSON.stringify({janus: 'message', transaction: 'join_subscriber', body: {request : 'join', ptype: 'subscriber', room: roomId, feed: feedId}, session_id: sessionId, handle_id: handleId});
  janusConnection.send(msg);
}

function join_subscriber(payload) {
  peerConnection.setRemoteDescription(new RTCSessionDescription(payload.jsep));
  peerConnection.createAnswer().then(function(answer) {
    peerConnection.setLocalDescription(answer);
    msg = JSON.stringify({janus: 'message', transaction: 'blah', body: {request: 'start'}, jsep: answer, session_id: sessionId, handle_id: handleId});
    janusConnection.send(msg);
  });
} 
```

现在，广播公司和观众这边的事情都完成了，让我们发送我们的 ICE 候选人:

```
function on_ice_candidate(event) {
  msg = JSON.stringify({janus: 'trickle', transaction: 'candidate', candidate: event.candidate, session_id: sessionId, handle_id: handleId});
  janusConnection.send(msg);
} 
```

最后，也是最重要的，缺少的是 keepalive。默认情况下，骏利将终止任何在 60 秒内没有发送任何请求的会话。记住，会话和 websockets 是不同的东西。

```
function keepalive() {
    msg = JSON.stringify({janus: 'keepalive', transaction: 'keepalive', session_id: sessionId});
    janusConnection.send(msg);
} 
```

因此，经过这一切，我们有了一个使用骏利:D 广播的工作概念验证

# 项目

首先，依赖性:

*   [二郎](http://www.erlang.org/)(我们和 OTP 21 合作过)。
*   坞站和坞站-组合
*   [Cowboy](https://github.com/ninenines/cowboy) :处理客户端-服务器 websocket 连接。
*   [websocket_client](https://github.com/jeremyong/websocket_client) :处理 server-Janus websocket 连接。
*   [jsx](https://github.com/talentdeficit/jsx) : JSON 解码/编码，我们所有的 websockets 都使用 JSON 进行通信。

让我们首先创建一个负责服务器与骏利通信的模块，`janus_handler.erl` :

```
-module(janus_handler).

-behaviour(websocket_client_handler).

-export([init/2,
         websocket_handle/3,
         websocket_info/3,
         websocket_terminate/3,
         start_link/1]).

start_link(From) ->
  Opts = [{extra_headers, [{<<"Sec-WebSocket-Protocol">>, <<"janus-protocol">>}]}],
  websocket_client:start_link("ws://localhost:8188", ?MODULE, From, Opts).

% Websocket Handler init(From, _ConnState) ->
  {ok, #{from => From}}.

websocket_handle({text, Msg}, _ConnState, State) ->
  DecodedMsg = jsx:decode(Msg, [return_maps]),
  process(DecodedMsg, State).

websocket_info(create_session, _ConnState, State) ->
  Msg = jsx:encode(#{janus => create, transaction => create_session}),
  {reply, {text, Msg}, State};

websocket_info({create_handle, SessionId}, _ConnState, State) ->
  Msg = jsx:encode(#{janus => attach,
                     plugin => <<"janus.plugin.videoroom">>,
                     transaction => create_handle,
                     session_id => SessionId}),
  {reply, {text, Msg}, State};

websocket_info({create_room, SessionId, HandleId}, _ConnState, State) ->
  Msg = jsx:encode(#{janus => message,
                     transaction => create_room,
                     body => #{request => create},
                     session_id => SessionId,
                     handle_id => HandleId}),
  {reply, {text, Msg}, State};

websocket_info({join_publisher, SessionId, HandleId, RoomId}, _ConnState, State) ->
  Body = #{request => join, ptype => publisher, room => RoomId},
  Msg = jsx:encode(#{janus => message,
                     transaction => join_publisher,
                     body => Body,
                     session_id => SessionId,
                     handle_id => HandleId}),
  {reply, {text, Msg}, State};

websocket_info({join_subscriber, SessionId, HandleId, RoomId, FeedId}, _ConnState, State) ->
  Body = #{request => join, ptype => subscriber, room => RoomId, feed => FeedId},
  Msg = jsx:encode(#{janus => message,
                     transaction => join_subscriber,
                     body => Body,
                     session_id => SessionId,
                     handle_id => HandleId}),
  {reply, {text, Msg}, State};

websocket_info({send_offer, SessionId, HandleId, Offer}, _ConnState, State) ->
  Msg = jsx:encode(#{janus => message,
                     transaction => send_offer,
                     body => #{request => publish},
                     jsep => Offer,
                     session_id => SessionId,
                     handle_id => HandleId}),
  {reply, {text, Msg}, State};

websocket_info({send_answer, SessionId, HandleId, Answer}, _ConnState, State) ->
  Msg = jsx:encode(#{janus => message,
                     transaction => send_answer,
                     body => #{request => start},
                     jsep => Answer,
                     session_id => SessionId,
                     handle_id => HandleId}),
  {reply, {text, Msg}, State};

websocket_info({trickle, SessionId, HandleId, Candidate}, _ConnState, State) ->
  Msg = jsx:encode(#{janus => trickle,
                     transaction => trickle,
                     candidate => Candidate,
                     session_id => SessionId,
                     handle_id => HandleId}),
  {reply, {text, Msg}, State};

websocket_info({keepalive, SessionId}, _ConnState, State) ->
  Msg = jsx:encode(#{janus => keepalive,
                     transaction => keepalive,
                     session_id => SessionId}),
  {reply, {text, Msg}, State}.

websocket_terminate(_Reason, _ConnState, _State) ->
  ok.

% Internal process(#{<<"transaction">> := <<"create_session">>,
          <<"data">> := #{<<"id">> := SessionId}}, State) ->
  Msg = #{session_id => SessionId},
  send_msg(Msg, State);
process(#{<<"transaction">> := <<"create_handle">>,
          <<"data">> := #{<<"id">> := HandleId}}, State) ->
  Msg = #{handle_id => HandleId},
  send_msg(Msg, State);
process(#{<<"transaction">> := <<"create_room">>,
          <<"plugindata">> := #{<<"data">> := #{<<"room">> := RoomId}}}, State) ->
  Msg = #{room_id => RoomId},
  send_msg(Msg, State);
process(#{<<"transaction">> := <<"join_publisher">>,
          <<"plugindata">> := #{<<"data">> := #{<<"id">> := PubId}}}, State) ->
  Msg = #{publisher_id => PubId},
  send_msg(Msg, State);
process(#{<<"transaction">> := <<"join_subscriber">>,
          <<"jsep">> := Offer}, State) ->
  Msg = #{jsep => Offer},
  send_msg(Msg, State);
process(#{<<"transaction">> := <<"publish">>,
          <<"jsep">> := Answer}, State) ->
  Msg = #{jsep => Answer},
  send_msg(Msg, State);
process(#{<<"janus">> := <<"ack">>}, State) ->
  {ok, State}.

send_msg(Msg, #{from := From} = State) ->
  From ! {reply, Msg},
  {ok, State}. 
```

您可能想知道存储在`State`中的`From`是什么，它最终将对应于处理客户机-服务器 websocket 连接的进程。这样，我们在两个流程之间建立了一对一的关系，它们可以相互传递请求/响应。

因为我们计划按需建立我们的服务器-骏利连接，所以让我们为它们创建一个主管，并将其添加到我们应用程序的(`janus_erlang_wrapper`)监督树中。

```
-module(janus_erlang_wrapper_sup).

-behaviour(supervisor).

-export([init/1,
         start_link/0]).

-export([]).

start_link() ->
  supervisor:start_link({local, ?MODULE}, ?MODULE, []).

init([]) ->
  JanusSup =
    {janus_sup, {janus_sup, start_link, []},
     permanent, infinity, supervisor, [janus_sup]
    },
  {ok, {{one_for_all, 10, 10}, [JanusSup]}}. 
```

```
-module(janus_sup).

-behaviour(supervisor).

-export([create_janus_connection/0,
         init/1,
         start_link/0]).

start_link() ->
  supervisor:start_link({local, ?MODULE}, ?MODULE, []).

create_janus_connection() ->
  supervisor:start_child(?MODULE, [self()]).

init(_) ->
  JanusHandler =
    {janus_handler, {janus_handler, start_link, []},
     transient, 5000, worker, [janus_handler]
    },
  {ok, {{simple_one_for_one, 10, 10}, [JanusHandler]}}. 
```

太好了！现在，我们可以动态地建立从我们的服务器到骏利的连接，并与之通信。最后一步是创建我们的客户将使用的 API，为此我们为 Cowboy 创建一个 websocket 处理程序:

```
-module(ws_handler).

-export([init/2,
         websocket_handle/2,
         websocket_info/2,
         websocket_init/1]).

init(Req, State) ->
  {cowboy_websocket, Req, State}.

websocket_init(State) ->
  {ok, State}.

websocket_handle({text, Msg}, State) ->
  DecodedMsg = jsx:decode(Msg, [return_maps]),
  process(DecodedMsg, State);
websocket_handle(_, State) ->
  {ok, State}.

websocket_info({reply, Msg}, State) ->
  EncodedMsg = jsx:encode(Msg),
  {reply, {text, EncodedMsg}, State}.

% Internal process(#{<<"request">> := <<"create_session">>}, State) ->
  {ok, JanusWs} = janus_sup:create_janus_connection(),
  JanusWs ! create_session,
  {ok, State#{janus_ws => JanusWs}};

process(#{<<"request">> := <<"create_handle">>,
          <<"session_id">> := SessionId}, #{janus_ws := JanusWs} = State) ->
  JanusWs ! {create_handle, SessionId},
  {ok, State};

process(#{<<"request">> := <<"create_room">>,
          <<"session_id">> := SessionId,
          <<"handle_id">> := HandleId}, #{janus_ws := JanusWs} = State) ->
  JanusWs ! {create_room, SessionId, HandleId},
  {ok, State};

process(#{<<"request">> := <<"join_publisher">>,
          <<"session_id">> := SessionId,
          <<"handle_id">> := HandleId,
          <<"room_id">> := RoomId}, #{janus_ws := JanusWs} = State) ->
  JanusWs ! {join_publisher, SessionId, HandleId, RoomId},
  {ok, State};

process(#{<<"request">> := <<"join_subscriber">>,
          <<"session_id">> := SessionId,
          <<"handle_id">> := HandleId,
          <<"room_id">> := RoomId,
          <<"feed_id">> := FeedId}, #{janus_ws := JanusWs} = State) ->
  JanusWs ! {join_subscriber, SessionId, HandleId, RoomId, FeedId},
  {ok, State};

process(#{<<"request">> := <<"publish">>,
          <<"session_id">> := SessionId,
          <<"handle_id">> := HandleId,
          <<"jsep">> := Offer}, #{janus_ws := JanusWs} = State) ->
  JanusWs ! {send_offer, SessionId, HandleId, Offer},
  {ok, State};

process(#{<<"request">> := <<"listen">>,
          <<"session_id">> := SessionId,
          <<"handle_id">> := HandleId,
          <<"jsep">> := Answer}, #{janus_ws := JanusWs} = State) ->
  JanusWs ! {send_answer, SessionId, HandleId, Answer},
  {ok, State};

process(#{<<"request">> := <<"trickle">>,
          <<"session_id">> := SessionId,
          <<"handle_id">> := HandleId,
          <<"candidate">> := Candidate}, #{janus_ws := JanusWs} = State) ->
  JanusWs ! {trickle, SessionId, HandleId, Candidate},
  {ok, State};

process(#{<<"request">> := <<"keepalive">>,
          <<"session_id">> := SessionId}, #{janus_ws := JanusWs} = State) ->
  JanusWs ! {keepalive, SessionId},
  {ok, State}. 
```

你可能注意到了，我们对每个骏利请求都有一个 API 请求。这样做是为了简单起见，但是您可以很容易地编写一个 API 请求来执行多个 Janus 请求。

## 注意事项

### 复用 websockets

如您所见，我们为每个客户端创建了一个服务器——Janus web socket。在实际使用中，由于必须按需建立 websocket 连接的开销，这可能无法正确扩展，因此我们设计了一个可能的解决方案:通过一组先前创建的 web socket 多路复用多个会话。大概是这样的:

1.  在开始使用工作池时创建一组固定的 server-Janus websockets。
2.  为请求创建一个唯一的 ID，应该标识发送骏利响应的流程。
3.  将请求的唯一 ID 添加到交易值中。
4.  在处理骏利的响应时，解码请求 ID，以确定我们应该将响应发送给哪个流程。

**注意:**会话必须是唯一的，每个客户端一个。

## Docker 内部的问题

我想我还没有提到这一点，但是我们正在 Docker 容器中使用 Janus。通常这不会是一个问题，将它添加到我们的`docker-compose.yml`，暴露端口，就这样。

遗憾的是，事情没有那么简单。STUN 服务器有一个简单的工作:告诉客户端它的公共 IP。这样，如果客户端位于 NAT 之后，并且只知道其私有 IP，它就可以找到其公共 IP。我们使用我们自己的一套 STUN/TURN 服务器(与骏利在同一个 docker-compose 中)，这在骏利创建它的 ICE 候选时成为一个问题，因为骏利和 STUN 在同一个局域网上。因为 STUN 服务器将其公共 IP 作为 Docker containers 网络内部的私有 IP 返回给骏利。如果你不是很懂网络，就知道这个**真的**不好。

谢天谢地，骏利有办法处理这种事情，你可以让它用你选择的 IP 替换所有 ICE 候选上的主机 IP:

*   传递参数`-1 <public IP>`
*   在`janus.jcfg`内`nat`段设置`nat_1_1_mapping = "<public IP>"`

# 结论

太好了！现在我们有了一个使用 WebRTC 的可工作的可伸缩的广播。学到了这么多新东西，有点过度，但我们做到了。:D

您可以在

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿米那里亚](https://github.com/AminArria) / [雅努斯 _ 二郎 _wrapper_demo](https://github.com/AminArria/janus_erlang_wrapper_demo)

### 包装 Janus 的 Erlang 应用程序演示

<article class="markdown-body entry-content container-lg" itemprop="text">

# 演示:Janus 的 Erlang 包装器

这是一个演示 Erlang 应用程序，演示了 Janus 的用法。有关“为什么要进行此演示？”的更多信息我鼓励你阅读我的博客文章[使用 WebRTC](https://dev.to/aminarria/scalable-broadcasting-using-webrtc-2984) 的可伸缩广播。

该演示将公开以下端点:

*   [http://localhost:8080/janus-Client](http://localhost:8080/janus-client)客户端直接连接到 Janus 进行视频广播
*   [http://localhost:8080/API-Client](http://localhost:8080/api-client)客户端连接到我们的 Erlang 应用
*   [http://localhost:8080/websocket](http://localhost:8080/websocket)端点与我们的 Erlang 应用程序建立 web socket 连接。

**注意:**这个演示是用火狐 65 和 Chrome 72 测试的。

## 要求

*   Erlang/OTP(用 Erlang/OTP 21 测试)
*   复合坞站

## 运行演示

第一次转向杰纳斯

```
make ops 
```

启动 Erlang 应用程序

```
make run 
```

去阻止杰纳斯

```
make ops_stop 
```

## 演示

两个客户端`janus-client`和`api-client`呈现相同的接口和使用方式，因此下面解释的一切对两者都适用。

### 作为广播员

1.  开始会话
2.  开始句柄
3.  腾出空间(这将填充`Room ID`字段)
4.  作为发布者加入(这将…

</article>

[View on GitHub](https://github.com/AminArria/janus_erlang_wrapper_demo)