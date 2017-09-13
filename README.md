# MYSQL

## 来自《MySQL必知必会》书籍上的全部代码，自己手写一遍


--tips为有用的知识点(来自网上搜索或书籍)


```SQL
--常用操作

--用这个删除table
DROP TEMPORARY TABLE IF EXISTS temptablename;
drop table if exists tableName

---治疗乱码--因为插入的和显示不一致

SET CHARSET GBK

---查看表结构-源码

show create table tableName\G

---查看视图的结构--源码
SHOW CREATE VIEW viewname;

---查看存储过程的源码

show create procedure procedurename;

---查看数据库

show databases;

---使用数据库

use databaseName;


--更改结束符号原来是;可以改为其它的，在存储过程的时候需要使用

delimiter $--将结束符定义为$


--以表格的形式查看视图或表的
desc tableName|viewname;



--查看数据库中所有存储的存储过程的基本信息，包括数据库，存储过程的信息，创建时间等信息
show procedure status;

--查看数据库中各个表的信息
show table status;

--查看数据库中视图的信息---侧面反映出视图的不存在--视图是特殊的表
show table stauts where comment='view';


------临时表与内存表

---tips

1.
    临时表是建立在系统临时文件夹中的表，如果使用得当，完全可以像普通表一样进行各种操作。 
    临时表的数据和表结构都储存在内存之中，退出时，其所占的空间会自动被释放。
2.
    show tables 语句不会列举临时表，内存表可以看到

--创建临时表---临时表默认的是MyISAM,但是可以修改。

CREATE temporary table tempTableName select ..... from tableName .....


CREATE temporary table www(
    name char(19),
    age int
);
insert into www values('wzl',1212);

---删除临时表

drop table tempTableName;

-------内存表-临时表的一种

---tips
1.
    heap不允许使用xxxTEXT和xxxBLOB数据类型
2.
    内存表使用哈希散列索引把数据保存在内存中，因此具有极快的速度，适合缓存中小型数据库。
    heap对所有用户的连接是可见的，这使得它非常适合做缓存
3.
    在任何查询之前，执行一次简单的查询，判断heap表是否存在数据，如果不存在，则把数据重新写入，
    或者DROP表重新复制某张表。这需要多做一次查询。不过
    
    可以写成include文件，
    在需要用该heap表的页面随时调用，比较方便。 
4.
    内存表必须使用memory存储引擎
5.
    每个基于memory存储引擎的表实际对应一个磁盘文件，该文件的文件名与表名相同，类型为frm类型。
    该文件只存储表的结构，而其数据文件，都是存储在内存中的，这样有利于对数据的快速的处理，
    提高整个表的处理效率。
6.
    show tables --可见

CREATE table wzl(
    age int,
    name char(10)
)type=heap;
insert into wzl VALUES(11221,'wzlll');



----mysql取整方式
--tips
1.
    select cast(19.16558 as decimal(9,2))精确到几位
2.
    select round(123.5); 四舍五入
3.
    select floor(123.5);取整数部分
4.
    select ceil(123.5);四舍五入





------单例检索-----
SELECT PROD_NAME FROM PRODUCTS;
---------------------------------------
------多列检索-----
SELECT PROD_ID,PROD_NAME,PROD_PRICE FROM PRODUCTS;
--------------------------------------
------检索所有列----
SELECT * FROM PRODUCTS;
--------------------------------------
------去重复-------
SELECT vend_id from PRODUCTS;
SELECT  DISTINCT vend_id FROM PRODUCTS;
---------------------------------------
-------限制结果
select PROD_NAME FROM PRODUCTS LIMIT 5;
------------------------

------------------- 第五章---排序检索数据----------
SELECT PROD_NAME FROM PRODUCTS;

SELECT PROD_NAME FROM PRODUCTS ORDER BY PROD_NAME;
-----按多列排序
--先按prod_price排序，在按prod_name排序
SELECT PROD_ID,PROD_PRICE,PROD_NAME FROM PRODUCTS ORDER BY PROD_PRICE,PROD_NAME;

------指定排序方向
---DESC从大到小排序(默认为ace)
SELECT PROD_ID,PROD_PRICE,PROD_NAME FROM PRODUCTS ORDER BY PROD_PRICE DESC;

----第六章 过滤数据---
SELECT PROD_NAME,PROD_PRICE FROM PRODUCTS WHERE PROD_PRICE=2.5;

--检查单个值

SELECT PROD_NAME,PROD_PRICE FROM PRODUCTS WHERE PROD_NAME='FUSES';

--不匹配检查

SELECT vend_id,PROD_NAME FROM PRODUCTS WHERE vend_id <> 1003;
SELECT vend_id,PROD_NAME FROM PRODUCTS WHERE vend_id != 1003;

--范围值检查
SELECT PROD_NAME,PROD_PRICE FROM PRODUCTS WHERE PROD_PRICE BETWEEN 5 AND 10;

--空值检查

SELECT PROD_NAME FROM PRODUCTS WHERE PROD_PRICE IS NULL;

---数据过滤
--AND

SELECT PROD_ID,PROD_PRICE,PROD_NAME FROM PRODUCTS 
    WHERE vend_id=1003 AND PROD_PRICE<=10;

--OR

SELECT PROD_NAME,PROD_PRICE FROM PRODUCTS 
    WHERE vend_id =1002 OR vend_id=1003;

(AND的优先级大于OR所以先执行后面的结果)
SELECT PROD_NAME,PROD_PRICE FROM PRODUCTS 
    WHERE vend_id=1002 OR vend_id=1003 AND PROD_PRICE>=10;

SELECT PROD_NAME,PROD_PRICE FROM PRODUCTS 
    WHERE (vend_id=1002 OR vend_id=1003) AND PROD_PRICE>=10;

-- IN

SELECT PROD_NAME,PROD_PRICE FROM PRODUCTS 
    WHERE vend_id IN(1002,1003) ORDER BY PROD_NAME;


-----第八章--用通配符进行过滤

-------LIKE
--%任意次数
SELECT PROD_ID,PROD_NAME FROM PRODUCTS WHERE PROD_NAME LIKE 'JET%';

SELECT PROD_ID,PROD_NAME FROM PRODUCTS WHERE PROD_NAME LIKE '%ANVIL%';

SELECT PROD_ID,PROD_NAME FROM PRODUCTS WHERE PROD_NAME LIKE 'S%E';

--_匹配一个

SELECT PROD_ID,PROD_NAME FROM PRODUCTS WHERE PROD_NAME LIKE '_ TON ANVIL';


----创建计算字段

--Concat()拼接字段

SELECT Concat(VEND_NAME,' (',VEND_COUNTRY,' )') FROM VENDORS ORDER BY VEND_NAME;

--RTRIM()删除右侧多余空格
SELECT Concat(RTRIM(VEND_NAME),' (',RTRIM(VEND_COUNTRY),' )') FROM VENDORS  
    ORDER BY VEND_NAME;

--使用别名
SELECT Concat(RTRIM(VEND_NAME),' (',RTRIM(VEND_COUNTRY),')') AS VEND_TITLE 
    FROM VENDORS   ORDER BY VEND_NAME;

--执行算数计算

SELECT PROD_ID,quantity,item_price FROM orderitems where order_num=20005;

SELECT PROD_ID,quantity,item_price,quantity*item_price AS EXPANDED_PRICE FROM orderitems WHERE order_num=20005;

--第十一章 使用数据处理函数

--Upper() 小写转大写
SELECT VEND_NAME,Upper(VEND_NAME) AS VEND_NAME_UPCASE FROM VENDORS ORDER BY VEND_NAME;

--Soundex()模糊查询，匹配发音相同的
SELECT CUST_NAME,CUST_CONTACT FROM CUSTOMERS 
    WHERE Soundex(CUST_CONTACT) =Soundex('Y LIE');

--日期和时间处理 
SELECT CUST_ID,order_num FROM ORDERS WHERE ORDER_DATE='2005-09-01';
SELECT CUST_ID,order_num FROM ORDERS WHERE DATE(ORDER_DATE)='2005-09-01';--更靠谱

--查找2005-09-01到2005-09-30
SELECT CUST_ID,order_num FROM ORDERS where DATE(ORDER_DATE) BETWEEN '2005-09-01' AND '2005-09-30';

--查找2005年的9月
SELECT CUST_ID,order_num FROM ORDERS 
    WHERE Year(ORDER_DATE) =2005 AND Month(ORDER_DATE)=9;

-----------第十二章-汇总数据
--聚集函数

--AVG()平均数
SELECT AVG(PROD_PRICE) AS AVG_PRICE FROM PRODUCTS;

--COUNT()
SELECT COUNT(*) AS NUM_CUST FROM CUSTOMERS;

SELECT COUNT(CUST_EMAIL) AS NUM_CUST FROM CUSTOMERS;

--MAX() 最大值

SELECT MAX(PROD_PRICE) AS MAX_PRICE FROM PRODUCTS;

--MIN()最小值

SELECT MIN(PROD_PRICE) AS MIN_PRICE FROM PRODUCTS;

--SUM()求和

SELECT SUM(quantity) AS ITEMS_ORDERED FROM orderitems WHERE order_num=20005;

SELECT SUM(item_price*quantity) AS TOTAL_PRICE FROM orderitems WHERE ORDER_NUM=20005;

--聚集不同的值
SELECT AVG(DISTINCT PROD_PRICE) AS AVG_PRICE FROM PRODUCTS WHERE vend_id=1003;

--组合聚集函数
SELECT COUNT(*) AS NUM_ITEMS,MIN(PROD_PRICE) AS PRICE_MIN,MAX(PROD_PRICE) AS PRICE_MAX,AVG(PROD_PRICE) AS PRICE_AVG FROM PRODUCTS;


----第十三章--分组数据

--数据分组


SELECT COUNT(*) AS NUM_PRODS FROM PRODUCTS WHERE vend_id=1003;

--创建分组

SELECT VEND_ID,COUNT(*) AS NUM_PRODS FROM PRODUCTS GROUP BY vend_id;

--分组后并且计算分了多少组的
SELECT VEND_ID,COUNT(*) AS NUM_PRODS 
    FROM PRODUCTS 
    GROUP BY vend_id with rollup;
--------------

--GROUP BY 必须出现在where 子句后面 order by 子句之前
--如果分组列中含有NULL 则NULL作为一个分组存在 

--------------



--过滤分组

--GROUP BY 与Having 连用
SELECT CUST_ID,COUNT(*) AS ORDERS FROM ORDERS 
    GROUP BY CUST_ID 
    HAVING COUNT(*)>=2;



---tips
1.
    HAVING:过滤分组
    WHERE： 过滤行
2.
    where 在数据分组前进行过滤
    having 在数据分组后进行过滤

    WHERE 排除的行不包括在分组中，这可能会改变计算值，从而影响 having 子句中基于这些值得过滤掉的分组
-------------------------------------------------------------------------------------

---同时使用where与having

--具有2个以上，价格在10以上的产品供应商
SELECT VEND_ID,COUNT(*) AS NUM_PRODS FROM PRODUCTS 
    WHERE PROD_PRICE>=10 
    GROUP BY VEND_ID 
    HAVING COUNT(*) >= 2;

--没有where的
SELECT VEND_ID,COUNT(*) AS NUM_PRODS FROM PRODUCTS  
    GROUP BY VEND_ID 
    HAVING COUNT(*) >= 2;


--分组与排序

1. 
    order by 排序
    GROUP by 分组行

--没有使用order by的分组
SELECT ORDER_NUM,SUM(quantity*item_price) AS ORDERTOTAL 
FROM orderitems
GROUP BY ORDER_NUM
HAVING SUM(quantity*item_price)>=50;

--使用order by的分组
SELECT ORDER_NUM,SUM(quantity*item_price) AS ORDERTOTAL 
FROM orderitems
GROUP BY ORDER_NUM
HAVING SUM(quantity*item_price)>=50
ORDER BY ORDERTOTAL;

----------------------------------

--tips

1.SELECT  要返回的列的表达式
2.FROM  要检索的表
3.WHERE 行级过滤
4.GROUP BY 分组说明
5.HAVING 组级过滤
6.ORDER BY 输出顺序
7.LIMIT 要检索的行数

--------------------------------------




-------------------------------------第十四章----使用子查询-


------------------利用子查询进行过滤

---查询订购物品TNT2的所有客户

1.检索包含物品TNT2的所有订单编号
2.检索具有1中列出的订单编号的户

@1.
    SELECT ORDER_NUM FROM orderitems WHERE PROD_ID='TNT2';

@1.
    SELECT CUST_ID FROM ORDERS
    WHERE ORDER_NUM IN(
        SELECT ORDER_NUM FROM orderitems WHERE PROD_ID='TNT2'
    );


--查询订购物品TNT2的所有客户，并且查询客户的信息

@3.
    SELECT CUST_NAME,CUST_CONTACT FROM CUSTOMERS 
    WHERE CUST_ID IN(
        SELECT CUST_ID FROM ORDERS
            WHERE ORDER_NUM IN(
                SELECT ORDER_NUM FROM orderitems WHERE PROD_ID='TNT2'
        )
    );



-----------------作为计算字段使用子查询

---假设需要显示customers表中每个客户的订单总数。订单与相应的客户ID存储在orders表中
1.从customers表中检索客户列表
2.对于检索出的每个客户，统计其中在orders表中的订单数目

--下面的代码对客户10001的订单进行计数

@1.
    SELECT COUNT(*) AS ORDERS FROM ORDERS WHERE CUST_ID=10001;
@2.
    SELECT CUST_NAME,CUST_STATE,(
        SELECT COUNT(*) FROM ORDERS 
        WHERE ORDERS.CUST_ID=CUSTOMERS.CUST_ID 
        ) AS ORDERS
    FROM CUSTOMERS ORDER BY CUST_NAME;




----------------------------------第十五章-------联接表

--在表vendors和products中查询vend_id相同的vend_name,prod_id,prod_price

@1
SELECT VEND_NAME,PROD_NAME,PROD_PRICE FROM VENDORS,PRODUCTS
    WHERE VENDORS.VEND_ID=PRODUCTS.VEND_ID
    ORDER BY VEND_NAME,PROD_NAME;

--没有where时返回的是笛卡儿积
SELECT VEND_NAME,PROD_ID,PROD_PRICE FROM VENDORS,PRODUCTS 
    ORDER BY VEND_NAME,PROD_NAME;


----内部联结
@2-- 与@1等效
SELECT VEND_NAME,PROD_NAME,PROD_PRICE FROM VENDORS
    INNER JOIN PRODUCTS ON VENDORS.VEND_ID=PRODUCTS.VEND_ID;


----联结多个表
SELECT PROD_NAME,VEND_NAME,PROD_PRICE,quantity FROM orderitems,PRODUCTS,VENDORS
    WHERE PRODUCTS.VEND_ID=VENDORS.VEND_ID 
    AND orderitems.prod_id=PRODUCTS.PROD_ID
    AND ORDER_NUM=20005;




--------------------------------第十六章-----创建高级联结


----自联结
@1
SELECT PROD_ID,PROD_NAME FROM PRODUCTS 
    WHERE VEND_ID=(
        SELECT VEND_ID FROM PRODUCTS WHERE PROD_ID='DTNTR'
    );

@2与上面的@1等效
SELECT P1.PROD_ID,P1.PROD_NAME FROM PRODUCTS AS P1,PRODUCTS AS P2
    WHERE P1.VEND_ID=P2.VEND_ID AND P2.PROD_ID='DTNTR';


----自然联结

--------------------空----

@1--内联结
SELECT CUSTOMERS.CUST_ID,ORDERS.ORDER_NUM FROM CUSTOMERS 
    INNER JOIN ORDERS ON CUSTOMERS.CUST_ID=ORDERS.CUST_ID;

@2--左外联结
SELECT CUSTOMERS.CUST_ID,ORDERS.ORDER_NUM FROM CUSTOMERS 
    LEFT OUTER JOIN ORDERS ON CUSTOMERS.CUST_ID=ORDERS.CUST_ID;

@3--右外联结
SELECT CUSTOMERS.CUST_ID,ORDERS.ORDER_NUM FROM CUSTOMERS 
    right OUTER JOIN ORDERS ON CUSTOMERS.CUST_ID=ORDERS.CUST_ID;



----使用带聚集函数的联结

--检索所有客户及每个客户所下的订单数
SELECT CUSTOMERS.CUST_NAME,CUSTOMERS.CUST_ID,COUNT(ORDERS.ORDER_NUM) AS NUM_ORD
    FROM CUSTOMERS INNER JOIN ORDERS ON CUSTOMERS.CUST_ID=ORDERS.CUST_ID
    GROUP BY CUSTOMERS.CUST_ID;

SELECT CUSTOMERS.CUST_NAME,CUSTOMERS.CUST_ID,COUNT(ORDERS.ORDER_NUM) AS NUM_ORD
    FROM CUSTOMERS LEFT JOIN ORDERS ON CUSTOMERS.CUST_ID=ORDERS.CUST_ID
    GROUP BY CUSTOMERS.CUST_ID;




----------------第十七章--组合查询

----创建组合查询
-- 使用UNION
--假设需要价格小于等于5的所有物品的一个列表，而且还想包括供应商1001和1002生产的所有物品(不考虑价格)。
@1
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE PROD_PRICE<=5;

@2 
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE VEND_ID IN(1001,1002);


@1 @2
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE PROD_PRICE<=5
    UNION
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE VEND_ID IN(1001,1002);



@3 使用where等效
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE PROD_PRICE<=5 OR VEND_ID IN (1001,1002);



---UNION--tips

1.
    UNION必须是两条或两条以上的select语句组成

2. 
    UNION中的每个查询必须包含相同的列，表达式，或集聚函数
3. 
    列数据类型必须兼容


----包含或取消重复的行

--UNION ALL不会取消重复的行
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE PROD_PRICE<=5
    UNION ALL
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE VEND_ID IN(1001,1002);


----对组合查询结果排序
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE PROD_PRICE<=5
    UNION ALL
SELECT VEND_ID,PROD_ID,PROD_PRICE FROM PRODUCTS
    WHERE VEND_ID IN(1001,1002)
    ORDER BY VEND_ID,PROD_PRICE;




---------------第十八章--全文搜索

--tips
1.只有MyISAM支持

-----进行全文本搜索
@1
SELECT NOTE_TEXT FROM PRODUCTNOTES  WHERE MATCH(NOTE_TEXT) AGAINST('rabbit');
@2 与@1等效
SELECT NOTE_TEXT FROM PRODUCTNOTES WHERE NOTE_TEXT LIKE '%RABBIT%';


SELECT NOTE_TEXT,MATCH(NOTE_TEXT) AGAINST('RABBIT') AS RANK FROM PRODUCTNOTES;

---换个显示方式
SELECT NOTE_TEXT,MATCH(NOTE_TEXT) AGAINST('RABBIT') AS RANK FROM PRODUCTNOTES\G

--tips
1.
    加了\G后会改变显示方式，且不需要分号
    Mysql----error:no query specified
    mysql下抛出错误：
        error:no query specified
    出现此错误是sql不合法原因：
    如：select * from abc\G;
    或者
    select * from abc;;
    \G后面不需要再加分号
------使用查询扩展

SELECT NOTE_TEXT FROM PRODUCTNOTES WHERE MATCH(NOTE_TEXT) AGAINST('anvils');

SELECT NOTE_TEXT FROM PRODUCTNOTES WHERE MATCH(NOTE_TEXT) AGAINST('anvils' with QUERY EXPANSION);

---布尔文本搜索
SELECT NOTE_TEXT FROM PRODUCTNOTES WHERE MATCH(NOTE_TEXT) AGAINST('heavy' IN boolean Mode);

SELECT NOTE_TEXT from PRODUCTNOTES where MATCH(NOTE_TEXT) AGAINST('heavy -rope*' IN boolean mode);

SELECT NOTE_TEXT FROM PRODUCTNOTES WHERE MATCH(NOTE_TEXT) AGAINST('+rabbit +bait' IN boolean mode);

-----------第十九章---插入数据

----插入完整的行

INSERT INTO CUSTOMERS VALUES(
    NULL,
    'Pep E. LaPew',
    '100 Main Street',
    'Los Angeles',
    'CA',
    '90046',
    'USA',
    NULL,
    NULL);

---tips
1. 因为cust_id是子增长类型 所以当你不想指定是设为nullmysql会自动填写


INSERT INTO CUSTOMERS(
    CUST_NAME,
    CUST_CONTACT,
    CUST_EMAIL,
    CUST_ADDRESS,
    CUST_CITY,
    CUST_STATE,
    CUST_ZIP,
    CUST_COUNTRY)
VALUES(
     'Pep E. LaPew',
     NULL,
     NULL,
    '100 Main Street',
    'Los Angeles',
    'CA',
    '90046',
    'USA'
);


----------------------第二十章----更新和删除数据


--更新数据
UPDATE CUSTOMERS SET CUST_EMAIL='ELMER@FUDD.COM' WHERE CUST_ID=10005;

UPDATE CUSTOMERS SET CUST_NAME='THE FUDDS',CUST_EMAIL='ELMER@.UDD.COM' WHERE CUST_ID=10005;

UPDATE CUSTOMERS SET CUST_EMAIL=NULL WHERE CUST_ID=10005;


--删除数据
DELETE FROM CUSTOMERS WHERE CUST_ID=10006;

--


------------------------第二十一章--创建和操纵表

CREATE TABLE orderitems
(
  order_num  int          NOT NULL ,
  order_item int          NOT NULL ,
  prod_id    char(10)     NOT NULL ,
  quantity   int          NOT NULL ,
  item_price decimal(8,2) NOT NULL ,
  PRIMARY KEY (order_num, order_item)
) ENGINE=InnoDB;

CREATE TABLE orders
(
  order_num  int      NOT NULL AUTO_INCREMENT,
  order_date datetime NOT NULL ,
  cust_id    int      NOT NULL ,
  PRIMARY KEY (order_num)
) ENGINE=InnoDB;

ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_orders FOREIGN KEY (order_num) REFERENCES orders (order_num);






















---------第二十二章--使用视图

---tips

1.
    重用SQL语句
2.
    简化复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节。
3.
    使用表的组成部分而不是整个表
4.
    保护数据。可以给用户授权表的特定部分的访问权限而不是整个表的访问权限。
5.
    更改数据格式和表示。视图可以返回与底层表的表示和格式不同的数据。

6.
    视图不能有索引，可以和表连用


---利用视图简化复杂的联结

--创建视图
CREATE VIEW productcustomers AS
    SELECT CUST_NAME,CUST_CONTACT,PROD_ID FROM CUSTOMERS,ORDERS,orderitems
    WHERE CUSTOMERS.CUST_ID=ORDERS.CUST_ID AND orderitems.ORDER_NUM=ORDERS.ORDER_NUM;


--查看视图
SHOW CREATE VIEW productcustomers;



---用视图重新格式化检索出的数据

@1
SELECT Concat(RTRIM(VEND_NAME),' (',RTRIM(VEND_COUNTRY),')') AS VEND_TITLE
        FROM VENDORS ORDER BY VEND_NAME;


@2
CREATE VIEW VENDORLOCATIONS AS 
    SELECT Concat(RTRIM(VEND_NAME),' (',RTRIM(VEND_COUNTRY),')') AS VEND_TITLE
        FROM VENDORS ORDER BY VEND_NAME;

---用视图过滤不想要的数据
CREATE VIEW CUSTOMEREMAILLIST AS
    SELECT CUST_ID,CUST_NAME,CUST_EMAIL FROM CUSTOMERS WHERE CUST_EMAIL IS NOT NULL;


--查看各个视图
show table status where comment='view'; 

--删除视图
drop view if EXISTS CUSTOMEREMAILLIST;



--------第二十三章---使用存储过程--可玩性高


-----tips
1.
    存储过程简单来说，就是为以后的使用而保存的一条或多条MySQL语句的集合

2.
    通过把处理封装在容易使用的单元中，简化复杂的操作
3.
    由于不要求反复建立一系列处理步骤，这保证了数据的完整性。如果所有开发人员和应用程序都使用同一存储过程，
    则所使用的代码都是相同的。
    这一点就是防止错误。需要执行的步骤越多，出错的可能性越大
4.
    简化对变动的管理。如果表名，列名或业务逻辑有变化，只需要更改存储过程的代码。使用它的人员不需要知道这些变化
    隔离，安全。



--缺点：
1.
    代码量大，复杂


---创建存储过程
delimiter //
create procedure productpricing()
begin
    select avg(prod_price) as proceaverage from PRODUCTS;
end //
delimiter ;--改回来，为了符合习惯,不然就得以//结尾
---调用存储过程
call productpricing();

---删除存储过程 定义是用的//所以在删除时也要用//结尾
drop procedure productpricing;


---使用参数--1  OUT示例
@1.
delimiter $
create procedure productpricing(
    out pl decimal(8,2),
    out ph decimal(8,2),
    out pa decimal(8,3)
)
begin
    select min(prod_price) INTO pl from PRODUCTS;
    select max(prod_price) into ph from PRODUCTS;
    select avg(prod_price) into pa from PRODUCTS;
end $
@2.更改分界符
delimiter ;

@3. 为了调用此存储过程，必须指定三个变量名
call productpricing(@pricelow,@pricehigh,@priceaverge);

@4.
select @priceaverge;

@5.
SELECT @pricehigh,@pricelow,@priceaverge;

---tips

pl:存储产品的最低价格
ph：存储最高价格
pa：存储产品平均价格

1.MySQL支持IN(传递给存储过程),OUT(从存储过程传出),INOUT(对存储过程的传入传出)


---使用参数--2 IN---OUT 示例
@1
delimiter $
create procedure ordertotal(
    IN onnumber int,
    out ototal decimal(8,3)
)
begin 
    select Sum(item_price*quantity) from orderitems
    where order_num=onnumber into ototal;

end$
@1.1 --等同
delimiter $
create procedure ordertotal(
    IN onnumber int,
    out ototal decimal(8,3)
)
begin  --into ototal可以在后面也可以在前面，紧跟查找参数 
    select Sum(item_price*quantity) into ototal from orderitems
    where order_num=onnumber;
end$

@2.
delimiter ;

@3.
call ordertotal(20005,@total);--必须用@开头
SELECT @total;

@4.
call ordertotal(20009,@www);
select @www;




---使用参数--3

delimiter $
create procedure test(n int)
begin 
    select * from orderitems where order_num=n;
end $

delimiter ;

call test(20005);







---建立只能存储过程
场景：你需要获得与以前一样的订单合计,但需要要对合计增加营业税，不过只针对某些顾客
1.获得合计
2.把营业税有条件的添加到合计
3.返回合计(带或不带税)

comment 后面加的是说明性文字


boolean 非零为真


delimiter !

CREATE procedure ordertotal(
    IN onumber int,
    in taxable boolean,
    out ototal decimal(8,2)
)comment 'tax'
begin   
    --申明局部变量total
    declare total decimal(8,2);
    --申明局部变量taxrate 默认为6
    declare taxrate int default 6;

    --获取合计total

    select sum(item_price*quantity) into total from orderitems 
        where order_num=onumber;

    --taxable是否存在
    if taxable then
        --存在就更新total
        SELECT total+(total/100*taxrate) into total;
    end if;
    --最后把total赋值给ototal
    select total into ototal;
end! 

delimiter ;


call ordertotal(20005,0,@total);

select @total;


--查看存储过程源码
show create procedure ordertotal;




------------第二十四章---使用游标

--tips

1.MySQL只能用于存储过程(和函数)



使用方法:
1.
    先申明游标
2.
    打开游标

3.
    结束后必须关闭游标


----创建游标
 

delimiter !

create procedure processorder()
begin
    --申明局部变量    
    declare done boolean default 0;
    declare o int;
    declare t decimal(8,2);

    --申明游标
    declare ordername cursor 
    for 
    select order_num from ORDERS;

    --当sqlstate为空时done=1
    declare continue handler for sqlstate '02000' set done=1;

    --创建表
    create table if not exists ordertotals(
        order_num int,
        total decimal(8,2)
    );
    --打开游标
    open ordername;
    --循环
    repeat
        fetch ordername into o;

        call ordertotal(o,1,t);

        INSERT into ordertotals(order_num,total) values(o,t);

    until done end repeat;
    --关闭游标
    close ordername; 
end!

delimiter ;

call processorder();

select * from ordertotals;



----------第二十五章---使用触发器

--MySQL5.0后支持触发器

----tips

1.
    触发器名唯一(每个表内唯一，数据库内不一定,可以在不同表中有相同的触发器)
2.
    触发器在执行 DELETE UPDATE INSERT 触发的(执行前或执行后)

3.
    只有表支持触发器，视图不支持，临时表也不支持


CREATE TRIGGER NEWORDER AFTER INSERT ON ORDERS FOR EACH ROW 
    begin
        INSERT INTO archive_orders(order_num,ORDER_DATE,CUST_ID)
        VALUES(OLD.order_num,OLD.ORDER_DATE,OLD.CUST_ID);
    end;





------第二十六章--管理事务处理

---MySQL

------tips--

1.
    InnoDB 支持事务处理与外键和行级锁
2.
    MyISAM不支持事务，表锁

---使用ROLLBack

select * from ordertotals;
--开启事务
start transaction;
delete from ordertotals;
select * from from ordertotals;
--回滚
rollback;
select * from ordertotals;



---使用COMMiT--但事务中有一条没有执行，就无法提交

start transaction;
delete from orderitems where order_num=20010;
delete from roders where order_num=20010;
COMMiT;





---部分回退

savepoint delete1；

rollback to delete1；

---tip
1.
    保留点可以很多,只要执行一条ROLLBACK就会释放所有的保留点



----更改默认的提交行为i
set autocommit=0;
关闭默认的自动提交。



----------------第二十七章--全球化和本地化

--查看字符集
show character set；

--校对完整的列表
SHOW COLLTION;

show variables like 'character%';

show variables LIKE 'collation%';


---------------第二十八章-----安全管理

---管理用户

--用户表在mysql中--user表中

use mysql;
select * from user;




--创建用户
create user ben indentified by "password";


--更改用户名为ben的用户

rename user ben to  newname;


--删除用户

drop user ben;


--查看用户的访问权限
show grant for ben;

--颁发权限

grant select on databaseName.tableName to username;


---更改口令


set password for username=Password('newpassword');



---------第二十九章--数据库维护

--检查表是否准确

analyze table tablename;

```