---
title: MySQL学习小结
date: 2018-3-1 20:08:08
tags:
- MySQL
---

这一篇最主要是记录下命令，方便以后查找

#### **使用Mysql**
创建数据库
> create database mysql_test;

展示所有数据库
> show databases;

选择要使用的数据库
> use Typeidea_db

获得表tables

> show tables;

显示服务器状信息

> show status;

更多show命令查询
> help show


#### **检索数据**

- select + 列名 from + 表
- selcet、from 都为关键字
- 使用select语句从products表中检索一个名为prod_name的列

> SELECT prod_name FROM products;

检索多个多个列
> select prod_name, prod_id, prod_price from products;

检索所有列

> select * from products;

检索不同的行
- DISTINCT关键字 “不同”
- 告诉MySQL只返回不同的vend_id行

> selcet DISTINCT vend_id from products;

检索限制结果
- limit
- limit 5,5:第一个5开始位置，第二个5要检索的行数;
- limit 4 OFFSET 3 :从行3开始取4行
- 返回不多于5行

> select prod_name from products LIMIT 5;

> select prod_name from products LIMIT 5,5;

### **排序检索数据**

- order by :排序
- 以字母顺序排序数据（A-Z）
> select prod_name from products ORDER BY prod_name;

按多个列排序

> select prod_name, prod_id, prod_price from products ORDER BY prod_price, prod_name;

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/9H1uQjhKa4i3BCbKhBKC4TZfxiBAwiQPuJojsnkeGmM!/b/dAgBAAAAAAAA&bo=ZwOlAQAAAAADB.I!&rf=viewer_4)


指定排序方向

- DESC 降序排序
- Z-A
- 对产品价格进行排序

> select prod_id, prod_price, prod_name from products ORDER BY prod_price DESC;

- 降序排序产品价格，再对产品名字排序
> select prod_id, prod_price, prod_name from products ORDER BY prod_price DESC, prod_name;


使用order by 和limit 找出列中最高或最低的值
- 去掉DESC就是最低值
- limit 1 ：仅返回一行
- 使用order by 和limit子句时，limit子句必须在order by 子句之后

> select prod_price from products ORDER BY prod_price DESC LIMIT 1;


### **过滤数据**

where子句

- 从products表中检索两个列， 过滤（只返回prod_price = 2.5的行）
- **使用order by 和 where子句时，order by 必须在where之后**
> select prod_name, prod_price from products WHERE prod_price = 2.50;


**检查单个值**

where字句操作符

- =
- <
- >
- !=
- <=
- >=
- BETWEEN ：在指定的两个值之间

检查where prod_name = 'fuses'，返回prod_name 的值为fuses的一行

- **值为字符串时需要使用单引号**

 > select prod_name, prod_price from products WHERE prod_name = 'fuses';

列出小于等于10美金的所有产品

> select prod_price, prod_name from products WHERE prod_price <= 10;


**不匹配检查**

列出不是由供应商1003制造的所有产品

> select vend_id, prod_name from products WHERE vend_id != 1003;


**范围值检查**

查询价格在5美金至10美金之间的所有产品
- ADN ：两个值之间必须要使用AND来连接
- BETWEEN

> select prod_name, prod_price from products WHERE prod_price BETWEEN 5 AND 10;

**空值检查**
NULL：空值

> select prod_name from products WHERE prod_price IS NULL;

文件中没有某位顾客的电子邮件

> select cust_id from customers WHERE cust_email IS NULL;


**组合WHERE子句**
AND操作符
检索供应商1003制造且价格小于等于10美金的所有产品的价格名称、价格、ID

> select prod_id, prod_price, prod_name from products WHERE vend_id = 1003 AND prod_price <=10;

OR操作符
- 指示MySQL检索匹配任一条件的行
- 检索任一个指定供应商制造的所有产品的名称和价格

> select prod_name, prod_price from products WHERE vend_id = 1002 OR vend_id = 1003;

**计算次序**
例子：
检索出10美金以上且由1002或者1003制造的所有产品
> select prod_name, prod_price from products WHERE vend_id = 1003 OR vend_id = 1002 AND prod_price >= 10;

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/kO3IdHsXAOaiXwhu9HFw9PgLwVExDtxDhIzawM8tEAU!/b/dAgBAAAAAAAA&bo=BgQUAQAAAAADFyU!&rf=viewer_4)

- sql语句理解为：检索1003供应商制造的所有产品或者1002供应商制造的大于10美金的所有产品
- and 在计算次序中优先级更高
- 这里应该使用圆括号

