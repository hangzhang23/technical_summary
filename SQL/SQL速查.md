1. 概念

- 数据库（database）：数据库是以某种有组织的方式储存的数据集合。
- 表（table）：某种特定类型数据的结构化清单.
- 模式（schema）：关于数据库和表的布局及特性的信息。
- 列（column）：表中的一个字段。所有表都是由一个或者多个列组成的
- 数据类型（datatype）：允许什么类型的数据。每个表列都有相对应的数据类型，它限制该列的存储的数据。
- 行（row）：表中的一个记录。
- 主键（primary key）：一列（或几列），其值能够唯一标识表中的每一行。
- 子句（clause）：SQL语句由子句组成，一个子句通常由一个关键字加上所提供的数据组成。
- 通配符（wildcard）：用来匹配值的一部分的特殊字符。通配符搜索只能用于文本字段（字符串）。
- 搜索模式（search pattern）：由字面值、通配符或两者组合构成的搜索条件。
- 字段（field）：基本与列的含义相同，但字段这个术语通常在计算字段这种场合下使用。
- 拼接（concatenate）：将值联结到一起构成单个值。
- 查询（query）：任何SQL语句都是查询。但此术语一般只SELECT语句。
- 可伸缩（scale）：能够适应不断增加的工作量而不失败。设计良好的数据库或者应用程序称为可伸缩性好。

2. 检索数据

sql最基本的操作，以select为开头，from选择列表。

```sql
select 列 from 表;
select 列, 列, 列 from 表;
select * from 表;
select distinct 列 from 表;
select 列 from 表 limit 值;
select 列 from 表 limit 值 offset 值;
```

注：
- select * 表示选择所有数据
- distince col 表示只返回不同的行，相同则选一个。
- 在 from 表后面加limit 是限制选择的个数；
- offset 表示从哪一行开始

3. 排序检索数据

在检索选择数据时可以添加的排序操作，跟在from后面，表示以order by指示的列进行排序。
```sql
select 列 from 表 order by 列;
select 列, 列, 列 from 表 order by 列, 列;
select 列, 列, 列 from 表 order by 值，值;
select 列, 列, 列 from 表 order by 列 desc, 列;
```

注：
- 在不注明列为desc时，都是升序排列，可用“列 desc”来指定排序；
- 当order by后面出现两个列时，按照先后顺序；
- 如果order by跟的是值，那么就是列所在的位置，比如“学生”所在的为3列。

4. 过滤数据

SQL语句中，可以用where触发过滤操作。
```sql
select 列, 列, 列 from 表 where 列 != 值;
```

注：
- 如果有order by则需要放在where之后；
- 当过滤选择不包含某值的所有行，不会返回null值得行；


操作符	说明
=	等于
<>	不等于
!=	不等于
<	小于
>	大于
<=	小于等于
>=	大于等于
!<	不小于
!>	不大于
between 值 and 值	在两个值之间
is null	为null值

5. 高级过滤数据

对where语句的复合操作，算是个多条件过滤
```sql
SELECT 列，列 FROM 表 WHERE 列 = 值 AND 表 <= 值;
SELECT 列，列 FROM 表 WHERE (列 = 值 OR 表 <= 值) AND 列 >= 值; 
SELECT 列，列 FROM 表 WHERE 列 IN (值，值); 
SELECT 列，列 FROM 表 WHERE NOT 列 = 值;
```

注：
- where后面可用and，or，和符号组合多条件，但是and优先级高于or，在需要or先运算得时候，需要辅助括号；
- 在等于多个值中，可以用“col=值 or col =值”，但是用“col in (值，值)”速度更快；

6. 用通配符进行过滤

仍然是过滤操作，但使用like可以进行模糊过滤

```sql
SELECT 列,列 FROM 表 WHERE 列 LIKE 'Fish%'; -- 选取Fish开头的
SELECT 列,列 FROM 表 WHERE 列 LIKE '%Fish%'; -- 选取含有Fish的
SELECT 列,列 FROM 表 WHERE 列 LIKE 'F%y';
SELECT 列,列 FROM 表 WHERE 列 LIKE '__ inch teddy bear'; 
SELECT 列,列 FROM 表 WHERE 列 LIKE '% inch teddy bear'; --同上效果一样。
SELECT 列,列 FROM 表 WHERE 列 LIKE '[JM]%'; --开头是J或者M,^表示否定。
SELECT 列,列 FROM 表 WHERE 列 LIKE '[^JM]%'; --可以用NOT代替。
SELECT 列,列 FROM 表 WHERE NOT 列 LIKE '[JM]%'; --同上效果一样。
```

- __是匹配两个字符，_匹配一个，使用%可以都包含

通配符	说明
%	代表零个或者多个字符
_	仅代替一个字符
[charlist]	字符列表中的任何单一字符
[^charlist]或[!charlist]	不在字符列中的任何单一字符


7. 创建计算字段

