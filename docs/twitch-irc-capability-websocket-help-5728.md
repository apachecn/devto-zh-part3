# Twitch IRC 功能 WebSocket(帮助)

> 原文：<https://dev.to/zaekof/twitch-irc-capability-websocket-help-5728>

我试图得到一个事件，当一个版主在聊天中解禁一个用户，我发送了一个 twitch.tv/tags 的请求，但当用户被禁或解禁时，我没有收到任何消息...

这是我的真实代码:

```
const WebSocket = require('ws');

const ws = new WebSocket('wss://irc-ws.chat.twitch.tv:443');

ws.onopen = function(event) {
  console.log('INFO: Socket Opened'); 
  ws.send('CAP REQ :twitch.tv/commands');
  ws.send('CAP REQ :twitch.tv/membership');
  ws.send('CAP REQ :twitch.tv/tags');
  ws.send('PASS oauth:...');
  ws.send('NICK zaekof_');
  ws.send('JOIN #zaekof_');
  ws.send('USER #zaekof_');
};
ws.onerror = function(error) {
  console.log('ERR: ', error);
};
ws.onclose = function() {
  console.log('INFO: Socket Closed');
}; 
ws.onmessage = function(event) {
  console.log('RECV: ', event.data);
}; 
```

Enter fullscreen mode Exit fullscreen mode

当我连接到 irc 聊天时，我会收到以下消息:

```
RECV:  :tmi.twitch.tv CAP * ACK :twitch.tv/commands
RECV:  :tmi.twitch.tv CAP * ACK :twitch.tv/membership
RECV:  :tmi.twitch.tv CAP * ACK :twitch.tv/tags

RECV:  :tmi.twitch.tv 001 zaekof_ :Welcome, GLHF!
:tmi.twitch.tv 002 zaekof_ :Your host is tmi.twitch.tv
:tmi.twitch.tv 003 zaekof_ :This server is rather new
:tmi.twitch.tv 004 zaekof_ :-
:tmi.twitch.tv 375 zaekof_ :-
:tmi.twitch.tv 372 zaekof_ :You are in a maze of twisty passages, all alike.:tmi.twitch.tv 376 zaekof_ :> 
```

Enter fullscreen mode Exit fullscreen mode

有人有办法帮我吗？

谢了。