> select prod_name, prod_price from products WHERE(vend_id = 1002 or vend_id = 1003) AND prod_price >= 10;

 IN 操作符
 IN - 用来指定范围


检索由1002或1003供应商提供的所有产品的名称和价格，名称以升序排序
> select prod_name, prod_price from products WHERE vend_id IN (1002, 1003) ORDER BY prod_name;


NOT操作符

- 用来否定它之后的所跟的任何条件
- 匹配除开1002和1003外所有的供应商的产品名字和价格，排序以名称升序排序

> select prod_name, prod_price from products WHERE vend_id NOT IN (1002, 1003) ORDER BY prod_name;

**用通配符进行过滤**

- LIKE操作符: 匹配未知值
- %通配符：表示任何字符出现任意次数
- 通配符无法匹配以null值作为产品名的行

查找以'jet'开头的产品

>  select prod_id, prod_name from products WHERE prod_name LIKE 'jet%';
 
搜索模式‘%anvil%’表示匹配任何位置包含文本anvil的值，无论它之前或之后出现什么字符


找到以s起头e结尾的所有产品

> select prod_name from products WHERE prod_name LIKE 's%e';

下划线（_）通配符
 - **至匹配单个字符**
 - 下划线后有个空格
 - .5 ton anvil没有被匹配，因为搜索模式要求匹配两个匹配符而不是一个”.一个“   ”5一个“


> select prod_id, prod_name from products WHERE prod_name LIKE '_ ton anvil';

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/jFqz6NjRu5tW5lWomyI1UrwGxFw9zGFAy6mCjj67pR8!/b/dGgBAAAAAAAA&bo=*wKuAAAAAAADB3E!&rf=viewer_4)



 ### **用正则表达式进行搜索**
- REGEXP
- LIKE :如果匹配值在列中被找到将不会返回数据，like匹配未知值

检索prod_name文本1000的所有行

> select prod_name from products WHERE prod_name REGEXP '1000' ORDER BY prod_name;

匹配所有.000
> select prod_name from products WHERE prod_name REGEXP '.000' ORDER BY prod_name;


OR匹配

- 使用  |  :为正则表达式中的OR操作，表示匹配其中之一
> select prod_name from products WHERE prod_name REGEXP '1000|2000' ORDER BY prod_name;


匹配几个字符之一

[123] Ton
- [123]定义一组字符，匹配1或2或3

> select prod_name from products WHERE prod_name REGEXP '[123] Ton' ORDER BY prod_name;


或者这样写

> select prod_name from products WHERE prod_name REGEXP '[1|2|3] Ton' ORDER BY prod_name;



**匹配范围**
- 用 - 来匹配
- [1-5]
- [a-z]


> select prod_name from products WHERE prod_name REGEXP '[1-5] Ton' ORDER BY prod_name;

**匹配特殊字符**

- 必须使用’\\‘   \\-     \\.    转义
> select vend_name from vendors WHERE vend_name REGEXP '\\.' ORDER BY vend_name;


**匹配多个实例**

> select prod_name from products WHERE prod_name REGEXP '\\([0-9]) sticks?\\)' ORDER BY prod_name;

- \\()
- [0-9]；匹配任意数字

> select prod_name from products WHERE prod_name REGEXP '[[:digit:]]{4}' ORDER BY prod_name;

- [:digit:]；任意数字集合
- {4}：任意4位数

**定位符**

- 在集合中[^.....]表示否定该集合或者指定字符的开始处


### **创建计算字段**


**拼接字段**

 - concate()函数来实现拼接

名字和地址拼接起来

> select Concat(vend_name, '(', vend_country, ')') from vendors ORDER BY vend_name;

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/2X5hE2XwrYRfHFpKuTP4XBAP*CE5G3JS*LgO8krHWtY!/b/dFYBAAAAAAAA&bo=mgMDAQAAAAADB7k!&rf=viewer_4)


- 删除右侧多余空格
- Concat()
- RTrim()函数

>  select Concat(RTrim(vend_name), '(', RTrim(vend_country), ')') from vendors ORDER BY vend_name;

**使用别名**
- alias 别名 AS

> select Concat(RTrim(vend_name), '(', RTrim(vend_country), ')') AS vend_title from vendors ORDER BY vend_name;

**执行算术计算**

> select prod_id, quantity, item_price from orderitems WHERE order_num = 2005;



### **使用数据处理函数**

**文本处理函数**

> select vend_name, Upper(vend_name) AS vend_name_upcase from vendors ORDER BY vend_name;

