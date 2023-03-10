# # 100 code examples–只有一个活动协议

> 原文：<https://dev.to/pesse/100codeexamples--only-one-active-protocol-22n2>

有时您会遇到这样的情况，您在数据库中存储了一个实体的几个不同的变体，但是您必须绝对确定一次只有一个变体是活动的。

例如，您可以选择不同的配置或配色方案、会计相关方案中的有效财政年度或默认条目。

死亡之星也有类似的东西，它运行在几种协议中的一种之上。

| 身份证明 | 标签 | 警报级别 | 防御模式 | 功率级别 |
| --- | --- | --- | --- | --- |
| one | 一切顺利吗 | 低的 | 贝尔金 | Eighty |
| Two | 当心 | 中等 | 怀疑 | Ninety |
| three | OMG 叛军！ | 非常高 | 先拍后问 | One hundred and twenty |

为了确保只有一个协议是活动的，数据库开发人员使用大多数关系数据库提供的简单工具:

```
/* We have several protocols for the deathstar
  but its important we only have one active protocol
  at a time
 */
create table deathstar_protocols (
  id integer not null primary key,
  label varchar2(256),
  alert_level varchar2(16) not null,
  defense_mode varchar2(32) not null,
  power_level number(5,2) not null
);

insert into deathstar_protocols
  values (1, 'Everything easy', 'LOW', 'BE_KIND', 80);
insert into deathstar_protocols
  values (2, 'Be careful', 'MEDIUM', 'BE_SUSPICIOUS', 90);
insert into deathstar_protocols
  values (3, 'OMG the rebels!', 'VERY HIGH', 
    'SHOOT_FIRST_ASK_LATER', 120);

select * from deathstar_protocols;

/* To make sure there is only one possibly
  active protocol, we can use basic relational modeling
  in combination with constraints
 */
create table deathstar_protocol_active (
  id integer not null primary key,
  only_one number(1) default 1 not null,
  -- ID is also foreign key
  constraint deathstar_prot_act_fk
    foreign key ( id )
    references deathstar_protocols ( id )
    on delete cascade,
  -- Make sure there can only be one row
  constraint deathstar_prot_act_uq
    unique ( only_one ),
  -- by limiting the possible value of the
  -- helper-column
  constraint deathstar_prot_act_chk
    check ( only_one = 1 )
);

/* This also means the technique is usable in
  every relational database with check-constraints
 */

insert into deathstar_protocol_active ( id ) values (1 );

-- We cannot have more than one active protocol
insert into deathstar_protocol_active ( id ) values ( 2 );

/* We can even have a view which shows
  the active protocol
 */
create view v_deathstar_protocols
  as
  select
    prot.id, label, alert_level, defense_mode, power_level,
    coalesce(active.only_one, 0) is_active
  from
    deathstar_protocols prot
    left outer join deathstar_protocol_active active
      on prot.id = active.id
;

select * from v_deathstar_protocols;

update deathstar_protocol_active set id = 2;

select * from v_deathstar_protocols; 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/hz9qvg02tmm5zj4usuryt3dis) 上运行这个例子，但是它也可以在 SQL Server 和其他所有带检查约束的关系数据库上运行。