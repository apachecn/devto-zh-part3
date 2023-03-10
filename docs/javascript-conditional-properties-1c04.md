# Javascript 条件属性

> 原文：<https://dev.to/micahriggan/javascript-conditional-properties-1c04>

您是否知道可以有条件地将属性添加到带有 spread 的对象中？

所以代替

```
function getUser(banned) {
  const userStatus = {banned, banReason: 'Wizard'};
  // only combine the user status if the user is banned
  const user = { name: 'Micah' };
  if(userStatus.banned) {
    Object.assign(user, userStatus);
  }
   return user;
}

console.log(getUser(true));
console.log(getUser(false)); 
```

你可以做

```
function getUser(banned) {
  const userStatus = {banned, banReason: 'Wizard'};
  // only combine the user status if the user is banned
   return {
    name: 'Micah',
    ...userStatus.banned && userStatus,
  }
}

console.log(getUser(true));
console.log(getUser(false)); 
```

## 输出

```
{ name: 'Micah', banned: true, banReason: 'Wizard' }
{ name: 'Micah' } 
```

整洁！