- AS(alias) 使用别名
- Upper()函数：将文本转换为大写


> select cust_name, cust_contact from customers WHERE Soundex(cust_contact) = Soundex('Y Lie');

- Soundex 检索所有发音类似‘Y Lie’的联系名（cust_contact）

**日期和时间处理函数**

> select cust_id, order_num from orders WHERE order_date = '2005-09-01';

如果要的是日期，就使用Date()
> select cust_id, order_num from orders WHERE Date(order_date) = '2005-09-01';

检索2005年9月份下的所有订单
1. 
> select cust_id, order_num from orders WHERE Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';

2. 
> select cust_id, order_num from orders WHERE Year(order_date) = 2005 AND Month(order_date) = 9;


### **汇总数据**
**AVG（）函数**
- AVG（）函数：返回某列的平均值
- AVG（）函数忽略列值为NULL的行
- AVG（）函数只能确定特定数值列的平均值

使用AVG函数f返回products表中所有产品的平均价格
> select AVG(prod_price) AS avg_price from products;

返回特定供应商所提供产品的平均价格

- 使用where来过滤出vend_id = 1003的产品
- AS 给prod_price取别名
> select AVG(prod_price) AS avg_price from products WHERE vend_id = 1003;


**COUNT（）函数**
 - COUNT(*)对表中数目进行计数，不管表列中包含的是空值还是非空值
 - COUNT(column)对特定列中具有值的行进行计数，忽略NULL值


统计customers表中所有的行，不管各列有什么值
> select COUNT(*) AS num_cust from customers;

只对具有电子邮箱地址的客户计数
> select COUNT(cust_email) AS num_cust from customers;

**MAX函数**
- 返回指定列中的最大值
- 要求指定列名

检索products表中最贵物品的价格

> select MAX(prod_price) AS max_price from products;

**MIN函数**

- 返回指定列的最小值

检索products表中最便宜i物品的价格
>select MIN(prod_price) AS min_price from products;


**SUM函数**

- 返回指定列值的和


检索所订购物品的总数

- SUM(quantity):返回订单中所有物品数量之和
- WHERE子句保证只统计某个物品订单中的物品

> select SUM(quantity) AS items_ordered from orderitems WHERE order_num = 20005;


检索20005订单中所有物品价格之和
- SUM(item_price*quantity)
> select SUM(item_price*quantity) AS total_price from orderitems WHERE order_num = 20005;


**DISTINCT**

使用distinct后，平均值值考虑各个不同的价格
>  select AVG(DISTINCT prod_price) AS avg_price from products WHERE vend_id = 1003;


**组合聚集函数**
返回4个值
- 物品的所有数目
- 最低价格
- 最高价格
- 平均价格


> select COUNT(*) AS num_items,
    -> MIN(prod_price) AS price_min,
    -> MAX(prod_price) AS price_max,
    -> AVG(prod_price) AS price_avg from products;


### **分组数据**

- GROUP BY 子句
- HAVING 子句


**创建分组**
- vend_id；包含供应商的ID
- AS 使用别名
- GROUP BY vend-id 对vend_id进行分组

> select vend_id, COUNT(*) AS num_prods from products GROUP BY vend_id;

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/eayzj17KAnaqB9lHt4teJWT5twIf9YwRGctWoQweZG0!/b/dAgBAAAAAAAA&bo=3gLXAAAAAAADByk!&rf=viewer_4)



ROLLUP

- 可以得到每个分组以及每个分组汇总级别（针对每个分组）的值
- null 值 也算了进去
> select vend_id, COUNT(*) AS num_prods from products GROUP BY vend_id WITH ROLLUP;


![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/oDZmhl7XCQdPuWtgv0i8vv9DfWjEKQqQEj9it2rcq84!/b/dEQBAAAAAAAA&bo=UQPyAAAAAAADB4I!&rf=viewer_4)

**过滤分组**


- having ：过滤分组
- where ：过滤行

过滤那些COUNT(*)*2的那些分组
> select cust_id, COUNT(*) AS orders from orders GROUP BY cust_id HAVING COUNT(*) >=2;


同时使用WHERE和HAVING子句
- 价格大于等于10
- vend_id分组
- 过滤COUNT(*)>=2分组

> select vend_id, COUNT(*) AS num_prods from products WHERE prod_price >= 10 GROUP BY vend_id HAVING COUNT(*) >= 2;


**分组和排序**

检索订总计订单价格大于等于50的订单的订单号和总计订单的价格

