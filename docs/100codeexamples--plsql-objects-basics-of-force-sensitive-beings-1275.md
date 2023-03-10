# # 100 code examples–PL/SQL 对象:力敏感生物的基础

> 原文：<https://dev.to/pesse/100codeexamples--plsql-objects-basics-of-force-sensitive-beings-1275>

由于 utPLSQL，我不久前开始钻研 PL/SQL 中的面向对象编程，同时在我的正常工作中也使用过几次。

PL/SQL 语言的这一强大特性没有得到充分利用，因此我想围绕它创建一些例子，希望展示 PL/SQL 中的对象所具有的一些优势。

假设我们想为对原力敏感的生物实现一些行为，当然，他们最突出的代表是绝地和西斯:

```
-- First create a base type
create or replace type force_sensitive force as object
(
  -- First we define object-level attributes
  c_name varchar2(100),
  -- notice the comma instead of semicolon:
  -- this is a definition, similar to table definition - not
  -- an implemenntation.

  -- define a function we are going to implement
  member function name
    return varchar2,

  -- and one we are only going to implement
  -- inside the children
  not instantiable member function alignment
    return varchar2
)
-- For this is our base type we dont want it to be
-- instantiable, e.g. no "new force_sensitive()" possible
not final not instantiable;
/

-- Now implement the body of the base type
create or replace type body force_sensitive as
  member function name
    return varchar2
  as
    begin
      return c_name;
    end;
end;
/

-- We want to have a real type now
create or replace type jedi under force_sensitive (
  overriding member function alignment
    return varchar2
);
/

create or replace type body jedi as
  overriding member function alignment
    return varchar2
  as
    begin
      return 'light';
    end;
end;
/

-- And another real type with a specialty
create or replace type sith under force_sensitive (
  -- Default constructor has all member-attributes
  -- as parameters and assignes them. We can change
  -- this with our own constructor
  constructor function sith( c_name in varchar2 )
    return self as result,

  overriding member function alignment
    return varchar2
);
/

create or replace type body sith as
  constructor function sith( c_name in varchar2 )
    return self as result
  is
    begin
      -- self is a special built-in parameter
      -- which is passed to each member function as first
      -- parameter, no matter if explicitly defined or not.
      -- It holds an instance to the object
      self.c_name := 'Darth ' || c_name;

      -- however, in a constructor we dont return self
      -- but just return.
      return;
    end;

  overriding member function alignment
    return varchar2 is
    begin
      return 'dark';
    end;
end;
/

declare
  l_luke force_sensitive;
  l_vader force_sensitive;

  -- We can here see one of the first gifts of plsql-
  -- objects: We can build generalized methods which
  -- work for any subtype
  procedure output_alignment(
    i_force_user in out nocopy force_sensitive )
  as
    begin
      dbms_output.put_line(i_force_user.name() ||
        ' is aligned to ' || i_force_user.alignment());
    end;
begin
  -- Create a new instance of a concrete type
  l_luke := new jedi('Luke Skywalker');
  output_alignment(l_luke);

  l_vader := new sith('Vader');
  output_alignment(l_vader);
end;
/ 
```

输出:

```
Luke Skywalker is aligned to light
Darth Vader is aligned to dark 
```

您可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/hu8xkvvnavq9yzgk10qupu798) 上运行这个例子。

这只是这个特定主题中关于 PL/SQL 对象的几个例子中的第一个，请继续关注。