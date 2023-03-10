# 为你的 discord.js bot 编写音乐命令(2020 年 3 月更新)

> 原文：<https://dev.to/galnir/how-to-write-a-music-command-using-the-discord-js-library-462f>

11 月 20 日编辑:这个指南已经过时了，因为播放命令代码一直在变。如果你正在寻找一个具有保存播放列表等功能的音乐机器人，我推荐克隆我在 [GitHub](https://github.com/galnir/Master-Bot) 上的机器人

如果你需要一个如何设置机器人的指南，请阅读 *[这个](https://dev.to/galnir/how-to-make-a-discord-music-bot-3c0p)*

Discord 是一款受游戏玩家和非游戏玩家欢迎的语音和聊天应用程序，他们使用它与朋友交流，而不像以前那样需要支付语音服务器的费用。

几乎每个大型服务器都有一个 Discord bot，可以用来管理它，行政命令如 ban 和 kick，gif 和播放音乐的**。既然可以免费为**制作一个音乐机器人，为什么还要花钱买呢？****

 ****没时间没精力看这个指南？只需从 [Github](https://github.com/galnir/Master-Bot) 克隆我的音乐机器人，遵循先决条件部分，点击`npm install`，你就会有一个工作的音乐机器人！

本指南假设你有一个基本的使用突击队的不和谐机器人。如果机器人不使用 discord.js-commando，我*强烈*推荐你阅读[这本指南](https://discordjs.guide/commando/)，因为 commando 让你的生活*变得更加轻松，所以*是这本指南所必需的。

音乐命令的代码可在[这里](https://github.com/galnir/Master-Bot/blob/master/commands/music/play.js)获得。

## 先决条件

确保 [ffmpeg](https://www.ffmpeg.org/download.html) ， [python 2.7](https://www.python.org/downloads/) ，Node( **v12 至少！**)安装完毕。
此处获取 Youtube API 密钥[。](https://developers.google.com/youtube/v3/getting-started)

## 安装软件包

让我们安装将要使用的软件包:
npm:

`npm install discordjs/discord.js discordjs/Commando ffmpeg-static node-opus simple-youtube-api ytdl-core`

纱线:

`yarn add discordjs/discord.js discordjs/Commando ffmpeg-static node-opus simple-youtube-api ytdl-core@latest`

## Index.js(你的主文件)

在我们进入 play.js 之前，我们需要扩展' Guild '类，这样我们就可以添加一个保存歌曲队列的属性。这将允许机器人同时在多个服务器上播放音乐。为了做到这一点，我们将在 index.js 的顶部导入“Structures”，并使用它来扩展“Guild”类:

```
// your index.js should look similar to this:
const { CommandoClient } = require('discord.js-commando');
const { Structures } = require('discord.js');
const path = require('path');
const { prefix, token } = require('./config.json');
// It's vital this is before the initiation of the client
Structures.extend('Guild', Guild => {
  class MusicGuild extends Guild {
    constructor(client, data) {
      super(client, data);
      this.musicData = {
        queue: [],
        isPlaying: false,
        volume: 1,
        songDispatcher: null
      };
    }
  }
  return MusicGuild;
});
const client = new CommandoClient({
  commandPrefix: prefix,
  owner: 'your-discord-user-id',
  unknownCommandResponse: false
});

client.registry
  .registerDefaultTypes()
  .registerGroups([
    ['music', 'Music Command Group']
  ])
  .registerDefaultGroups()
  .registerDefaultCommands()
  .registerCommandsIn(path.join(__dirname, 'commands'));

client.once('ready', () => {
  console.log('Ready!');
});

client.login(token); 
```

Enter fullscreen mode Exit fullscreen mode

## play.js

在你的“命令”文件夹中，创建一个名为 music 的文件夹，并在其中创建 **play.js** 。
我们将从导入包和我们的 Youtube API 密钥开始:

```
const { Command } = require('discord.js-commando');
const { MessageEmbed } = require('discord.js');
const Youtube = require('simple-youtube-api');
const ytdl = require('ytdl-core');
const { youtubeAPI } = require('../../config.json');
const youtube = new Youtube(youtubeAPI); 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们将声明“PlayCommand”类，它扩展了“Command”:

```
module.exports = class PlayCommand extends Command {
  constructor(client) {
    super(client, {
      name: 'play', 
      memberName: 'play',
      group: 'music', // this means the folder the file is inside
      description: 'Play any song or playlist from youtube',
      guildOnly: true, // make this command available only in servers not dm's
      clientPermissions: ['SPEAK', 'CONNECT'],
      args: [
        {
          key: 'query', // here we name the variable that will hold the input
          prompt: 'What song would you like to listen to?', // send this msg if
          // the user hasn't provided any arg or if the arg was not a string
          type: 'string',
          validate: query => query.length > 0 && query.length < 200 
        }
      ]
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

每个命令都以 run 方法开始(使用命令时希望机器人运行的代码):

```
 async run(message, { query }) {
    // don't let users run this command if they are not in a voice channel
    var voiceChannel = message.member.voice.channel;
    if (!voiceChannel) return message.say('Join a channel and try again'); 
```

Enter fullscreen mode Exit fullscreen mode

运行此命令时，用户有 3 个选项:

1.  用一个歌名运行它
2.  使用 Youtube URL(任何类型的 URL)运行它
3.  用 Youtube 播放列表 URL 运行它

例如:

```
!play Darude Sandstorm
!play https://www.youtube.com/watch?v=y6120QOlsfU (and other url kinds)
!play https://www.youtube.com/playlist?list=PLuUrokoVSxlfUJuJB_D8j_wsFR4exaEmy 
```

Enter fullscreen mode Exit fullscreen mode

为了做到这一点，我们将编写一个 if 语句来检查任何类型的 Youtube URL。如果输入匹配正则表达式，我们将应用不同的逻辑，而不是按歌曲名称查询。

首先检查*查询*是否是一个播放列表 URL:

```
 if (
      query.match(
        /^(?!.*\?.*\bv=)https:\/\/www\.youtube\.com\/.*\?.*\blist=.*$/
      )
    ) {
      try {
        const playlist = await youtube.getPlaylist(query); // get playlist data 
        const videosObj = await playlist.getVideos(); // songs data object
        //const videos = Object.entries(videosObj); // turn the object to array
        // iterate through the videos array and make a song object out of each vid
        for (let i = 0; i < videosObj.length; i++) { 
          const video = await videosObj[i].fetch();

          const url = `https://www.youtube.com/watch?v=${video.raw.id}`;
          const title = video.raw.snippet.title;
          let duration = this.formatDuration(video.duration);
          const thumbnail = video.thumbnails.high.url;
          if (duration == '00:00') duration = 'Live Stream';
          const song = {
            url,
            title,
            duration,
            thumbnail,
            voiceChannel
          };

          message.guild.musicData.queue.push(song); // if you remember, the queue lives in the guild object so each server has its own queue

        }
        if (message.guild.musicData.isPlaying == false) { // if nothing is playing
          message.guild.musicData.isPlaying = true;
          return this.playSong(message.guild.musicData.queue, message); // play the playlist
        } else if (message.guild.musicData.isPlaying == true) { // if something is already playing
          return message.say(
            `Playlist - :musical_note: ${playlist.title} :musical_note: has been added to queue`
          );
        }
      } catch (err) {
        console.error(err);
        return message.say('Playlist is either private or it does not exist');
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

Youtube URL 正则表达式:

```
 if (query.match(/^(http(s)?:\/\/)?((w){3}.)?youtu(be|.be)?(\.com)?\/.+/)) {
      const url = query; // temp variable
      try {
        query = query
          .replace(/(>|<)/gi, '')
          .split(/(vi\/|v=|\/v\/|youtu\.be\/|\/embed\/)/);
        const id = query[2].split(/[^0-9a-z_\-]/i)[0];
        const video = await youtube.getVideoByID(id);
        const title = video.title;
        let duration = this.formatDuration(video.duration);
        const thumbnail = video.thumbnails.high.url;
        if (duration == '00:00') duration = 'Live Stream';
        const song = {
          url,
          title,
          duration,
          thumbnail,
          voiceChannel
        };
        message.guild.musicData.queue.push(song);
        if (
          message.guild.musicData.isPlaying == false ||
          typeof message.guild.musicData.isPlaying == 'undefined'
        ) {
          message.guild.musicData.isPlaying = true;
          return this.playSong(message.guild.musicData.queue, message);
        } else if (message.guild.musicData.isPlaying == true) {
          return message.say(`${song.title} added to queue`);
        }
      } catch (err) {
        console.error(err);
        return message.say('Something went wrong, please try again later');
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果用户输入了歌曲名作为参数:

```
 try {
      // search for the song and get 5 results back
      const videos = await youtube.searchVideos(query, 5);
      if (videos.length < 5) {
        return message.say(
          `I had some trouble finding what you were looking for, please try again or be more specific`
        );
      }
      const vidNameArr = [];
      // create an array that contains the result titles
      for (let i = 0; i < videos.length; i++) {
        vidNameArr.push(`${i + 1}: ${videos[i].title}`);
      }
      vidNameArr.push('exit'); // push 'exit' string as it will be an option
      // create and display an embed which will present the user the 5 results
      // so he can choose his desired result
      const embed = new MessageEmbed()
        .setColor('#e9f931')
        .setTitle('Choose a song by commenting a number between 1 and 5')
        .addField('Song 1', vidNameArr[0])
        .addField('Song 2', vidNameArr[1])
        .addField('Song 3', vidNameArr[2])
        .addField('Song 4', vidNameArr[3])
        .addField('Song 5', vidNameArr[4])
        .addField('Exit', 'exit'); // user can reply with 'exit' if none matches
      var songEmbed = await message.say({ embed });
      try {
        // wait 1 minute for the user's response
        var response = await message.channel.awaitMessages(
          msg => (msg.content > 0 && msg.content < 6) || msg.content === 'exit',
          {
            max: 1,
            maxProcessed: 1,
            time: 60000,
            errors: ['time']
          }
        );
        // assign videoIndex to user's response
        var videoIndex = parseInt(response.first().content);
      } catch (err) {
        console.error(err);
        songEmbed.delete();
        return message.say(
          'Please try again and enter a number between 1 and 5 or exit'
        );
      }
      // if the user responded with 'exit', cancel the command
      if (response.first().content === 'exit') return songEmbed.delete();
      try {
        // get video data from the API
        var video = await youtube.getVideoByID(videos[videoIndex - 1].id);
      } catch (err) {
        console.error(err);
        songEmbed.delete();
        return message.say(
          'An error has occured when trying to get the video ID from youtube'
        );
      }
      const url = `https://www.youtube.com/watch?v=${video.raw.id}`;
      const title = video.title;
      let duration = this.formatDuration(video.duration);
      const thumbnail = video.thumbnails.high.url;
        if (duration == '00:00') duration = 'Live Stream';
        const song = {
          url,
          title,
          duration,
          thumbnail,
          voiceChannel
        };

        message.guild.musicData.queue.push(song);

        if (message.guild.musicData.isPlaying == false) {
          message.guild.musicData.isPlaying = true;
          songEmbed.delete(); // delete the selection embed
          this.playSong(message.guild.musicData.queue, message);
        } else if (message.guild.musicData.isPlaying == true) {
          songEmbed.delete();
          // add the song to queue if one is already playing
          return message.say(`${song.title} added to queue`);
        }
    } catch (err) {
      // if something went wrong when calling the api:
      console.error(err);
      if (songEmbed) {
        songEmbed.delete();
      }
      return message.say(
        'Something went wrong with searching the video you requested :('
      );
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

那么我们上面多次调用的那个 *playSong* 函数是什么呢？该函数将队列和消息对象作为参数。当被调用时，它告诉机器人加入用户的频道并开始播放音乐！

```
// this is inside the PlayCommand class
  playSong(queue, message) {
    let voiceChannel;
    queue[0].voiceChannel
      .join() // join the user's voice channel
      .then(connection => {
        const dispatcher = connection
          .play(
            ytdl(queue[0].url, { // pass the url to .ytdl()
              quality: 'highestaudio',
              // download part of the song before playing it
              // helps reduces stuttering
              highWaterMark: 1024 * 1024 * 10
            })
          )
          .on('start', () => {
            // the following line is essential to other commands like skip
            message.guild.musicData.songDispatcher = dispatcher;
            dispatcher.setVolume(message.guild.musicData.volume);
            voiceChannel = queue[0].voiceChannel;
            // display the current playing song as a nice little embed
            const videoEmbed = new MessageEmbed()
              .setThumbnail(queue[0].thumbnail) // song thumbnail
              .setColor('#e9f931')
              .addField('Now Playing:', queue[0].title)
              .addField('Duration:', queue[0].duration);
            // also display next song title, if there is one in queue
            if (queue[1]) videoEmbed.addField('Next Song:', queue[1].title);
            message.say(videoEmbed); // send the embed to chat
            return queue.shift(); //  dequeue the song
          })
          .on('finish', () => { // this event fires when the song has ended
            if (queue.length >= 1) { // if there are more songs in queue
              return this.playSong(queue, message); // continue playing
            } else { // else if there are no more songs in queue
              message.guild.musicData.isPlaying = false;
              return voiceChannel.leave(); // leave the voice channel
            }
          })
          .on('error', e => {
            message.say('Cannot play song');
            message.guild.musicData.queue.length = 0;
            message.guild.musicData.isPlaying = false;
            message.guild.musicData.nowPlaying = null;
            console.error(e);
            return voiceChannel.leave();
          });
      })
      .catch(e => {
        console.error(e);
        return voiceChannel.leave();
      });
  } 
```

Enter fullscreen mode Exit fullscreen mode

格式持续时间函数:

```
 formatDuration(durationObj) {
    const duration = `${durationObj.hours ? durationObj.hours + ':' : ''}${
      durationObj.minutes ? durationObj.minutes : '00'
    }:${
      durationObj.seconds < 10
        ? '0' + durationObj.seconds
        : durationObj.seconds
        ? durationObj.seconds
        : '00'
    }`;
    return duration;
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 就是这样！

你可以在机器人的 [repo](https://github.com/galnir/Master-Bot/tree/master/commands/music) 上查看其他音乐命令

如果你遇到了问题，要么在下面评论，要么在机器人的 GitHub 库中打开一个问题。

我还写了一个关于编写音乐问答(琐事)命令的指南，你可以在这里查看一下****