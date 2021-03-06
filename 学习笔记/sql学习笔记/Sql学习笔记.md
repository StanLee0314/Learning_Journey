#<font size =  6>表创建增删改</font>

# <font size =5>创建表</font>

``` mysql
create table Orders
（ 
 Order_id integer not null,
 order_date datetime default current_time(),
 );
 
 alter table Orders drop(add) column id;

```

# <font size =5>插入数据</font>

可以选择部分行， 可以为null.

复制表

```mysql
create table custcopy as select * from customers
```

可以用select from table；

Insert into Customers(cust_id,

​                                        cust_name,

​                                        Just_address)

values(

'10000',

'Tony',

'122 Any Street');

也可以用

select 

ID，

name，

address

from Customers；

#<font size =5>更新删除</font>

Update table_name where

Delete From table_name where 

# <font size =  6>基本语句</font>

Select   <font color = red size = 5>(distinct)  </font>Column_name1，column_name2 from table_name 

```mysql
select Concat(vender_name, '(', vender_contry, ')') as vender_tilte from table_name
```



(sql优先处理and，多条件处理加括号)

Where pro_price between 5 and 10 (<,  != (<>), is null ) and pro_id = 'D01' or (....)

`where pro_price in (3.2, 8.6)`

where <font color = red>not</font> pro_price = 2.6 

============================================================

数据分组

```mysql
group by order_num
having count(*)>=3
order by items, order_num;
```

Order by colume_name1, column name2<font color= red size = 5> desc(降序)</font>(<font color= red> 仅在 column_name1 相等时才排列column_name2</font>) 

```mysql
limit 5
```

顺序 : 

<font color = red size =4 >select from</font> 

<font color = red size =4 >where</font> 

<font color = red size =4 >group by</font> 

<font color = red size =4 >having</font> 

<font color = red size =4 >order by</font> 



# <font size =  6>函数</font>

`Length(), Ltrim(),Rtrim(),upper(),lower()`

#<font size =  6>通配符</font>

%匹配多个字符  

_匹配单个字符

[JM]集合 exp：like '[JM]%'  <font color =red>姓名以J或M开头的名字</font>

#<font size =  6>子查询</font>

```mysql
select cust_name,
cust_state,
(select count(*) from orders where Orders.cust_id = Customers.cust_id) as orders
from Customers
order by cus_name;
```

# <font size = 6>聚集函数</font>

avg(), count(*)所有行，count(just_email)忽略null值，max(), min(), sum()

```mysql
select count(*) as num_items,
min(prod_price) as price_min,
max(pro_price) as price_max
from products;
```

# <font size =  6>注释</font>

/*

*/

# <font size =  6>联结</font>

<font size =  5>内联结（等值联结)</font>

```mysql
select cus_name, cust_contact
from Customer, Orders, OrderItems
where Customer.cusId = Orders.cusId and
Orders.order_nums = OrderItems.order_nums and
prod_id = 'Rg01';
```

给表起别名 

`from Customer as a, Orders as b, OrderItems as c `

<font size =  5>自联结</font>

找到jim所在的顾客(子查询)

```mysql
select cust_id, cust_name,cust_contact
from Customer
where cust_name = select cust_name 
                  from Customers
                  where cust_contact = 'Jim';
```

```mysql
select c1.cust_id, c1.cust_name,c1.cust_contact
from Customer as c1,Customer as c2
where c1.cust_name = c2.cust_name 
and c2.cust_contact ='Jim';
```

<font size =  5>外联结</font>

检索包括没有订单顾客在内的所有顾客（加聚集函数）

```mysql
select Customer.cust_id,
Count(Orders.order_nums) as num_ord
from Customers LEFT OUTER JOIN Orders
On Customer.cust_id = Order.cust_id
group by Customer.cust_id;
```

<font size =  6>组合查询</font>

两个查询的列要完全相同，自动去除重复行，（union all 不去）

```mysql
select vender_name, vender_age from table_name
where pro_price in (3.2, 8.6)
union
select vender_name, vender_age from table_name
where vender_name = 'Jim';
```

# <font size =  6>视图</font>

```mysql
create view ProductCustomers as 
select cus_name, cust_contact
from Customer, Orders, OrderItems
where Customer.cusId = Orders.cusId and
Orders.order_nums = OrderItems.order_nums
```

1. 简化复杂查询

2. 格式化数据

3. 过滤不想要的数据

4. 使用数据与计算字段

   

# sql语句分类

**SQL 语句主要可以划分为以下 3 个类别。**

**DDL（Data Definition Languages）语句：**数据定义语言，这些语句定义了不同的数据段、数据库、表、列、索引等数据库对象的定义。常用的语句关键字主要包括 create、drop、alter等。

**DML（Data Manipulation Language）语句：**数据操纵语句，用于添加、删除、更新和查询数据库记录，并检查数据完整性，常用的语句关键字主要包括 insert、delete、udpate 和select 等。(增添改查）

**DCL（Data Control Language）语句：**数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库、表、字段、用户的访问权限和安全级别。主要的语句关键字包括 grant、revoke 等。

## MySQL游标/存储过程

```csharp
delimiter //
drop procedure if exists StatisticStore;
CREATE PROCEDURE StatisticStore()
BEGIN
    --创建接收游标数据的变量
    declare c int;
    declare n varchar(20);
    --创建总数变量
    declare total int default 0;
    --创建结束标志变量
    declare done int default false;
    --创建游标
    declare cur cursor for select name,count from store where name = 'iphone';
    --指定游标循环结束时的返回值
    declare continue HANDLER for not found set done = true;
    --设置初始值
    set total = 0;
    --打开游标
    open cur;
    --开始循环游标里的数据
    read_loop:loop
    --根据游标当前指向的一条数据
    fetch cur into n,c;
    --判断游标的循环是否结束
    if done then
        leave read_loop;    --跳出游标循环
    end if;
    --获取一条数据时，将count值进行累加操作，这里可以做任意你想做的操作，
    set total = total + c;
    --结束游标循环
    end loop;
    --关闭游标
    close cur;

    --输出结果
    select total;
END;
--调用存储过程
call StatisticStore();
```

#索引

```mysql
create index pro_name_id on products(pro_name);
```