> SELECT order_num, SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price) >= 50;


按总计订单价格排序输出，需要添加ORDER BY子句

>  SELECT order_num, SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price) >= 50 ORDER BY ordertotal;

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/pleWNPxAdX1TgdGVYB2WeoV.2rZKCoNyli2Wawu*xtQ!/b/dEQBAAAAAAAA&bo=TQT0AAAAAAADB58!&rf=viewer_4)


### **使用子查询**

需要列出订购物品TNT2的所有客户，应该怎样检索？
		1. 检索包含物品TNT的所有订单的编号
		2. 检索具有前一步聚列出的订单编号的所有客户的ID
		3. 检索前一步聚返回的所有客户ID的客户信息


对于prod_id 为TNT2的所有订单物品，从orderitems表中检索其order_num列
> SELECT order_num FROM orderitems WHERE prod_id = 'TNT2';

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/l9LWv2iEbhL50jnqpr.1ifszzDuSh.kLH*4RvpeKlB8!/b/dJEAAAAAAAAA&bo=cgKxAAAAAAADB.M!&rf=viewer_4)

查询具有订单20005和20007的客户ID

- IN 范围设定

> SELECT cust_id FROM orders WHERE order_num IN (20005, 20007);

把第一步的查询变为**子查询**，组合两个查询
mysql实际执行了两个操作
- 执行查询：SELECT order_num FROM orderitems WHERE prod_id = 'TNT2';  返回20005和20007两个订单号
- IN操作，外部查询变成；SELECT cust_id FROM orders WHERE order_num IN (20005, 20007);

>  SELECT cust_id FROM orders WHERE order_num IN (SELECT order_num FROM orderitems WHERE prod_id = 'TNT2');


检索客户ID信息
> SELECT cust_name, cust_contact FROM customers WHERE cust_id IN (10001, 10004);

转换为子查询

> SELECT cust_name, cust_contact FROM customers WHERE cust_id IN (SELECT cust_id FROM orders WHERE order_num IN (SELECT order_num FROM orderitems WHERE prod_id = 'TNT2'));

**作为计算字段使用子查询**


对每个客户执行COUNT(*)计算，应该将COUNT(*)作为一个子查询

> SELECT cust_name, cust_state, (SELECT COUNT(*) FROM orders WHERE orders.cust_id = customers.cust_id) AS orders FROM customers ORDER BY cust_name;


**联结表**

- 这里有两张表， vendors和products，这就是联结的两个表的名字
- 用WHERE子句正确联结，过滤条件
- WHERE子句指示MYSQL匹配vendors表中的vend_id和products表中的vend_id

> SELECT vend_name, prod_name, prod_price
    -> FROM vendors, products
    -> WHERE vendors.vend_id = products.vend_id
    -> ORDER BY vend_name, prod_name;


**笛卡儿积**
- 由没有联结条件的表关系返回的结果为笛卡儿积
- 检索出的行的数目将是第一个表中的行数乘以第二个表中的行数


**内部联结**
- from子句的的组成由INNER JOIN指定
- 联结条件使用ON，而不是WHERE

> SELECT vend_name, prod_name, prod_price
    -> FROM vendors INNER JOIN products
    -> ON vendors.vend_id = products.vend_id;


**联结多个表**

显示编号为20005订单中的物品

> SELECT prod_name, vend_name, prod_price, quantity
    -> FROM orderitems, products, vendors
    -> WHERE products.vend_id = vendors.vend_id
    -> AND orderitems.prod_id = products.prod_id
    -> AND order_num = 20005;

### **创建高级联结**

**使用表别名**
- concat :拼接字段
- RTrim :删除右侧多余空格
- AS使用别名
- 

>  SELECT Concat(RTrim(vend_name), '(', RTrim(vend_country), ')') AS
    -> vend_title
    -> FROM vendors
    -> ORDER BY vend_name;

**使用不同类型的联结**
- 自联结
- 自然联结
- 外部联结

**自联结**
- 两张相同的表products
- 使用别名p1,p2
- 联结表
- 按p2过滤数据


> SELECT p1.prod_id, p1.prod_name
    -> FROM products AS p1, products AS p2
    -> WHERE p1.vend_id = p2.vend_id
    -> AND p2.prod_id = 'DTNTR';

**自然联结**

> SELECT c.*, o.order_num, o.order_date, oi.prod_id, oi.quantity, oi.item_price
    -> FROM customers AS c, orders AS o, orderitems AS oi
    -> WHERE c.cust_id = o.cust_id
    -> AND oi.order_num = o.order_num
    -> AND prod_id = 'FB';


