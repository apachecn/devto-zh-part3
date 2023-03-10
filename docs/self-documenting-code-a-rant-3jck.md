# 自我记录代码[咆哮]

> 原文：<https://dev.to/hammerdr/self-documenting-code-a-rant-3jck>

我将从一个例子开始。这种特殊的互动是我职业生涯中在多种情况下都遇到过的。当谈到注释、分解良好的代码、干净的代码或类似的东西时，可能会有人提出异议

> 自记录代码是一个神话。主张自文档化代码的开发人员只是懒惰，不想做保持代码可维护性的必要工作。

这个评论是我咆哮的来源。

## 无法评论？

没有注释是*而不是*自文档化代码的定义特征。相反，自文档化代码是一种用干净、简单的源材料消除了其他必要注释的代码。这有许多好处:

*   它减少了代码的总行数(包括注释)。
*   它减少了分散在多条线上的知识，而一条线就够了。
*   它减少了阅读器的内存开销。
*   它减少了由于位腐败而导致的注释和代码不一致的机会。

所有这些都有助于更好地理解代码，意味着随着时间的推移会有更快更好的变化。

## 丑陋、无证的代码

举个说明性的例子，让我们写下一些我可能在大学里写过的代码。

```
class Animal {
  public int kills(bool pred) {
    int r = 0;
    for(int i = 0; i < k.size(); i++) {
      if(new Boolean(k[i][0].get('kill')) && (!pred || k[i][0].get('type') == 0) {
        r += 1;
      }
    }
    return r;
  }
} 
```

这个丑陋的、没有文档记录的代码当然可以通过一些注释来改进。我们来补充一下。

```
class Animal {
  // gets the number of other animal kills
  // 
  // Boolean pred if true, return only predator kills
  public int kills(Boolean pred) {
    // k is the instance variable for all kills, plants and animals
    // the resulting number of kills
    int r = 0;
    for(int i = 0; i < k.size(); i++) {
      if(
       // is this interaction an animal kill?
       new Boolean(k[i][0].get('kill')) && 
       // are we not search for just predator kills?
       // or is this an animal kill type?
       (!pred || k[i][0].get('type') == 0)
      ) {
        r += 1;
      }
    }
    return r;
  }
} 
```

我认为很难说，在这种情况下，注释是不必要的(假设代码没有变化)。事实上，如果我看着一团我不理解的代码，我会经常做这种风格的注释，在我们进行的过程中把我的理解编码到代码中。

## 大工具

创建自文档化的代码有很多层，但是你可以用两个工具覆盖大约 95%的变更:重命名变量/方法和提取方法。让我们在这里应用每一个，留下评论。对于这个练习，我将添加一个`/* Refactoring: Comment */`来解释我所做的事情。

```
class Animal {
  // gets the number of other animal kills
  // 
  // Boolean pred if true, return only predator kills
  /* Rename: kills -> getAnimalKills for convention following and clarity */
  /* Rename: pred -> onlyIncludePredatorKills for clarity */
  public int getAnimalKills(Boolean onlyIncludePredatorKills) {
    // k is the instance variable for all kills, plants and animals
    /* Rename: k -> allPlantAndAnimalKills for clarity */
    // the resulting number of kills
    /* Rename: r -> result for clarity */
    int result = 0;
    for(int i = 0; i < allPlantAndAnimalKills.size(); i++) {
      if(
       // is this interaction an animal kill?
       /* Extract Method: isAnimalKillAt(i) for clarity */
       isAnimalKillAt(i) &&
       // are we not search for just predator kills?
       // or is this an animal kill type?
       /* Extract Method: isPredatorKillAt(i) for clarity */
       (!onlyIncludePredatorKills || isPredatorKillAt(i))
      ) {
        result += 1;
      }
    }
    return result;
  }

  private Boolean isAnimalKillAt(int index) {
    return new Boolean(allPlantAndAnimalKills[index][0].get('kill'));
  }

  private Boolean isPredatorKillAt(int index) {
    return allPlantAndAnimalKills[index][0].get('type') == 0;
  }
} 
```

现在，让我们删除所有的评论，看看情况如何。

```
class Animal {
  public int getAnimalKills(Boolean onlyIncludePredatorKills) {
    int result = 0;
    for(int i = 0; i < allPlantAndAnimalKills.size(); i++) {
      if(isAnimalKillAt(i) && (!onlyIncludePredatorKills || isPredatorKillAt(i))) {
        result += 1;
      }
    }
    return result;
  }

  private Boolean isAnimalKillAt(int index) {
    return new Boolean(allPlantAndAnimalKills[index][0].get('kill'));
  }

  private Boolean isPredatorKillAt(int index) {
    return allPlantAndAnimalKills[index][0].get('type') == 0;
  }
} 
```

这里，我们有一个现在“自我记录”的方法但是，这段代码不一定是干净的。我会继续问的问题:

*   为什么 allPlantAndAnimalKills 数据结构中会有一个幻数 0？
*   为什么 type 是整数？
*   为什么 kill == true 表示是动物杀死的？

这些问题可能会通过进一步的重构得到解决。例如，也许我们只是将`'kill'`键重命名为`'animalKill'`。但是，其他问题可能成本太高，无法立即解决，或者可能有复杂的原因需要解决。这些问题的答案就是注释有用的地方。

## 注释是解释原因

我们用注释来解释为什么有些事情不寻常。或者非常复杂的东西。这些解释性注释应该回答这样一个问题:为什么代码是以这种特殊的方式编写的？可能会有一些有用的“这是什么？”文字。但是，对我来说，为什么更重要。让我们最后一次使用我们的例子。

```
class Animal {
  /*
   * This data structure represents kills within the ecosystem
   * that belong to this animal.
   *
   * [
   *   // kills array
   *   [
   *     { 'kill' => 0, 'type' => 0, 'x' => 0, 'y' => 0 },
   *   ],
   *   // metadata array
   *   [
   *     { 'killsInDesert' => 0, 'killsInSea' => 0 }
   *   ]
   * ]
   *
   * This is a very complicated data structure that undergirds
   * our entire system. We've extracted most of the data out of
   * this into better typed data structures, but we have not yet
   * eliminated this for all use cases.
   *
   */
  private List<List<HashMap<String, Integer>>> allPlantAndAnimalKills;

} 
```

这里，注释做了代码没有做的事情。它描述了代码作者/编辑的动机。他们希望这种复杂的数据结构消失，但还没有能够把它推到边缘。对于第一次接触代码的新开发人员来说，这可能是一种催化剂，他们需要投资于摆脱整个糟糕的数据结构，以支持更好的东西。

注意还有一个`what`。由于类型系统滥用，这个`what`在这种情况下是有价值的。理论上，你可以将所有的信息编码到某种伪类型的系统中，只在这种数据结构上运行。我不会在这里讲太多细节，只是想说，即使在这种非常复杂的情况下，也有可能去掉`what`。但是，没有任何代码能够理解为什么？