字段这个词也是在SQL中出现，用以指代列，所以计算字段其实就是列计算，而这一步出现在select部分。根据数据库不同，可用+（access，SQL server），||（DB2，Oracle，SQLlite，PostgreSQL，Open Office Base）或者concat（MySQL和MariaDB）来进行字段拼接。

``sql
SELECT 列 + '(' 列 + ')' FROM 表; --返回列(列)，+可以用||代替。
SELECT Concat(列,'(', 列 , ')') FROM 表; --Mysql是这样的。这样和上面会填充空格。
SELECT RTRIM(列) + '(' RTRUM(列) + ')' FROM 表; -- 这样才会正确返回格式化数据。RTRIM()函数会取掉右边所有的空格。TRIM(),LTRIM()。
SELECT RTRIM(列) + '(' RTRUM(列) + ')' AS 别名 FROM 表; --使用别名（alias）。这样就可以在客户端引用它。别名也叫导出列。
SELECT 列，列，列*列 AS 列 FROM 表 WHERE 列 = 值; --AS的列是计算之后的,也会被显示出来。
```

8. 使用函数处理数据

SQL自带的一部分函数，可在任何需要进行函数操作的地方使用。
```sql
SELECT 列,UPPER(列) AS 列 FROM 表;--处理文本
SELECT 列 FROM 表 WHERE YEAR(列) = 2020;--指定时间
```

	函数	说明
文本处理函数	LEFT()	返回字符串左边字符
LENGTH()	返回字符串长度
LOWER()	字符串转小写
LTRIM()	去掉字符串左边的空格
RIGHT()	返回字符串右边的字符
RTRIM()	去掉字符串右边的空格
SUBSTR(),SUBSTRING()	提取字符串的组成部分
SOUNDEX()	返回字符串的soundex值
UPPER()	字符串转大写
时间处理函数（含非时间）	ABS()	绝对值
COS()	余弦
EXP()	指数
PI()	圆周率
SIN()	正弦
SQRT()	平方根
TAN()	正切
聚集函数	AVG()	平均值
COUNT()	行数
MAX()	最大值
MIN()	最小值
SUM()	和


9. 分组数据

主要是groupby的使用，注意结果会按照group by指定列进行分组，在使用得时候group by的优先级高于其他。

```sql
SELECT 列,COUNT(列) AS 列名
FROM 表
GROUP BY 列;

SELECT 列,COUNT(*) AS 列名
FROM 表
GROUP BY 列
HAVING COUNT(*) >= 2; -- where和having的区别是，where在数据分组前进行过滤，having在数据分组后进行过滤。

SELECT 列,COUNT(*) AS 列名
FROM 表
WHERE 列 >= 4
GROUP BY 列
HAVING COUNT(*) >= 2;
```

- 注意where和having的差别，where在分组前规律，having在分组后过滤。

```sql
select字句顺序	说明	是否必须使用
select	要返回的列或表达式	yes
from	从中检索数据的表	尽在表中选择数据时使用
where	行级过滤	no
group by	分组说明	仅在按组计算聚集时使用
having	组级过滤	no
order by	输出排序顺序	no
limit	限制	no
```

10. 使用子查询

子查询的作用就是在sql的查询语句中插入额外的查询语句

```sql
SELECT cust_id FROM Orders
WHERE order_num IN (SELECT order_num FROM OrderItems WHERE prod_id = 'RGANO1');--作为子查询的SELECT语句只能查询单列。

SELECT cust_name,
       cust_state,
       (SELECT COUNT(*)
       FROM Orders
       WHERE Orders.cust_id = Customers.cust_id) AS orders
FROM Customers
ORDER BY cust_name --为了避免产生冲突，必须采用完全限定列名。
```

11. 联结表

联结表出现from两个表的情况，使用where做链接，也可以用join链接

```sql
SELECT vend_name,prod_name,prod_price
FROM Vendors,Products
WHERE Vendors.vend_id = Products.vend.id;--如果没有where做联结，则结果满足笛卡尔乘积。

