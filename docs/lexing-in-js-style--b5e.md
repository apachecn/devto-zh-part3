# JS é£æ ¼ç Lexingð

> åæï¼<https://dev.to/areknawo/lexing-in-js-style--b5e>

**è¿ç¯æç« æèª[æçåå®¢](https://areknawo.com)ï¼æä»¥è¯·å¡å¿æ¥çæ´å¤ææ°åå®¹ð**

è¿ç¯æç« æ¯ AIM é¡¹ç®ç³»åæç« çå»¶ç»­ï¼æä»¥å¦æä½ è¿æ²¡æéè¯»è¿,[ä»¥åçæç« ,](https://areknawo.com/tag/aim),æä¼ç»ä½ ä¸äºç­æ¡ãå*ä¸ºä»ä¹ï¼*æé®ã

å¨æ¬æä¸­ï¼æ¯æ¶åçæ­£å¼å§ç¼å AIM è¯­è¨äºï¼æå°ä»åå»ºä¸ä¸ª **lexer** å¼å§ãä¸ä¸ª**è¯æ³åæå¨**ï¼æèå¦æä½ ä¸åæ¬¢é·åå­- **è®°å·èµäºå¨**ï¼æ¯ä¸ä¸ªå°äººç±»å¯è¯»ææ¬è½¬æ¢æä¸ç³»å**è®°å·**çå·¥å·ï¼ç¨äºä»¥åçå¤çãå®è¢«ç¨äºåå»ºç¼ç¨è¯­è¨ï¼ä¹ç¨äºææ¬å¤çåå¶ä»åç§äºæãæä»¥ï¼è¦æ³¨æçæ¯ï¼è¿ä¸ä»ä»éç¨äºåå»ºç¼ç¨è¯­è¨ãç°å¨ï¼ççè¿éçä¾å­:

```
"128 + 428" 
```

Enter fullscreen mode Exit fullscreen mode

åºæ¬çï¼è¶çº§ç®åçä¸¤ä¸ªæ°ç¸å ãç°å¨è®©æä»¬ççå¦ä½å°å®åæ**ä»¤ç** :
çå½¢å¼

```
["128", "+", "428"] 
```

Enter fullscreen mode Exit fullscreen mode

ä»¤çä¸ä¸å®åªæå­ç¬¦ä¸²ãè¿äºå¯ä»¥æ¯ä¾å¦åå«éå ç**åæ°æ®**ç**å¯¹è±¡**ä»¥ä¾ä»¥åä½¿ç¨ãæ¬æç¨å°å±ç¤ºå¦ä½å®ç°ä¸ä¸ªåºæ¬ç lexer æ¥ä»ä¸é¢çä¸ç§å½¢å¼è½¬æ¢å°å¦ä¸ç§å½¢å¼ã

# å·¥è£

èªç¶ï¼æå¾å¤å¾ä¹¦é¦åå¶ä»æ´å¤§çåä½æ¥å¤çè¿ç±»äºæãæåæ¬¢è¿çåæ¬ **moo** å **lex** ãçè³æå®æ´çå·¥å·åæ¥å¸®å©ä½ åå»º**è¯æ³åæå¨åè§£æå¨**ï¼ä¾å¦ **nearley** å **jison** ãæ­¤å¤ï¼å¯¹äºè¯¥é¢åå¶ä»æ´ä¸ä¸çè¯­è¨(å¦ C/C++)æ¥è¯´ï¼è¿äºåè¡¨å¯è½ä¼é¿å¾å¤ï¼ä½è¿æ¬¡æ¯ JavaScriptï¼æèæ´ç¡®åå°è¯´æ¯ TypeScriptãðå©ç¨è¿äºï¼ä½ å¯ä»¥å¾å®¹æå¾å¿«å°å®æå·¥ä½ãä½æ¯ï¼è¿ä¸æ¯æ¬æç¨åæ´ä¸ª AIM é¡¹ç®çç®çï¼åªæ¯ä½¿ç¨ä¸åçåºãä¸ï¼è¿å°ä»**å¼å§èªå¨æ§è¡**ãç°å¨-è®©æä»¬å¼å§å§ï¼

# å®ä¹

è®©æä»¬é¦åå®ä¹æä»¬ç**è¯æ³åæå¨**åºè¯¥æ¯ä»ä¹æ ·å­ã
**åºè¯¥æ¯**:

*   ä»¥å¯ç§»æ¤åå¯æ©å±çå½¢å¼å®ç°ææçç®æ è¯­æ³ï¼
*   éä¸ªæ è®°å°æ¸è¿æ«æç»å®ææ¬ï¼
*   æè¿­ä»£å¤çè¿çä»¤ççå¥½æ¹æ³ï¼
*   æä¾ä»¤çåå¶åè¡¨çåºæ¬çæ¬çæ¹æ³ã

è¿æ¯éå¸¸åºæ¬çä¸è¥¿ââæ¨åºè¯¥ä»æ­£ç¡®æå»ºç lexer ä¸­æå¾çä¸åãæ¥ä¸æ¥ï¼æä»¬éè¦å³å®å¦ä½åç¡®å°åå»ºæä»¬ç lexerãè¿ç§è½¯ä»¶æ 3 ç§æ åè§£å³æ¹æ¡:

*   éè¿ä½¿ç¨å¤ä¸ªæ­£åè¡¨è¾¾å¼ï¼
*   éè¿ä½¿ç¨åä¸ªæ­£åè¡¨è¾¾å¼ï¼
*   éè¿éå­ç¬¦éè¯»ææ¬ã

è¿éæä»¬éç¨ç¬¬äºç§éæ©ãé¦åï¼å©ç¨æ­£åè¡¨è¾¾å¼å¤çææ¬éå¸¸å®¹æãå®ä»¬è®©æä»¬å¯ä»¥éæ¶éå°è½»æ¾æ©å±æä»¬çè¯­æ³ãæ­¤å¤ï¼å½è¯­æ³éè¦æ¹åæåå±æ¶ï¼éå­ç¬¦éè¯»ææ¬ä¸æ¯æä½³è§£å³æ¹æ¡ãæåï¼å¯¹äºç¬¬ä¸ä¸ªéé¡¹ï¼åä¸ª regexp åºè¯¥å¯ä»¥æä¾æ´å¥½çæ§è½ã

# å±ä»¬ç ï¼

æå³å®å°ä»£ç åæ 3 ä¸ªåºæ¬æä»¶:

*   *grammar.ts* -å®ä¹è¯­æ³ä»¥å¤åç¨çæä»¶ï¼
*   *lexer . ts*ââåºç¡`Lexer`ç­çå°æ¹ï¼
*   *token.ts -* ä¸º`Token`é¶å±åå¤çåºæã

## lexer.ts

æå°ä»å®ä¹`Lexer`ç±»åå¶æ¹æ³å¼å§:

```
import Token from "./token";

export interface GrammarStruct {
  id: string;
  match: string;
}

export default class Lexer {
  private index: number = 0;
  private expr: string = "";
  private regex?: RegExp;
  public tokens: Token[] = [];
  public column: number = 1;
  public line: number = 1;
  public data: string = "";
  public grammar: GrammarStruct[] = [
    {
      id: "newline",
      match: "\\n"
    },
    {
      id: "whitespace",
      match: "\\s"
    }
  ];

  private getRegex() {}
  public loadDefinition(def: GrammarStruct) {}
  public loadGrammar(grammar: GrammarStruct[]) {}
  public loadData(data: string) {}
  public next() {}
  public processAll() {}
  public update() {}
  public empty() {}
} 
```

Enter fullscreen mode Exit fullscreen mode

è®©æä»¬è¿ä¸æ­¥ç ç©¶è¿ä¸ªæ ·æ¿æä»¶ï¼å¹¶å¨åé¢ä»ç»ååºçæ¹æ³çä»£ç ã

å¼å¤´æ è®°äºä¸ä¸ªå¯¼å¥ç**æ è®°**ç±»åå®ä¹ç`GrammarStruct`æ¥å£ï¼ç¨äºæå®åæ è®°å¹éç regexp å®¹å¨åºè¯¥æ¯ä»ä¹æ ·å­ãæ¥ä¸æ¥æ¯`Lexer`ç±»ï¼å®çå±æ§å¾å°ï¼åå­æ¬èº«å°±è¯´æäºä¸åãå¶ä¸­ 3 ä¸ªè¢«æ è®°ä¸º**ç§æ**ï¼å³`index`ã`expr`å`regex`ï¼å ä¸ºè¿äºæ¯ç± lexer å¤ççï¼ä¸åºå¨å®ä¹å¤ä½¿ç¨ãç°å¨ï¼è®©æä»¬ç»§ç»­è®¨è®ºæ¹æ³ã

```
// ...
private getRegex() {
    if (!this.regex) {
      this.regex = new RegExp(this.expr, "gmu");
      console.log(this.regex);
    }
    this.regex.lastIndex = this.index;
    return this.regex;
  }
// ...

12345678910 
```

Enter fullscreen mode Exit fullscreen mode

ç¬¬ä¸ä¸ªåé¨æ¹æ³`getRegex()`ç¨äºä»ä¼ éç`expr`(ä»è¿æ¥ç`GrammarStruct`å¹éå¨çæ)ä¸­çæåä¸ª regexpï¼å¹¶ç¡®ä¿å½ regexp éè¦éæ°çææ¶(å½æ·»å æ°ç`GrammarStruct`)æ­£ç¡®è®¾ç½® lastIndexã

```
// ...
public loadDefinition(def: GrammarStruct) {
    if (this.expr.length > 0) this.expr += "|";
    this.expr += `(${def.match})`;
    this.regex = undefined;
    this.grammar.push(def);

    return this;
}

public loadGrammar(grammar: GrammarStruct[]) {
    for (const def of grammar) {
      this.loadDefinition(def);
    }

    return this;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

`loadDefinition()`å`loadGrammar()`å½æ°è´è´£å è½½`GrammarStruct` sï¼å³å°å®ä»¬ç»åæä¸ä¸ªå¹éè¡¨è¾¾å¼ã`loadDefinition()`å è½½åä¸ª`GrammarStruct`(å¹éå¨å®ä¹)ï¼è`loadGrammar()`å è½½å®ä»¬çæ°ç»(æ´ä¸ªè¯­æ³)ã`this`æ¯ä¸ºäºæ´å®¹æé¾æ¥èè¿åç(ä¹éç¨äºå¶ä»æ¹æ³)ã

```
// ...
public loadData(data: string) {
    this.data += data;

    return this;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

é¡¾åæä¹ââä¸º lexer å è½½æ´å¤æ°æ®ãæ°æ®åªæ¯ä¸ä¸ªå­ç¬¦ä¸²ï¼éå å¨æ´é¿çå­ç¬¦ä¸²åé¢ã

```
// ...
public next() {
    const regex = this.getRegex();
    const match = regex.exec(this.data);
    if (match) {
      const length = match[0].length;
      const token = this.grammar[match.indexOf(match[0], 1) - 1];
      const id = token.id;
      this.index += length;
      this.tokens.push(
        new Token(
          {
            column: this.column,
            line: this.line,
            value: match[0],
            length,
            id
          },
          this
        )
      );
      if (id === "newline") {
        this.column = 1;
        this.line++;
      } else if (id === "whitespace") {
        this.column++;
      } else {
        this.column += length;
      }

      return this.tokens[this.tokens.length - 1];
    }
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

æ¯ä»¥åçä»»ä½ä¸ç§æ¹æ³é½è¦å¤æä¸äºãä½æ¯è¿ä¸ªä¹æ²¡æä»ä¹ç¥å¥çãå®åªæ¯ä½¿ç¨ regexp å¹éæ°æ®ä¸­çä¸ä¸ä¸ªä»¤çï¼å¯¹å¶è¿è¡å¤çï¼å¹¶æ ¹æ®çæçæ°æ®ååè¡¨ä¸­æ·»å æ°ç`Token`ï¼å³å¶**ä½ç½®**ã**é¿åº¦**å **ID** ãå®è¿æ£æ¥ä»»ä½**æ¢è¡ç¬¦**å**ç©ºç½ç¬¦**(å®ä»¬çå¹éç¬¦å¨`Lexer`ä¸­é»è®¤é¢å®ä¹)ï¼å¹¶æ­£ç¡®å¤çå®ä»¬ä»¥è®¡ç®æ¯ä¸ªæ è®°çä½ç½®(è¡å·ååå·)ã

```
// ...
public processAll() {
    for (let i = 0; i < Infinity; i++) {
      const token = this.next();
      if (!token) break;
    }

    return this.tokens;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

`processAll()`åªæ¯ä»`next()`æ¹æ³æ´¾çåºæ¥çãåºæ¬ä¸ï¼å®æåçå°±æ¯å¹éææä¾æ°æ®ä¸­ææå¯è½çæ è®°ï¼ç´å°æ¾ä¸å°æ è®°ï¼ç¶åç«å³è¿åå®ä»¬çå®æ´åè¡¨ã

```
// ...
public update() {
    this.tokens = this.tokens
      .filter(token => {
        return token.value && token.value !== "";
      })
      .sort((a, b) => {
        const line = a.line - b.line;
        const column = a.column - b.column;
        return line === 0 ? column : line;
      })
      .map((token, index, tokens) => {
        if (index > 0) {
          const previous = tokens[index - 1];
          if (previous.id === "newline") {
            return token.moveTo(previous.line + 1, 1, false);
          }
          return token.moveTo(
            previous.line,
            previous.column + previous.length,
            false
          );
        } else {
          return token.moveTo(1, 1, false);
        }
      });

    return this;
  }
// ... 
```

Enter fullscreen mode Exit fullscreen mode

`update()`æ¯æ¸¸æä¸­çå¦ä¸ä¸ªå¤§ç©å®¶ãå®ä»¥ä¸ç§ç®æ´ãå®ç¨çæ¹å¼å¯¹ä»¤çæ°ç»è¿è¡æåºåæåãé¦åï¼æ ¹æ®**ä¸ºç©º** -æ²¡æå¼çä»¤çè¿æ»¤æ°ç»ãæ¥ä¸æ¥ï¼ä»ä»¬æç§åèªçä½ç½®è¿è¡æåºãæåï¼å¯¹æ è®°è¿è¡æ å°ï¼ä½¿å®ä»¬ä»ç¬¬ 1 è¡åç¬¬ 1 åå¼å§æåï¼è¿éè¦æ£æ¥æ¢è¡ç¬¦åç©ºæ ¼ãè¿ä¸ªæ¹æ³å¨å¤§å¤æ°ç`Token`ç±»æ¹æ³ä¸­é½æä½¿ç¨ã

```
// ...
public empty() {
    this.data = "";
    this.line = 1;
    this.column = 1;
    this.index = 0;
    this.tokens = [];

    return this;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

`empty()`æ¹æ³å³é­åè¡¨ãå®ä¼æ¸ç©º`Lexer`çæ°æ®ä»¥ä¾éç¨(è¯­æ³å®ä¹ä»ç¶è¢«å è½½)ã

`Lexer`è¯¾å°æ­¤ç»æï¼è¿å¹¶æ²¡æé£ä¹å¤æââå¦æççå¤æçè¯ï¼ä½ä¸åé½åºè¯¥æ¯è¿æ ·çââä¸ºä»ä¹è¦æè¿ä¹å®¹æè§£å³çäºæåæå¤§é®é¢å¢ï¼å½ç¶ï¼å¯è½ä¼æä¸äºæ¹è¿ï¼ä½åºæ¬ææ³æ¯ä¸æ ·çã

## token.ts

å¨è¿ä¸ªæä»¶ä¸­ï¼å£°æäºæ´ç®åç`Token`ç±»ãåºæ¬ä¸æ¯è¿æ ·ç:

```
import Lexer from "./lexer";

interface TokenData {
  value: string;
  id: string;
  line: number;
  column: number;
  length: number;
}
export default class Token implements TokenData {
  public value: string;
  public id: string;
  public line: number;
  public column: number;
  public length: number;
  private lexer: Lexer;

  public constructor(params: TokenData, ctx: Lexer) {
    this.lexer = ctx;
    this.set(params, false);
  }
  public setValue(newValue: string, update = true) {}
  public moveTo(line?: number, column?: number, update = true) {}
  public moveBy(line?: number, column?: number, update = true) {}
  public set(params: Partial<TokenData>, update = true) {}
  public remove() {}
} 
```

Enter fullscreen mode Exit fullscreen mode

å¨æå¼å§ï¼æä»¬æä¸ä¸ªç¨äºç±»åå®ä¹ç®çç`Lexer`ç±»çå¯¼å¥å`TokenData`æ¥å£çå£°æï¼å®å®ä¹äºåå»ºæ°ä»¤çæéçææå¼ã`Token` class åªä¸è¿æ¯ä¸ä¸ªç®åçåºæ¬æ°æ®æ¶éå¨ï¼å¸¦æä¸äºè¾å©å½æ°ã`Lexer`éè¦ä½ä¸ºæè°ç*ä¸ä¸æ*æ¥ä¼ éï¼ä»¥ä¾¿å¶æ¹æ³ä¸`Token` API è¿è¡åç»­äº¤äºã

```
// ...
public setValue(newValue: string, update = true) {
    this.value = newValue;
    this.length = newValue.length;
    if (update) {
      this.lexer.update();
    }
    return this;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

åå®åºè¯¥åçäºæââè®¾ç½®ä»¤ççå¼åé¿åº¦ãè¿æ¯è®¸å¤æ è®°ç¼è¾æ¹æ³ä¸­çä¸ç§ï¼å¯ä»¥éæ©ç¨äºçææ è®°çåºæ¬çæ¬ãå®çç¬¬äºä¸ªåæ°ï¼é»è®¤å¼ä¸º`true`ï¼æç¤º`Lexer`æ¯å¦åºè¯¥å¨ææå¶ä»ä»»å¡ä¹åè°ç¨`update()`æ¹æ³ã

```
// ...
public moveTo(line?: number, column?: number, update = true) {
    line && (this.line = line);
    column && (this.column = column);
    if (update) {
      this.lexer.update();
    }
    return this;
}

public moveBy(line?: number, column?: number, update = true) {
    line && (this.line += line);
    column && (this.column += column);
    if (update) {
      this.lexer.update();
    }
    return this;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

`moveTo()`å`moveBy()`æ¯ç¨äºéæ°å®ä½å·²ç»å¹éçä»¤ççå®ç¨æ¹æ³ã`moveTo()`å°ä»¤çç§»å¨å°æå®çè¡ååï¼`moveBy()`å°ä»¤çç§»å¨ç»å®çè¡æ°ååæ°ãç§»å¨è¢«æç¤ºåï¼ä»¤çéè¿`Lexer`ç`update()`æ¹æ³å¨æ°ç»ä¸­ç§»å¨ã

```
// ...
public set(params: Partial<TokenData>, update = true) {
    this.value = params.value || this.value;
    this.id = params.id || this.id;
    this.line = params.line || this.line;
    this.column = params.column || this.column;
    this.length = params.length || this.length;
    if (update) {
      this.lexer.update();
    }
    return this;
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

`set()`ç¨äºå¨ä¸æ¬¡è°ç¨ä¸­è®¾ç½®ä¸åçä»¤çå¼ã

```
// ...
public remove() {
    this.value = undefined;
    this.id = undefined;
    this.line = undefined;
    this.column = undefined;
    this.length = undefined;
    this.lexer.update();
 }
 // ... 
```

Enter fullscreen mode Exit fullscreen mode

`remove()`å é¤ææä»¤ççå¼ï¼å¹¶è¿è¡`update()`æ¹æ³ï¼å¶ä¸­ä»¤çç±äºç¼ºå°å¼èè¢«ä»åè¡¨ä¸­è¿æ»¤æã

æä»¥ï¼`Token`ç±»ä¸»è¦æä¾äºä¸äºç¼è¾æ°æ®çæ¹æ³ãå¯è½å¹¶ä¸æ»æ¯éè¦å®ï¼ä½å®æ¯ä¸ä¸ªå¾å¥½çåè½ã

## [T1ãgrammar . ts](#grammarts)

```
import { GrammarStruct } from "./lexer";

const grammar: GrammarStruct[] = [
  // Comments
  {
    id: "single_line_comment_begin",
    match: ">>>"
  },
  {
    id: "multi_line_comment_begin",
    match: ">>"
  },
  {
    id: "multi_line_comment_end",
    match: "<<"
  }
  // ...
]

export default grammar; 
```

Enter fullscreen mode Exit fullscreen mode

å¨ *grammar.ts* æä»¶ä¸­ï¼æä»¬ä¸ºå¯¹è±¡æ°ç»å®ä¹äºæä»¬çè¯­æ³ãæä»¬æä¾`id`ä½ä¸ºå¹éæ è®°ç±»åçæ è¯ç¬¦ï¼å¹¶æä¾`match`ä½ä¸ºå­ç¬¦ä¸²å½¢å¼ç regexpï¼ç¨äºä»¥åçè¿æ¥ãè¿éè¦æ³¨æä¸ç¹ãå ä¸ºæä»¬å®æ´çæ­£åè¡¨è¾¾å¼æ¯ä»¥çº¿æ§æ¹å¼çæçï¼æä»¥å¿é¡»ä¿æ`GrammarStruct`å¹éå¨çæ­£ç¡®é¡ºåºã

# æµ·è´¼ç

ä»¥ä¸ææä»£ç æ¾å¨ä¸èµ·å(ä½ å¯ä»¥å¨ [**AIM** multi-repo](https://github.com/areknawo/AIM) ç **core** åä¸­æ¾å°å®æ´çæºä»£ç )æ¯æ¶åä½¿ç¨è¿ä¸ªåä½äºï¼è¿ä¸åé½å½ç»ä¸ºä¸é¢çä»£ç :

```
import Lexer from "../src/lexer";
import grammar from "../src/grammar";

const AIMLexer = new Lexer().loadGrammar(grammar);
AIMLexer.loadData("public variable #int32 = 1")
AIMLexer.processAll() 
```

Enter fullscreen mode Exit fullscreen mode

ç°å¨ï¼æå¯è½ä¼å¨è¿éç»æè¿ä¸ªæäºï¼ä½è¿æä¸ä¸ªé®é¢ãä½ çï¼lexer åªç¨äºå°çº¿æ§ææ¬å¤çæä¸ä¸ªæ è®°æ°ç»ãè¿æ¯å¦ä¸ä¸ªå·¥å·çå·¥ä½- **è§£æå¨** -ä»¥æ­£ç¡®çæ¹å¼è¯»å/å¤çå®ä»¬ãä¸è¿ä¸ªé®é¢ç¹å«ç¸å³çä¸ä¸ªæ¹é¢æ¯æä»¬è¯­æ³ä¸­ç**å­ç¬¦ä¸²**çå®ç°ãè¿ä¸»è¦æ¯å ä¸ºå¨ AIM ä¸­åå»ºç±»ä¼¼ JS æ¨¡æ¿æå­çæ³æ³ãå¦ä½ç¨ä¸ä¸ª regexp å¹éææçå¯è½æ§ï¼ä¾å¦è½¬ä¹å¼ãå­ç¬¦åå®ä½ç¹ï¼

```
"text\$${value}text"

1 
```

Enter fullscreen mode Exit fullscreen mode

ç®åçåç­å°±æ¯**ä½ ä¸**ãä¹è®¸è§£å³æ¹æ¡å¯¹ä½ ä»¬ä¸­çä¸äºäººæ¥è¯´æ¯æ¾èæè§çï¼ä½å®ç¡®å®éè¦æè¿è¡ä¸äºæ·±å¥çæè(å¾å¯è½æä¸å¤å¼æ)ãä½ å¿é¡»ä¸ä¸ªå­ç¬¦æ¥ä¸ä¸ªå­ç¬¦å°å¤çå­ç¬¦ä¸²**(è³å°è¿æ¯ææ³åºæ¥ç)ãä¾å¦ï¼ççæçè¯­æ³å®ä¹æ°ç»çä¸é¨åã** 

```
[
    // ...
    {
        id: "char",
        match: `(?<=(?:(?:\\b|^)["'\`])|[\\x00-\\x7F])[\\x00-\\x7F](?=(?:[\\x00-\\x7F]+)?["'\`](?:\\b|$))`
    },
    // ...
    // Anchors and brackets
    {
        id: "string_anchor",
        match: "['`\"]"
    }
    // ...
] 
```

Enter fullscreen mode Exit fullscreen mode

ææå­ç¬¦ä¸²åæäºéåå­ç¬¦ãè¿æ ·ï¼å½æå°å®ä¸ä»»ä½ç»å®çå­ç¬¦ä¸²è¿è¡å¹éæ¶ï¼æä¼æ¶å°è®¸å¤ä¸åçæ è®°ï¼è¿äºæ è®°æ`id` of **char** å...é£å®å¨æ²¡é®é¢ï¼ç¨åæå¯ä»¥ç¨è§£æå¨å°å®å¤çææç»çãå¥½ççãå¿«éçå½¢å¼ã

# è¿åªæ¯å¼å§

ç¹å«æ¯ä¸è§£æå¨åç¼è¯å¨ç¸æ¯ï¼lexer åªæ¯å°èä¸ç¢ãä½æ¯æææçè°é¢æ¾å¨æ­£ç¡®çå°æ¹ççå¾éè¦ãåªæåºç¡ç¢åºäºï¼å¡æä¸ä¼åãä¹å°±æ¯è¯´ï¼æè®¤ä¸º lexer çä»£ç å¯è½ä¼åçä¸äºåå(ä¸»è¦æ¯å¨ç¼åè§£æå¨çæ¶å)ï¼ä½ä¸»è¦ææ³å°ä¿æä¸åã

åæ ·ï¼å¦æä½ æ³çå®æ´çä»£ç ï¼å» [**AIM** åè´­](https://github.com/areknawo/AIM)ãå¦æä½ æ³æ´å¯åå°è·è¸ª AIM å¼åçè¿ç¨ï¼å¯ä»¥èè*å¼å§åè´­*æè*å¨ [Twitter](https://twitter.com/areknawo) ä¸å³æ³¨æã*ð¡