**外部联结**

联结包含了那些在相关表中没有关联行的行
- OUTER JOIN: 指定联结类型
- 必须使用RIGHT或者LEFT关键字指定包括其所有行的表
- RIGHT :OUTER JOIN右边的表
- LEFT：OUTER JOIN左边的表
- 从from子句的左边表(customers)中选择所有行
- 
>  SELECT customers.cust_id, orders.order_num
    -> FROM customers LEFT OUTER JOIN orders
    -> ON customers.cust_id = orders.cust_id;


**使用带聚集函数的联结**

检索所有客户及每个客户所下的订单数，使用COUNT（）函数

- INNER JOIN将customers和orders两张表关联起来
- GROUP BY 按客户分组数据
- COUNT(orders.order_num)对每个客户的订单技术

>  SELECT customers.cust_name, customers.cust_id,
    -> COUNT(orders.order_num) AS num_ord
    -> FROM customers INNER JOIN orders
    -> ON customers.cust_id = orders.cust_id
    -> GROUP BY customers.cust_id;




### **组合查询**

**创建组合查询**


需要价格小于等于5的所有物品的一个列表，而且包括供应商1002和1001生产的所有物品      
  
 - union 操作符

> SELECT vend_id, prod_id, prod_price FROM products WHERE prod_price <= 5
    -> UNION
    -> SELECT vend_id, prod_id, prod_price FROM products WHERE vend_id IN (1001, 1002);

**包含或取消重复的行**

使用union all不取消重复行

> SELECT vend_id, prod_id, prod_price
    -> FROM products
    -> WHERE prod_price <= 5
    -> UNION ALL
    -> SELECT vend_id, prod_id, prod_price
    -> FROM products
    -> WHERE vend_id IN (1001, 1002);

**对组合查询结果排序**

- ORDER BY 不允许使用多条子句
- 组合查询中，虽然ORDER BY放在最后一条语句中，但实际上它可以排序所有SELECT语句

>  SELECT vend_id, prod_id, prod_price
    -> FROM products
    -> WHERE prod_price <= 5
    -> UNION
    -> SELECT vend_id, prod_id, prod_price
    -> FROM products
    -> WHERE vend_id IN (1001, 1002)
    -> ORDER BY vend_id, prod_price;
    
### **全文本搜索**

 Match()指定被搜索的列
 Against()指定要使用的搜索表达式


- Match(note_text)： 对note_text列进行搜索
- Against('rabbit')： 指定词rabbit作为搜索文本



> SELECT note_text
    -> FROM productnotes
    -> WHERE Match(note_text) Against('rabbit');

**实用查询扩展**
- with query expansion



>  SELECT note_text
    -> FROM productnotes
    -> WHERE Match(note_text) Against('anvils' WITH QUERY EXPANSION);


**布尔文本搜索**

 - IN BOOLEAN MODE

> SELECT note_text FROM productnotes WHERE Match(note_text) Against('heavy' IN BOOLEAN MODE);


匹配包含heavy但不包含以rope开始的行

- -排除一个词
- *截断操作符

> SELECT note_text FROM productnotes WHERE Match(note_text) Against('heavy -rope*' IN BOOLEAN MODE);



### **插入数据**

**插入完整的行**
- 存储到每个列表中的值在VALUES
> INSERT INTO customers
    -> VALUES(
    -> NULL,
    -> 'Pep E. LaPew',
    -> '100 Main Street',
    -> 'Los Angeles',
    -> 'CA',
    -> '90046',
    -> 'USA',
    -> NULL,
    -> NULL);
Query OK, 1 row affected (0.01 sec)


编写更加安全的INSERT语句

- 插入行时，用VALUES列表中的相应值填入列表中的对应项
>  INSERT INTO customers(cust_name,
    -> cust_address,
    -> cust_city,
    -> cust_state.
    -> cust_zip,
    -> cust_country,
    -> cust_contact,
    -> cust_email)
    -> VALUES('Pep E. La Pew',
    -> '100 Main Street',
    -> 'Los Angeles',
    -> 'CA',
    -> '90046',
    -> 'USA',
    -> NULL,
    -> NULL);

**插入多个行**
 - 列名相同的情况下
 
> INSERT INTO CUSTOMERS(cust_name,
    -> cust_address,
    -> cust_city,
    -> cust_state,
    -> cust_zip,
    -> cust_country)
    -> VALUES(
    -> 'treehl',
    -> 'baoshan',
    -> 'shanghai',
    -> 'sh',
    -> '0000',
    -> 'cn'
    -> ),
    -> (
    -> 'yang',
    -> 'baoshan',
    -> 'shanghai',
    -> 'sh',
    -> '1111',
    -> 'cn'
    -> );

