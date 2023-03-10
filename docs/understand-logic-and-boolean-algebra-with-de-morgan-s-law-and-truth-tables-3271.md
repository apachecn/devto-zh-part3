# 用德摩根定律和真值表理解逻辑和布尔代数

> 原文：<https://dev.to/obbap/understand-logic-and-boolean-algebra-with-de-morgan-s-law-and-truth-tables-3271>

# 简介

有了德摩根定律和真值表，我们将能够简化逻辑表达式和模型，找到可能性甚至 bug。这些过程帮助我们组织、简化，甚至几乎可以想象事情是如何运作的。真值表在设计逻辑电路和逻辑门时也很有用。让我们开始吧。

# 德·摩根定律

> 两个集合并的补集是它们补集的交，两个集合交的补集是它们补集的并。

我知道这是一大堆胡言乱语，但是我个人通过这个例子理解了这一点

[![Image for De Morgan](img/9c6d13c4bfb4773a8df938d5794efdf2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eiKvWQkQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/pbaba/image/upload/v1555733838/set_m4qev9.jpg)

如果 **U** 是{1，2，3，4，5，6}， **A** 是{2，3}， **B** 是{3，4，5}

```
//The union of A and B
A u B = {2,3,4,5}

// What the universal set contains and (A u B) doesn't
(A u B)' = {1,6}

// What the universal set contains and A doesn't
A' = {1,4,5,6} 

//What the universal set contains and B doesn't
B' = {1,2,6}

//The intersection of the complements of A and B
A' n B' = {1,6} 

A u B = A' n B' 
```

在英语中，不可能同时是夏天和冬天，所以要么不是夏天，要么不是冬天。只有在不是夏天或冬天的情况下，才不是夏天也不是冬天。按照这种推理，and 可以转换成 ORs，反之亦然

这基本上意味着

```
/*it cannot be summer and winter at once also 
means it is either, not summer or not winter, 
using the template that its either summer or winter that are available*/

!(Summer AND Winter) = !Summer OR !Winter

/*If its either not summer and not winter, that means it can't be summer or winter*/

!Summer AND !Winter = !(Summer OR Winter) 
```

# 真值表

我们可以使用真值表来分析我们的模型所依赖的内部变量。这些行代表变量的可能状态或组合。每个变量有两种可能的结果，所以我们使用 2 ^ n 公式，其中 n 是变量的数量。每个结果可以是 ***真*** 或 ***假*** 。

# 用例

```
class User {
  constructor(firstname, lastname, isValidated, rateCount,isBlocked){
    this.firstname = firstname;
    this.lastname = lastname;
    this.isValidated = isValidated;
    this.rateCount = rateCount;
    this.isBlocked = isBlocked;
  }

  writeToFile() {
    if(!this.isBlocked && this.rateCount < 10 && this.isValidated ){
      console.log('User is writing...');
      this.addToRate();
    }else console.log(`${this.firstname}  ${this.lastname} you have issues`)

  }

  addToRate() {
    this.rateCount ++;
  }

  get rate(){
    return this.rateCount;
  }

} 
```

这是一个向经过身份验证的用户授予写权限的系统，如果用户尝试使用未经验证的电子邮件地址写入系统，或者在超过 10 次提交的限制后尝试写入系统，该系统会阻止用户。

***利用德·摩根定律***

我们希望分析导致用户被阻止的逻辑路径或过程。

a:未经验证的用户
B:写入系统
C:超过速率限制(10)
D:用户被阻止

```
/*If an Unvalidated user writes to the system or if a validated user exceeds the limit, the user gets blocked.*/
(A AND B) OR (B AND C) -> D

//We can factorize using distributivity
B AND (A OR C) -> D

//The user remains unblocked at !D
!D -> !(B AND (A OR C)) // The law of contrapositivity

//Using DeMorgan's law (!(A AND B) = !A OR !B)
!D -> !B OR !(A OR C)

//Using DeMorgan's law again
!D -> !B OR (!A AND !C) 
```

最后一个表达式告诉我们，如果用户没有写入系统，或者如果他通过了验证并且没有超过限制，那么他不会被阻止。

***使用真值表***

如果我们必须创建一个具有以下需求的系统

*   如果用户没有验证他/她的电子邮件，他/她只有读取权限。
*   未经验证的用户不能拥有写权限
*   用户具有读取或写入权限。

a:未验证的用户
B:读取权限
C:写入权限

1.  A - > B(未验证的用户只有读权限)这个语句只有在输出(B)为真，或者输出(A 和 B)都为假时才成立。
2.  ！(A 和 C)(未经验证的用户不能拥有写权限)
3.  b 或 C(用户拥有读或写权限)

由于我们有三个变量，我们将有 8 个可能的结果(2 ^ 3)对或错的每个变量。然后，我们用上面的三个语句测试这些可能的结果。

| A | B | C | one | Two | three | 总数 |
| --- | --- | --- | --- | --- | --- | --- |
| T | T | T | T | F | T | F |
| T | T | F | T | T | T | T |
| T | F | T | F | F | T | F |
| T | F | F | F | T | F | F |
| F | F | F | T | T | F | F |
| F | F | T | T | T | T | T |
| F | T | T | T | T | T | T |
| F | T | F | T | T | T | T |

所以我们可以看到，只有当至少一个变量为假，或者至少一个变量为真时，我们才有真值。它们不可能全是假的，也不可能全是真的。这是有道理的，你不能被无效，仍然要写权限。

# 结论

分析逻辑模型还有很多其他的规则和法则，这只是其中的一部分，我偶然发现了它，我喜欢它，所以我决定分享一下。感谢阅读！🌹 🌹

最初写于[我的博客](https://paschal.dev/understand-logic-and-boolean-algebra-with-de-morgan-s-law-and-truth-tables)

# 参考文献

1.  Wladston Ferreira Filho 提炼的计算机科学
2.  [http://www.ask-math.com/de-morgans-law.html](http://www.ask-math.com/de-morgans-law.html)