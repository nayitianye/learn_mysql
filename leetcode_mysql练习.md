#### 简单

##### 1173.即时食物配送I

###### **题目描述**

![image-20230212171401022](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1173_1.png)

如果顾客期望的配送日期和下单日期相同，则该订单称为 「即时订单」，否则称为「计划订单」。

写一条 SQL 查询语句获取即时订单所占的百分比， **保留两位小数。**

查询结果如下所示。

![image-20230212171447017](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1173_2.png)

###### **题目解答**

**方法一：直接计算**

**思路**

题目要求**获取即时订单所占的比例**，最直观的思路就是计算出即时订单的数量，再计算出总订单的数量。即时订单数量除以总订单数据就是我们要求的比例。

**算法**

1. 计算即时订单数，即时订单的要求为**配送日期和下单日期相同**：

```mysql
select count(*) from Delivery where order_date = customer_pref_delivery_date
```

2. 计算总订单数：

```mysql
select count(*) from Delivery
```

3. 将两数相除，使用 `round` 函数保留 2 位小数，因为要计算的是所占比例，所以还需要乘 100。

```mysql
select round (
    (select count(*) from Delivery where order_date = customer_pref_delivery_date) / 
    (select count(*) from Delivery) * 100,
    2
) as immediate_percentage
```

**方法二： `sum` 和 `case when`**

**思路**

我们可以使用 `sum` 和 `case when` 计算出即时订单的数量。当满足条件的时候，使用 `case when` 将 `sum` 值加 1。

**代码**

```mysql
select round (
    sum(case when order_date = customer_pref_delivery_date then 1 else 0 end) /
    count(*) * 100,
    2
) as immediate_percentage
from Delivery
```

**方法三： `sum`**

**思路**

我们还可以直接使用 `sum`。当 `order_date = customer_pref_delivery_date` 为真时，`sum` 值加 1。

**代码**

```mysql
select round (
    sum(order_date = customer_pref_delivery_date) /
    count(*) * 100,
    2
) as immediate_percentage
from Delivery
```

##### 1221.查询结果的质量和占比

###### 题目描述

![image-20230212180315867](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1221_1.png)

将查询结果的质量 `quality` 定义为：

> 各查询结果的评分与其位置之间比率的平均值。

将劣质查询百分比 `poor_query_percentage` 为：

> 评分小于 3 的查询结果占全部查询结果的百分比。

编写一组 SQL 来查找每次查询的`名称`(`query_name`)、`质量`(`quality`) 和 `劣质查询百分比`(`poor_query_percentage`)。

`质量`(`quality`) 和`劣质查询百分比`(`poor_query_percentage`) 都应四舍五入到小数点后两位。

查询结果格式如下所示：

![image-20230212180413792](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1221_2.png)

###### 题目解答

**思路**

1. 计算质量 `quality`：

> 各查询结果的评分与其位置之间比率的平均值。

评分与其位置之前的比率为 `rating/position`， 平均值为：

```mysql
avg(rating/position)
```
2. 计算劣质查询百分比 `poor_query_percentage`

>评分小于 3 的查询结果占全部查询结果的百分比。

评分小于 3 的数量可以使用 `SUM` 和 `IF`，如果 `rating` 小于 3，那么数量加 1。全部查询结果可以直接使用 `COUNT()`。因为要求的是百分比，所以分子需要乘 100。

```mysql
avg(rating<3)
```

最后不要忘记使用 `ROUND()` 函数将结果**四舍五入到小数点后两位**。

**代码**

```mysql
select query_name,
       round(avg(rating/position),2) as quality,
       round(avg(rating<3)*100,2) as poor_query_percentage
from Queries
group by query_name
```

##### 1303.求团队人数

###### 题目描述

![image-20230212200813182](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1303_1.png)

编写一个 SQL 查询，以求得每个员工所在团队的总人数。

查询结果中的顺序无特定要求。

查询结果格式示例如下：

![image-20230212200907157](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1303_2.png)



###### 题目解答

##### 1663.各赛事的用户注册率

###### **题目描述**

![](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1663_1.png)

![image-20230212165636211](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1663_2.png)

写一条 SQL 语句，查询各赛事的用户注册百分率，保留两位小数。

返回的结果表按 `percentage` 的 **降序** 排序，若相同则按 `contest_id` 的 **升序** 排序。

查询结果如下示例所示。

![image-20230212165842788](E:\Learn\数据库\learn_mysql\png\leetcode_mysql练习\1663_3.png)

###### **题目解答**

```mysql
select contest_id, round(count(distinct user_id)/(select count(*) from Users)*100,2) as percentage
from Register
group by contest_id
order by percentage desc, contest_id asc
```

#### 中等



#### 困难