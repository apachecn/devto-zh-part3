# 角反应形式中的嵌套形式

> 原文：<https://dev.to/crazedvic/nested-formarrays-in-angular-reactiveforms--4hm>

2019 年 2 月 7 日

# 角形反应形式有大量嵌套

我到处寻找一个我可以复制粘贴到我的项目中的解决方案，但是运气不好，所以这里是我为下一个陷入我这种情况的可怜人提供的尝试。我已经尽力让代码尽可能简单。

# 你有联赛

```
league_details: this.fb.group({
        name: "",
        founder: ""
      }) 
```

# 一个联赛有球队

```
this.leagueForm = this.fb.group({
      league_details: this.fb.group({
        name: "",
        founder: ""
      }),
      teams: this.fb.array([this.teams])
    }); 
```

# 球队有名字和球员

```
get teams(): FormGroup {
    return this.fb.group({
      team_name: "",
      players: this.fb.array([this.players])
    });
  } 
```

# 玩家有名字和号码

```
get players(): FormGroup {
    return this.fb.group({
      player_name: "",
      player_number: ""
    });
  } 
```

# 你有一个表单组

```
<form [formGroup]="leagueForm"> 
```

# 显示联赛详情

```
<div class="form-header" formGroupName="league_details">
    <label>League Name <input formControlName="name"/></label>
    <label>Founder <input formControlName="founder"/></label>
  </div> 
```

# 显示团队

有一个定义 formArrayName 的包装 div 是至关重要的！

```
<div formArrayName="teams">
    <div class="teams" *ngFor="let team of leagueForm.get('teams').controls;
             let teamIndex = index" [formGroupName]="teamIndex">
      <label>Team Name <input formControlName="team_name"/></label>
      <!--  div with formArrayName='players' goes here (see below) -->
    </div>
</div> 
```

# 显示队伍内的玩家

同样重要的是，您有一个带有 formArrayName 的包装 div。此外，你需要在团队环境中添加球员。注意，在 ngFor 中，我引用了在上面的 ngFor 循环中为团队定义的团队变量
。我也需要参考这个团队时，删除或添加球员。

还要注意`<span>`元素，这是您引用字段进行验证功能的方式。

```
<div formArrayName="players">
    <div class="players" *ngFor="let player of team.get('players').controls;
        let playerIndex=index" [formGroupName]="playerIndex">
        <label>Player Name <input formControlName="player_name" />
        <label>Player Number <input formControlName="player_number"/></label>
        <span *ngIf="player.get('player_number').touched">touched!</span>
    </div>
</div> 
```

# 添加团队

将 ADD 按钮放在定义 formArrayArrayName 团队的 div 上方

```
 <button type="button" (click)="addTeam()">Add Team</button> 
```

```
addTeam() {
    (this.leagueForm.get("teams") as FormArray).push(this.teams);
  } 
```

# 添加玩家

将 ADD 按钮放在定义 formArrayName players 的 div 上方，并记住从封闭的 ngFor 循环中传入 team 变量。

```
<button type="button" (click)="addPlayer(team)">Add Player</button> 
```

```
addPlayer(team) {
    team.get("players").push(this.players);
  } 
```

在 github 上看项目:
[https://github.com/crazedVic/angularnestedformarrays](https://github.com/crazedVic/angularnestedformarrays)