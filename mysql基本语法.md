​	

1、 **if** 语句或者 **case when** 语句写法

![image-20230130210551373](E:\Learn\markdown\数据库\png\mysql基本语法\1.png)

请你编写一个 SQL 查询来交换所有的 `'f'` 和 `'m'` （即，将所有 `'f'` 变为 `'m'` ，反之亦然），仅使用 **单个 update 语句** ，且不产生中间临时表。

注意，你必须仅使用一条 update 语句，且 **不能** 使用 select 语句。

查询结果如下例所示。

![image-20230130210703186](E:\Learn\markdown\数据库\png\mysql基本语法\2.png)

**If** 语句：

```mysql
update Salary set sex=if(sex='m','f','m')
```

**case when** 语句：

```mysql
update Salary set sex= case sex when 'f' then 'm' else 'f' end;
```

2、 **like+%** 取余、 **mod** 和 **left** 函数、 **if** 判断和 **mod** 取余、 **case** 函数、正则匹配、**&** 预算与 **regexp** 运算

![image-20230130212049955](E:\Learn\markdown\数据库\png\mysql基本语法\3.png)

写出一个SQL 查询语句，计算每个雇员的奖金。如果一个雇员的id是奇数并且他的名字不是以'M'开头，那么他的奖金是他工资的100%，否则奖金为0。

Return the result table ordered by `employee_id`.

返回的结果集请按照`employee_id`排序。

查询结果格式如下面的例子所示。

![image-20230130212128953](E:\Learn\markdown\数据库\png\mysql基本语法\4.png)

 **like+%** 取余

```mysql
select 
    employee_id,
    if (
        employee_id % 2 = 1 and name not like 'M%',
        salary,
        0
     ) as bonus
from employees
order by employee_id;
```

 **mod** 和 **left** 函数

Mod(a,b) 在sql中的意思是 a % b
Mod基础用法：如果id需要是偶数或者奇数时就可以使用mod。
mod(id,2)=1 是指id是奇数。
mod(id,2)=0 是指id是偶数。
LEFT：返回最左边的n个字符的字符串str，或NULL如果任何参数是NULL。

```mysql
select 
    employee_id,
    (
        case 
            when mod(employee_id,2)!=0 and left(name,1)!='M' then salary
            when mod(employee_id,2)=0 or left(name,1)='M' then 0
        end
    ) as bonus
from employees
order by employee_id;
```

 **if** 判断和 **mod** 取余

IF第一个参数写条件，第二个参数写条件成立返回的内容，第三个参数写条件不成立返回的内容

```mysql
select
    employee_id,
    if(mod(employee_id,2)!=0 and left(name,1)!='M',salary,0) as bonus
from employees
order by employee_id;
```

 **case** 函数

CASE配合WHEN,THEN判断，LEFT和’=‘判断，MOD取余 CASE配合WHEN,THEN使用，WHEN后接条件，THEN后接符合条件返回的内容 ，有多个条件时使用 需要用ELSE返回以上条件都不成立时返回的内容，最后以END`结尾

```mysql
select
    employee_id,
    case
        when mod(employee_id,2)!=0 and left(name,1)!='M' then salary
        when mod(employee_id,2)=0 or left(name,1)='M' then 0
        else 0
    end as bonus
from employees
order by employee_id
```

正则匹配

`rlike`是正则，正则的写法与`java`一样。‘‘需要使用’\’,例如’\w’需要使用’\w’

```mysql
select 
    employee_id,
    case
        when mod(employee_id,2)=1 and name not rlike '^M' then salary
        else 0
    end as bonus
from employees
order by employee_id;
```

**&** 预算与 **regexp** 运算

`regexp`为正则运算符

逻辑与运算，`1&1=1；0&1=0`

```mysql
select 
    employee_id,
    if (
        employee_id&1 and name regexp '^[^M]',
        salary,
        0
    ) as bonus
from employees
order by employee_id;
```

3、开窗函数 **row_number()** 、自连接、 **not in** 、分析函数 **dense_rank()**

![image-20230130233242716](E:\Learn\markdown\数据库\png\mysql基本语法\5.png)

编写一个 SQL **删除语句**来 **删除** 所有重复的电子邮件，只保留一个id最小的唯一电子邮件。

以 **任意顺序** 返回结果表。 （**注意**： 仅需要写删除语句，将自动对剩余结果进行查询）

查询结果格式如下所示。

![image-20230130233325630](E:\Learn\markdown\数据库\png\mysql基本语法\6.png)

开窗函数 **row_number()**

```mysql
delete from Person 
where id in(
    select id
    from (
        select id,email,
        row_number()over(partition by email order by id) as id_order
        from Person
    )as p
    where id_order>1
);
```

自连接

```mysql
delete p1
from Person p1,Person p2
where p1.email=p2.email and p1.id>p2.id
```

 **not in** 

```mysql
delete from Person
where id not in (
    select id
    from (
        select min(id) as id
        from Person
        group by email
    ) as P
);
```

分析函数 **dense_rank()**