**插入检索出的数据**

INSERT SELECT


> INSERT INTO customers (cust_id,
..............
)
SELECT cust_id,
...............
FROM ...;


### **更新和删除数据**

**更新数据**

UODATE
- 更新表中所有行
- 更新表中特定行


更新表customers
set命令用来将新值赋给被更新的列，set子句设置cust_email为指定的列
WHERE子句：告诉MySQL更新哪一行

> UPDATE customers
    -> SET cust_email = 'elmer@fudd.com'
    -> WHERE cust_id = 10005;

**使用NULL来删除某个列的值**

> UPDATE customers SET cust_email = NULL WHERE cust_id = 10005;

**删除数据**

DELETE
- 从表中删除特定的行
- 从表中删除所有行
- 不需要列名或通配符
- 删除整行而不是删除列


DELETE FROM -  指定从哪个表删除
WHERE- 子句过滤要删除的行

从customers表中只删除id为10006的
> DELETE FROM customers WHERE cust_id = 10006;


### **创建和操作表**

**表创建基础**

NOT NULL - 阻止插入没有值的列

> CREATE TABLE customers
    -> (
    ->  cust_id         int     NOT NULL AUTO_INCREMENT,
    ->  cust_name       char(50)NOT NULL,
    -> cust_address     char(50)NOT NULL,
    -> cust_city        char(50)NULL,
    -> cust_state       char(5) NULL,
    -> cust_zip         char(10)NULL,
    -> cust_country     char(50)NULL,
    -> cust_contact     char(50)NULL,
    -> cust_email       char(255)NULL,
    -> PRIMARY KEY (cust_id)
    -> )ENGINE=InnoDB

**使用NULL值**


**主键**

定义主键例子
> PRIMARY KEY(vend_id)

由多个列组成的主键

>  CREATE TABLE orderitems
    -> (
    ->  order_num       int             NOT NULL,
    ->  order_item      int             NOT NULL,
    ->  prod_id         char(10)        NOT NULL,
    ->  quantity        int             NOT NULL,
    ->  item_price      decimal(8, 2)   NOT NULL,
    ->  PRIMARY KEY     (order_num, order_item)
    -> )ENGINE=InnoDB;


**AUTO_INCREMENT**
auto_increment- 自动增量
- auto_increment告诉MySQL，本列每当增加一行时自动增量，执行一个INSERT操作，自动对该列增量
- 每个表只允许一个AUTO_INCREMENT
- 

> cust_id           int       NOT NULL   AUTO_INCREMENT


**指定默认值**
- default ；表示在没有给出数据时，默认就是1



> CREATE TABLE orderitems
    -> (
    ->  order_num       int             NOT NULL,
    ->  order_item      int             NOT NULL,
    ->  prod_id         char(10)        NOT NULL,
    ->  quantity        int             NOT NULL DEFAULT 1,
    ->  item_price      decimal(8, 2)   NOT NULL,
    ->  PRIMARY KEY (order_num, order_item)
    -> )ENGINE=InnoDB;
   
**引擎类型**
- InnoDB 一个可靠的事务处理引擎，它不支持全文本搜索
- MyISAM 一个性能极高的引擎，它支持全文本搜索，但不支持事务处理
- MEMORY 功能等同于MyISAM，但由于数据存储在内存中，速度很快（适合临时表）
> ENGINE=InnoDB


**更新表**

- ALTER TABLE + 表名

给vendors表增加vend_phone列，必须明确其数据char(20)

> ALTER TABLE vendors ADD vend_phone CHAR(20);

删除刚刚添加的列

- DROP COLUMN

> ALTER TABLE vendors DROP COLUMN vend_phone;


**删除表**
假设有这个表

>  DROP TABLE customers2;

**重命名表**

> RENAME TABLE customers2 TO customers;

重命名多个表

> RENAME TABLE backup_customers TO customers,
    -> backup_vendors TO vendors,
    -> backup_products TO products;


### **使用视图**

- 用CREATE VIEW来创建
- 使用SHOW CREATE VIEW viewname 来查看创建视图的语句
- 用DROP删除视图


**使用视图简化复杂的联结**

创建了一个名为productcustomers的视图，它联结三个表

