# Vue Bot 场景语法

> 原文：<https://dev.to/razbakov/vue-bot-scenario-syntax-2e14>

## 背景

我正准备写一个聊天机器人，但是我和我的伙伴在回顾场景时遇到了一个问题，而且我也没有找到一个好的 js API 来创建场景。

所以我从一些声明性语法开始:

```
Q: what's your name?
A: input: name

Q: how old are you?
A: 28

(if < 18) do (1) else do (2)

(1)
Q: you have to be 18 years old to see the content

(2)
Q: welcome on board 
```

但后来我问自己，如果我想有更复杂的场景，但仍然希望能够与我的合作伙伴沟通，该怎么办？我想出了下面的 Vue.js 语法。

**我的问题是:**

*   容易阅读和理解吗？
*   有可能实现吗？
*   您在项目中使用它的可能性有多大？

## 好处

*   用户友好 -要求所见即所得编辑器为用户提供友好的用户界面
*   **开发人员友好的** -只需要 Vue.js 中的模板知识
*   **灵活** -基于非常简单的概念，可以通过提供逻辑步骤(是/否条件)和循环(从一个场景跳到另一个场景)来处理复杂的场景

## API

```
type IVariable = Any

// Group set of scenarios in one collection
interface Scenarios {
  id: String
  title: String
}

// Define scenario
interface Scenario {
  id: String
  title: String
}

// Define global variable shared between all scenarios
interface Var {
  v-model: IVariable  // Global variable
  val: IVariable      // Set default value
  userInput: Boolean  // Show UI element to accept user input
  then: String        // Scenario id to execute if value is TRUE
  else: String        // Scenario id to execute if value is FALSE
  request: Promise    // Function which returns promise to deliver value
}

// Add text UI element to chat
interface Text {

}

// Play scenario by id
interface Play {
  id: String
} 
```

## 场景示例

```
<template>
  <Scenarios id="dance-events" title="Dance Events">
    <Scenario id="welcome" title="Welcome">
      <Var v-model="$ConversationsCount" :val="$ConversationsCount || 0"/>
      <Var v-model="$GreetingText" :request="Quotes.getRandom({ type: 'greeting', name: $MyProfile.name })"/>
      <Text>
        {{ $GreetingText }}.
        How can I serve you?
      </Text>

      <Play v-if="$ConversationsCount === 0" id="dance-now" />
      <Var v-else v-model="$Input" userInput @input="Stop()" then="FindScenarios($Input)" />
    </Scenario>

    <Scenario id="dance-now" title="Where can I dance today?">
      <Var v-model="$Type" value="events"/>
      <Var v-model="$MyCity" :value="$MyProfile.currentCity"/>
      <Var v-model="$Date" value="today"/>
      <Var v-model="$MyGenres" :value="$MyProfile.genres"/>

      <Var v-model="$FoundEvents" request="Search($Type, $MyCity, $Date, $MyGenres)" then="events-found" empty="events-notfound"/>
    </Scenario>

    <Scenario id="events-found">
      <Var v-model="$FoundGenres" request="GetAllGenres($FoundEvents)" then="genres-found" empty="genres-notfound"/>
    </Scenario>
    <Scenario id="genres-found">
      <Text>
        What would you like to dance today?
      </Text>
      <Var v-model="$SelectedGenre" userInput :options="$FoundGenres" then="events-results"/>
    </Scenario>
    <Scenario id="events-results">
      <Var v-model="$FilteredEvents" request="Filter($FoundEvents, { genre: $SelectedGenre })"/>
      <Var v-model="$SelectedResultIndex" :val="$SelectedResultIndex || 0"/>
      <Var v-model="$SelectedEvent" :val="$FilteredEvents[$selectedResult]"/>

      <Text>
        There is a {{ $SelectedEvent.name }} near {{ $SelectedEvent.subwayStation }} with {{ $SelectedEvent.guests | count }} guests.
        Wanna go?
      </Text>

      <Event id="$SelectedEvent.id"/>

      <Var v-model="$RSVP" userInput then="rsvp">
        <Else>
          <Var v-model="$SelectedResultIndex" :val="$SelectedResultIndex + 1"/>
          <Play id="events-results" />
        </Else>
      </Var>
    </Scenario>
    <Scenario id="rsvp">
      <Var v-model="$BookingStatus" :request="Event.RSVP($SelectedEvent, 'yes')" then="rsvp-confirmed" else="rsvp-aborted"/>
    </Scenario>
    <Scenario id="rsvp-aborted">
      <Text>
        There was a problem with your RSVP: {{ $BookingStatus.error }}.
        Would you like to try again?
        <Var v-model="$RSVP" userInput then="rsvp">
          <Else>
            <Var v-model="$SelectedResultIndex" :val="$SelectedResultIndex + 1"/>
            <Play id="events-results" />
        </Else>
        </Var>
      </Text>
    </Scenario>
    <Scenario id="rsvp-confirmed">
      <Text>
        Booking is confirmed today at {{ $SelectedEvent.startAt | time }}
        <Play id="end" />
      </Text>
    </Scenario>
  </Scenarios>
</template> 
```