SELECT vend_name,prod_name,prod_price
FROM Vendors
INNER JOIN Products ON Vendors.vend_id = Products.vend.id;--等值联结也叫内联结(inner join)，可以通过不同的语法明确联结的类型。
```

- where后面的过滤语句就可以在两个表中分别选择列；
- inner join为内连接。

12. 创建高级联结

在需要从两个表中提取不同的列做下一步运算得时候，可以用高级联结。主要也是用AS来对不同的表命名。
--自联结

```sql
SELECT c1.cust_id,c1.cust_name,c1.cust_contact
FROM Customers AS c1, Customers AS c2
WHERE c1.cust_name = c2.cust_name
AND c2.cust_contact = 'Jim Jones'--别名的好处在于在SELECT语句中可以不止一次引用相同的表。自联结比子查询快得多。
```

--外联结
--左外连接关键字是LEFT OUTER JOIN 或LEFT JOIN(还是不建议省略outer，可读性不强)。左外连接查询是以左边的表为基准，去匹配要连接的表，不管是否匹配条件都会以基准表的条数返回结果(这里明显不同于内连接)，匹配到的数据就显示匹配到的数据，没有匹配条件的数据就显示为null。右联结相似。全连接(full join)就是返回目标表的所有数据，有匹配的就显示，没有匹配的就为null。MYSQL里面没有全联结，可以对左外连和右外联做union实现相同的效果。

```sql
SELECT td.dept_id,td.dept_name,te.emp_name  
FROM tb_dept td
LEFT OUTER JOIN tb_emp te
ON td.dept_id = te.dept_id ;
```

- 左外连接 left outer join。左外链接查询是以左边的表为基准，去匹配要链接的表，不管是否匹配条件都会以基准表的条数返回结果(这里明显不同于内连接)，匹配到的数据就显示匹配到的数据，没有匹配条件的数据就显示为null。
- 右联结相似。全连接(full join)就是返回目标表的所有数据，有匹配的就显示，没有匹配的就为null。MYSQL里面没有全联结，可以对左外连和右外联做union实现相同的效果。

13. 组合查询

union将多条select语句组合成一个结果集。
```sql
select cust_name,cust_contact,cust_email
from customers
where cust_state in ('IL','IN','MI')
union
select cust_name,cust_contact,cust_email
from customers
where cust_name='Fun4ALL';
```

上面这段是用两端select语句，通过中间的union连接成一个结果集。
```sql
select cust_name,cust_contact,cust_email
from customers
where cust_state in ('IL','IN','MI')
or cust_name='Fun4ALL'
```

上面由于是从一个表查询，则可不用union，直接使用一个select以及where得到组合查询。
- 简单查询，union不明显，而复杂过滤，union可以让检索更快捷；
- union使用规则：
-- 必须有两条或两条以上的select语句组成；
-- union中每个查询必须包含相同的列、表达式和聚合函数。也就是select必须相同；
-- 数据类型必须兼容。

14. 插入数据

关键字insert
- 插入完成的行
```sql
insert into 表名（表中的列名1，列名2，...，列名n）
values（每一列对应的内容，内容1，内容2，...，内容n）
```
- 插入部分行，和插入完成行语法相同，只是并不要求输入表名的全部列名。
- 插入检索出的数据
```sql
insert into 被插入表名（被插入表中的列名1，列名2，...，列名n）
select 选择插入的表的列名1，列名2，...，列名n
from 选择插入的表
```
- 从另一个表赋值到另一个表
```sql
select * into 表1 from 表2;
```

- 在使用select into 时，任何select选项和子句都可以使用包括where和group by；
- 可利用链接从多个表插入数据；
- 不管从多少个表中检索数据，数据都只能插入一列；
- 在插入前记得备份数据。

15. 更新和删除数据
- update 更新：更新表中特定行，以及所有行。
```sql
update 表1
set 列名1=内容1
列名2=内容2
where 列名3=内容3；
```

- delect 删除：删除表中的特定行，使用where；删除所有行。
```sql
delete from 表1
where 列名1=内容1
```
- 除非确定更新和删除每一行，否则使用语句的时候一定要加上where！
- 保证每个表都有主键，使用where和主键配合，确定更新删除的范围
- 在使用更新和删除前，先用select看看选定的是不是要更新删除的内容。
- 使用强制实施引用完整数据库，，DBMS将不允许删除取数据与其他表相关联的行
- 要注意有没有存在撤销按钮，以防止更新删除错误
- 更新删除前记得备份数据

16. 创建和操纵表
- 创建表：
-- 交互式创建和管理数据库表的工具
-- 表可以直接只用SQL语句进行操纵

···sql
create table 表名1 (列名1 数据类型, 列名2 数据类型, ...)
```
- 更新表：
```sql
alter table 表名1 
add 列名1 数据格式 
```
- 删除表：
```sql
drop table 表名1
```
- 重命名表：

17. 使用视图

虚拟的表，使用时动态检索数据
- 视图的规则：
-- 视图名字必须唯一，不能和别的视图和表重名。
-- 创建视图数目没有限制
-- 视图可以嵌套

```sql
create view PC as
select cust_name,cust_contact,prod_id
from customers,orders,orderitems
where customers.cust_id=orders.cust_id
and orderitems.order_num=orders.order_num;
```

- 视图重新格式化检索数据
-- 在视图里连接两个列
-- 在视图里过滤数据，比如空值异常值
-- 在视图中计算字段

18. 使用存储过程

存储的用途：
- 将语句封装，简化操作
- 不要求反复建立一些过程，所有的人员和程序使用相同的过程——代码
- 简化变动的管理。比如表名，列名变化，只需要修改存储过程的代码
- 存储过程中通常以编译过的形式存储，所以工作量减少
- 存在一些只能用在单个请求的元素和特性