> CREATE VIEW productcustomers AS
    -> SELECT cust_name, cust_contact, prod_id
    -> FROM customers, orders, orderitems
    -> WHERE customers.cust_id = orders.cust_id
    -> AND orderitems.order_num = orders.order_num;

检索订购了产品TNT2的客户
 - 它将指定的WHERE子句添加到试图查询中的已有的WHERE子句中，以便正确过滤数据

> SELECT cust_name, cust_contact FROM productcustomers WHERE prod_id = 'TNT2';

**用视图重新格式化检索出的数据**

创建视图
>  CREATE VIEW vendorlocations AS
    -> SELECT Concat(RTrim(vend_name), '(', RTrim(vend_country), ')') AS vend_title
    -> FROM vendors
    -> ORDER BY vend_name;

检索出以创建所有邮件标签的数据

> SELECT * FROM vendorlocations;

**用视图过滤不想要的数据**


定义customeremaillist视图，过滤没有邮箱的客户

> CREATE VIEW customeremaillist AS
    -> SELECT cust_id, cust_name, cust_email
    -> FROM customers
    -> WHERE cust_email IS NOT NULL;

检索由邮箱的客户

> SELECT * FROM customeremaillist;


**使用试图与计算字段**

检索某个特定订单中的物品，计算每种物品的总价格

>  CREATE VIEW orderitemsexpanded AS
    -> SELECT order_num, prod_id, quantity, item_price, quantity*item_price AS expanded_orice
    -> FROM orderitems;

检索订单20005的详细内容


> SELECT * FROM orderitemsexpanded WHERE order_num = 20005;

### **使用存储过程**

**执行存储过程**
执行名为productpricing的存储过程，它计算并返回产品的最低、最高和平均价格



> CALL productpricing(@pricelow,
								    @pricehigh,
									@priceaverage);



**创建存储过程**
 
 - 存储过程名为productpricing()
 - BEGIN END用来限定过程存储体
 - 这里SELECT语句结束后用分号有可能会报错
 - DELIMITER //   分隔符

>  DELIMITER //
mysql> CREATE PROCEDURE productpricing()
    -> BEGIN
    ->     SELECT Avg(prod_price) AS priceaverage
    ->     FROM products;
    -> END //
Query OK, 0 rows affected (0.00 sec)


使用存储过程
- 使用分隔符（DELIMITER // 后需要再输入 DELIMITER ;）


> CALL productpricing();


**删除存储过程**
>  DROP PROCEDURE productpricing;


**使用参数**

- decimal 十进制
- pl 存储最低价
- ph 存储最高价
- pa 存储产品平均价
- OUT 指出相应的参数用来从存储过程传出一个值
- 存储过程的代码位于BEGIN和END之间
- 指定INTO保存到相应的变量



> DELIMITER //
mysql> CREATE PROCEDURE productpricing(
    ->     OUT pl DECIMAL(8,2),
    ->     OUT ph DECIMAL(8,2),
    ->     OUT pa DECIMAL(8,2)
    -> )
    -> BEGIN
    ->     SELECT Min(prod_price)
    ->     INTO pl
    ->     FROM products;
    ->     SELECT Max(prod_price)
    ->     INTO ph
    ->     FROM products;
    ->     SELECT Avg(prod_price)
    ->     INTO pa
    ->     FROM products;
    -> END //


为调用此修改过的存储过程，必须指定三个变量名

- 所有MySQL变量名都必须以@开始

>  CALL productpricing(@pricelow,
    -> @pricehigh,
    -> @priceaverage);

检索出产品的平均价格
> SELECT @priceaverage;

检索平均价格，最高价格，最低价格

> SELECT @pricehigh, @pricelow, @priceaverage;

 IN 和OUT参数
- onumber定义为IN
- ototal定义为OUT
- where子句使用onumber选择正确的行
- INTO使用ototal存储计算出来的合计


 ordertotal接受订单并返回该订单的合计

- onumber定义为IN
- ototal定义为OUT
- where子句使用onumber选择正确的行
- INTO使用ototal存储计算出来的合计


>  CREATE PROCEDURE ordertotal(
    ->     IN onumber INT,
    ->     OUT ototal DECIMAL(8,2)
    -> )
    -> BEGIN
    ->     SELECT Sum(item_price*quantity)
    ->     FROM orderitems
    ->     WHERE order_num = onumber
    ->     INTO ototal;
    -> END //




调用新存储过程
- 第一个参数为订单号
- 第二个参数为包含计算出来的合计的变量名



> CALL ordertotal(20005, @total);


显示合计

> SELECT @total;