## GitHub 上的明星

正如我在开头提到的，我的问题是:

*   容易阅读和理解吗？
*   有可能实现吗？
*   您在项目中使用它的可能性有多大？

用你的星星激励我- [Vue Bot 场景](https://github.com/razbakov/vue-bot-scenario)

* * *

## 阅读清单

### 概述

*   [https://chatbotsmagazine . com/the-tools-every-bot-creator-must-know-c 0e 9 DD 685094](https://chatbotsmagazine.com/the-tools-every-bot-creator-must-know-c0e9dd685094)
*   [https://medium . com/swlh/what-is-conversation-design-and-how-to-design-your-chatbot-3754 f 04 ab 1e 7](https://medium.com/swlh/what-is-conversation-design-and-how-to-design-your-chatbot-3754f04ab1e7)

### 平台

*   [https://docs . Microsoft . com/en-us/health bot/quick start-createyourfirssscenario](https://docs.microsoft.com/en-us/healthbot/quickstart-createyourfirstscenario)
*   [https://botpress.io/](https://botpress.io/)
*   [https://botsociety.io/](https://botsociety.io/)
*   [https://docs.botstar.com/en/wit.ai.html](https://docs.botstar.com/en/wit.ai.html)
*   [https://botmock.com/](https://botmock.com/)
*   [https://chatbotslife . com/custom-API-ai-chatbot-using-botui-58d 673155 c7d](https://chatbotslife.com/custom-api-ai-chatbot-using-botui-58d673155c7d)

### 谷歌助手

*   [https://www . gupshup . io/developer/docs/bot-platform/guide/how-to-build-a-bot-on-Google-home](https://www.gupshup.io/developer/docs/bot-platform/guide/how-to-build-a-bot-on-google-home)
*   [https://chatbotslife . com/how-to-build-a-app-for-Google-assistant-using-dialog flow-enterprise-edition-and-actions-on-Google-4413 a61 d2f 2d](https://chatbotslife.com/how-to-build-an-app-for-google-assistant-using-dialogflow-enterprise-edition-and-actions-on-google-4413a61d2f2d)
*   [https://dialogflow.com/docs/integrations/google-assistant](https://dialogflow.com/docs/integrations/google-assistant)
*   [https://tutorials.botsfloor.com/tagged/google-assistant](https://tutorials.botsfloor.com/tagged/google-assistant)

### vista . js 模块

*   [https://docs.botui.org/index.html](https://docs.botui.org/index.html)
*   [https://github.com/ajb413/chat-engine-vue](https://github.com/ajb413/chat-engine-vue)
*   [https://github.com/ajb413/vue-chat-engine](https://github.com/ajb413/vue-chat-engine)
*   [https://medium . com/js-Dojo/building-a-chatbot-with-vue-js-chat engine-and-Amazon-lex-67 ea 1a 3352 b 9](https://medium.com/js-dojo/building-a-chatbot-with-vue-js-chatengine-and-amazon-lex-67ea1a3352b9)
*   [https://github . com/Microsoft/bot framework-WebChat/issues/461](https://github.com/Microsoft/BotFramework-WebChat/issues/461)

### 产品

*   [https://reminderbot.io/?ref=madewithvuejs.com](https://reminderbot.io/?ref=madewithvuejs.com)