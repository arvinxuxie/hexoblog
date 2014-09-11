title: Mysql tutorial
tags: database
---
##Introduction to MySQL

<!--more-->
###Definitions
A**relational database** is a collection of data organized in tables. There are relations among the tables. The tables are formally described. They consist of rows and columns.关系型数据库是一种被组织在表里的数据的集合。
**SQL**(Structed Query Language) is a database computer language designed for managing data in relational database management systems.结构化查询语言是用来管理关系型数据库中数据的数据库语言。
A **schema** of a database system is its structure described in a formal language. It defines the tables. It defines the _tables_,the _fields_,_relationships_,_views_,_indexs_,_procedures_,_functions_,_queues_,_truggers_ and other elements.

##MySql Installation
On Ubuntu and other debian based distributions, we can install MySQL from packages by using the apt-get tools.
```
$sudo apt-get install mysql-server
```
##First steps in MySQL
###Starting/stopping the MySQL server
我的debian系统是这样，但教程是`mysqld`命令找不到。
```
$sudo /etc/init.d/mysql start
$sudo /etc/init.d/mysql stop
//下面也可以
$sudo service mysqlstart
$sudo service mysql stop
```
启动服务后，我们可以查看它是否在运行。
```
$mysqladmin -uroot -p***** ping
mysql is alive
```
也可以不写密码，防止泄密，手动输入。
我们用`mysqladmin`工具关闭MySQL服务。
```
$mysqladmin -uroot -p shutdown
```
安装完后启动服务提示
###Connecting to the MySQL server
```
$mysql -uroot -p
```
### Creating a database
显示系统中可用的数据库:
```
mysql> show database;
```
创建一个新的数据库,我们需要有适当的权限。
```
mysql>create database mydb;
```
选中新创建的数据库:
```
mysql>use mydb;
```
显示可以用的表:
```
mysql>show tables;
```
执行sql脚本：
```
mysql>source cars.sql;
```
###Creating a new user(创建新用户)
创建一个用户名为user12,密码为‘34kld’的用户。
```
mysql>create user user12@localhost identified by ‘24klq’;
```
将`mydb`的所有权限赋给用户`user12`：
```
mysql>grant all on mydb.* to user12@localhost;
```
## MySQL quick tutorial
用describe语句查看`City`表的结构。
```
mysql>describe City;
```
查看创建表`City`的`SQL`语句：
```
mysql>show create table City;
```
用`mysqldump`工具备份`City`表：
```
mysqldump -uroot -p world City > city.sql
```
删除表：
```
mysql>drop table City;
```
### Queries(查询语句)
限制前10行数据输出(Limiting data output)
```
mysql>select Id, Name, Population from City limit 10;
```
限制15到20行输出：
```
mysql>select Id, Name, Population from City limit 15, 5;
```
我们可以用`pager`命令显示数据：
```
mysql>pager less
PAGER set to ‘less’
mysql>select * from City;
```
我们可以`mysql`非交互式命令的方法，重定向到一个文件中去展示数据。
```
mysql -u user12 -p world -e “select * from City” > city
```
将world数据库中的city表中的数据重定向到city文件中。
## MySQL storage engines
MySQL5.5之前的版本默认的的存储引擎是`MyISAM`，之后的数据库版本默认存储引擎是`InnoDB`。
MySQL支持的存储引擎：
* [MyISAM](http://en.wikipedia.org/wiki/MyISAM)
* [InnoDB](http://en.wikipedia.org/wiki/InnoDB)
* [Memory](http://en.wikipedia.org/wiki/InnoDB)
* [CSV]()
* [Merage]()
* [Archive]()
* [Federated]()
* [Blackhole]()
* [Example]()
大家还是wiki吧，内容太多了。
### 指定和改变存储引擎
`engine`关键字将指定存储引擎用于特定的表中：
```
mysql>create table Cars(id integer primary key, name varchar(50), cost integer) engine=’MyISAM’;
```
默认存储引擎存储在`storage_engine`变量里
```
mysql>show variables like ‘storage_engine’;
+---------------+-----------+
|Variable_name  |  Value    |
+---------------+-----------+
|storage_engine |  InnoDB   |
+---------------+-----------+
```
查看特定表所用的存储引擎
```
mysql>select engine from information_schema.tables
    ->where table_schema=’mydb’
    ->and table_name=’Cars’;
```
改变表的存储引擎
```
mysql>alter table Cars engine=’InnoDB’;
```
## MySQL数据类型
###Numbers(数值类型)
####整型
| Data type | Bytes | Minimum value        | Maximum value       |
| --------- | ----- | -------------        | -------------       |
| tinyint   | 1     | -128                 | 127                 |
| smallint  | 2     | -32768               | 32767               |
| mediumint | 3     | -8388608             | 8388607             |
| integer   | 4     | -2147483648          | 214783647           |
| bigint    | 8     | -9223372036854775808 | 9223372036854775807 |
####浮点数据类型
| Data type  | Bytes            |
| :-------:  | :-----:          |
| float      | 4                |
| double     | 8                |
| deciml     | (M位,小数点后D位)|
必须谨慎使用`float`和`double`类型，虽然他们处理比较快，但是最后几位数据不够精确。在处理金融数据和科学计算常用`decimal`数据类型。
```
mysql>create table numbers (id tinyint, floats float, decimals decimal(3,2));
insert into Numbers values (1, 1.1, 1.1),(2, 1.1, 1.1), (3, 1.1, 1.1);
```
改变数据类型
```
mysql>alter table Ages modigy age tinyint unsigned;
```
####日期和时间类型
用`timediff()`函数求时间差：
```
mysql>select timediff(‘23:34:32’, ‘22:00:00’);
+----------------------------------+
|TIMEDIFF(‘23:34:32’, ‘22:00:00’)  |
+----------------------------------+
|11:27:42                          |
+----------------------------------+
```
我们可以用两个不同的类型
```
mysql>select timediff(‘211344’, 201123);
+-------------------------------------+
|TIMEDIFF(‘211344’, 201123)           |
+-------------------------------------+
|01:02:21                             |
+-------------------------------------+
```
用`time()`函数提取日期时间的时间部分。
```
mysql>select time(‘2011-01-29 11:27:42’);
```
MySQL可以用任何符号字符做分隔符，例如用@字符
```
mysql>select dayname(‘2011@01@29 11@50@13’);
+--------------------------------------------+
|DAYNAME(‘2011@01@29 11@50@13’)              |
+--------------------------------------------+
|Saturday                                    |
+--------------------------------------------+
```
时间戳**timestamp**

| Data type     | Format              |
|:-----------:  | :-----------------: |
| timestamp(14) | yyyymmddhhmmss      |
| timestamp(12) | yymmddhhmmss        |
| timestamp(10) | yymmddhhmm          |
| timestamp(8)  | yyyymmdd            |
| timestamp(6)  | yymmdd              |
| timestamp(4)  | yymm                |
| timestamp(2)  | yy                  |
创建含有时间戳的表
```
mysql>create table Prices(id tinyint, price decimal(8, 2), stamp timestamp);
```
当插入或是更新记录时时间戳默认取系统当前时间。
也可以关闭时间戳的更新，用`tamp timestamp default 0 on update current_timestamp`
#### strings类型
`vachar`常用于存储email,names,merchandise, address等
`binary`和`varbinary`没有字符集合，存储和比较基于(based on the numeric values of the bytes in the values)

| Data type     | Format        | 备注               |
| :-----------: | :-----------: | :---------------:  |
| CHAR          | 0-255         | 定长字符串存储     |
| VARCHAR       | 0-65535       | 变长字符串数据存储 |
| BINARY        | 0-255         | 定长字节存储       |
| VARBINARY     | 0-65535       | 变长字节存储       |

`blog`是一种大对象数据类型，它用于存储二进制数据类型，像图片文档等。

| Data type     | Format        | 备注              |
| :-----------: | :-----------: | :---------------: |
| TINTYBLOG     | 0-255         |                   |
| BLOG          | 0-65535       |                   |
| MEDIUMBLOG    | 0-16777215    |                   |
| LONGBLOG      | 0-4294967295  |                   |
`text`用于存储大文本数据，例如文章，博客，页面，评论等。

| Data type     | Format        | 备注              |
| :-----------: | :-----------: | :---------------: |
| TINYTEXT      | 0-255         |                   |
| TEXT          | 0-65535       |                   |
| MEDIUMTEXT    | 0-16777215    |                   |
| LONGTEXT      | 0-4294967295  |                   |
创建只能插入特定值的表
```
mysql>create table SizeTable(size enum(‘S’,‘M’,’L’,’XL’,’XXL’));
```
`set`和`enum`相似，但它可以包含0和多个列表中的值
### 创建改变删除表
创建一个表
```
mysql>create table Testing(id integer);
```
用`show`命令显示建表语句
```
mysql>show create table Testing;
```
创建的非临时表可以被`show`展示。
```
mysql>show tables like ‘T%’;
```
删除表
```
mysql>drop table Testing;
```
改变表名
```
mysql>alter table Testing rename to TestTable;
```
增加新属性：
```
mysql>alter table TestTable add iValues int;
```
显示每个属性：
```
mysql>show columns from TestTable;
```
增加约束条件：
```
mysql>alter table TestTable add primary key (id);
```
改变某属性的名称：
```
mysql>alter TestTable change column iValues iValues1 int;
```
改变某个属性的类型：
```
mysql>alter table TestTable modify column iValues1 mediumint;
```
删除某个属性：
```
mysql>alter table TestTable drop column iValues1;
```
## MySQL表达式
文件的值可能包含`strings`,`numbers`,`hexadecimal`,`boolean`和`NULL`。
```
mysql> select 3, ‘Wolf’, 34.5, 0x34, 0+b’10111’;
+----+------+----+-----+-----------------------+
| 3  | Wolf |34.5|0x34 |0+b’10111’             |
+----+------+----+-----+-----------------------+
| 3  | Wolf |34.5|4    |                     23|
+----+------+----+-----+-----------------------+
```
MySQL的变量前面加上`@`符号。
```
mysql> set @name = ‘Jane’;
mysql> select @name;
+--------+
|  @name |
+--------+
| Jane   |
+--------+
```
**运算符**
```
mysql> select not(3>9);     #一元运算
mysql> select 9/2, 9 DIV 2;    #算数运算
mysql> select false xor false, false xor true, true xor false, true xor true;    #逻辑运算
mysql> select 3*3 = 9, 9 = 9, 3 < 4, 3 <> 5, 4 <= 4, 5 != 5;    #关系运算
mysql> select 6 | 3, 6 & 3, 6 << 1;    #位运算
#其他
mysql> set @running = False;
mysql> select @running is false;
mysql> select ‘Tom’ in (‘Tom’, ‘Frank’, ‘Jane’);
mysql> select * from Cars where Name in (‘Audi’, ‘Hummer’);
mysql> select * from Cars where Name like ‘Vo1%’;
mysql> select * from Cars where Name like ‘____’;
mysql> select * from Cars where Name regexp ‘e.$’;
mysql> select * from Cars where Name regexp ‘^.e.*e.$’;
mysql> select * from Cars where Cost between 20000 and 55000;
```
## 插入、更新和删除数据
创建表
```
mysql> create table Books(id integer primary key, title varchar(100), author varchar(60));
```
插入表
```
mysql> insert into Books(id, title, author) values(1, ‘War and Peace’, ‘Leo Tolstoy’);
```
`replace`：比较数据的主键，如果没有则插入，如果有则更新
```
mysql> replace into Books values(3, ‘Paradise Lost’, ‘John Milton’);
```
创建Books2,将Books数据导入进去。
```
mysql> create table Book2(id integer primary key auto_increment, title varchar(100), author varchar(60)); 
mysql> insert into Book2 select * from Books;
```
插入多个值
```
mysql> insert into Books2(title, author) values(‘The Insulted and Humiliated’, ‘Fyodor Dostoyevasky’), (‘Cousin Bette’, ‘Honore de Balzac’);
```