**建立智能存储过程**


**检查存储过程**

- 获得何时、由谁创建等详细信息的存储过程列表

>  SHOW CREATE PROCEDURE ordertotal;

### **使用游标**

游标（cursor）：是一个存储在MySQL服务器上的数据库查询


**创建游标**
定义了ordernumbers的游标

- DECLARE语句定义和命名游标ordernumbers
- 存储过程处理完成后，游标就消失（因为它仅限于存储过程）



>  CREATE PROCEDURE processorders()
    -> BEGIN
    ->     DECLARE ordernumbers CURSOR
    ->     FOR
    ->     SELECT order_num FROM orders;
    -> END //


**打开或关闭游标**

> OPEN ordernumbers;

> CLOSE ordernumbers;

**使用游标数据**

- FETCH语句分别访问它的每一行
 
从游标中检索单个行

> CREATE PROCEDURE

### **使用触发器**

**创建触发器**

CREATE TRIGGER - 建立触发

>  CREATE TRIGGER newproduct AFTER INSERT ON products
    -> FOR EACH ROW SELECT 'Product added';


**删除触发器**


>  DROP TRIGGER newproduct;


### **管理事务处理**

 - InnoDB引擎支持事务处理管理

- transaction 事务处理 指一种SQL语句
- rollback 回退 指撤销指定SQL语句的过程
- commit 提交 指将未存储的SQL语句结果写入数据库表
- savepoint 保留点 指事务处理中设置的临时占位符
- START TRANSACTION 标识事务的开始


**使用ROLLBACK**

> SELECT * FROM ordertotals;
> START TRANSACTION;
> DELETE FROM ordertotals;
> SELECT * FROM ordertotals;
> ROLLBACK;
> SELECT * FROM ordertotals;

1. 执行一条select语句显示该表不为空
2. 开始食物处理
3. 删除表中所有的行
4. select 验证表确实为空
5. 回退到START TRANSACTION之后的所有语句
6. 最后一条select显示该表不为空


**使用COMMIT**



**使用保留点**
> SAVEPOINT delete1;


**更改默认的提交行为**
- autocommit 标志决定是否自动提交更改

> SET autocommit=0;




### **全球化和本地化**


**使用字符集和校对顺序**

> SHOW CHARACTER SET;


查看所支持校对的完整列表

> SHOW COLLATION


给表指定字符集和校对

- 创建一个包含两个列的表
- 指定一个字符集
- 制定一个校对顺序




>  CREATE TABLE mytable
    -> (
    ->     column1    INT,
    ->     column2    VARCHAR(10)
    -> ) DEFAULT CHARACTER SET hebrew
    ->   COLLATE hebrew_general_ci;


### **安全管理**


获得所有用户账号的列表

>  USE mysql

>  SELECT user FROM user;
>

**创建用户账号**

- identified 确定

> CREATE USER treehl IDENTIFIED BY '密码';

重新命名一个帐户
> RENAME USER treehl TO treehl1;


**删除用户账号**


> DROP USER treehl1;


**设置访问权限**
查询用户权限

>  SHOW GRANTS FOR treehl;

![](http://m.qpic.cn/psb?/V10WDaE22S84Sl/z4CNoKaxCQkf3IbWcXEu1C7T.ZneiHUclLVaC088vZU!/b/dDIBAAAAAAAA&bo=qAGPAAAAAAADBwQ!&rf=viewer_4)


USAGE表示没有权限

用GRANT设置权限

> GRANT SELECT ON MySQL_test.* TO treehl;

显示更改后的权限


> show grants for treehl;

撤销权限

>  REVOKE SELECT ON MySQL_test.* FROM treehl;


GRANT和REVOKE可在几个层次上控制访问权限


1. 整个服务器，GRANT ALL REVOKE ALL
2. 整个数据库 使用ON databases.*
3. 特定的表 使用ON database.table

**更改口令**

> SET PASSWORD FOR treehl = Password('密码');


### **数据库维护**

刷新数据

>  FLUSH TABLES;

**进行数据库维护**

analyze table - 分析表


>  ANALYZE TABLE orders;

check table - 检查表


> CHECK TABLE ORDERS, ORDERITEMS;


**争端启动问题**

mysqld


### **改善性能**
 查看所有活动进程
 > SHOW PROCESSLIST;

查看当前设置

> SHOW VARIABLES;
> SHOW STATUS;

[GitHub](https://github.com/Family-TreeSY)
欢迎访问博客[Treehl的博客](https://family-treesy.github.io/)


