```json
{
  "date": "2022.12.9 16:30",
  "title": "Mysql",
  "tag":"数据库"
}
```

## 常用命令

创建数据库 ：`create database 库名；` 使用数据库：`use 库名；`

查看所有数据库：`show databases；`

查看某个数据库下的表：`show tables；`

查看表结构：`desc 表名；`  查看版本号 `select version()；`

查看当前使用的数据库：`select database；`

数据导出：在windows的dos命令窗口中：

`mysqldump 数据库名 （可再加表名）>D:\数据库名.sql -uroot -p123456`

数据导入：

先创建数据库，再使用数据库，然后初始化

`source D:\数据库名.sql`

## 表

创建表：`create table 表名（字段名1 数据类型，字段名2 数据类型，字段名3 数据类型）；`

`create table 表名 as select * from 表名； 复制表`

数据类型：`varchar 可变长度字符串（根据数据长度动态分配空间） char 定长字符串（固定分配空间） int（整数）bigint（长整数）float（单精度浮点数）double（双精度浮点数）date（短日期类型）datetime（长日期类型）clob（字符大对象，最多存储4G的字符串）blob（二进制大对象，专门存储图片、声音、视频等流媒体数据，使用IO流插入）`

短日期默认格式：`%Y-%m-%d`，长日期默认格式：`%Y-%m-%d %h:%i:%s`

删除表： `drop table 表名；表不存在则报错`

`drop table if exists 表名； 若存在则删除，不存在不报错`

## `SQL`语句

`SQL`语句分类：

​	`DQL`：数据查询语言（凡是带有`select`关键字的都是查询语句）

​	`DML`：数据操作语言（凡是对表中的数据进行增删改的都是`DML`）

​	`insert delete update 主要操作表中的数据data`

​	`DDL`：数据定义语言（凡是带有 `create、drop、alter的都是DDL`）

​	主要操作的是表的结构，不是表中的数据

​	`TCL`：事务控制语言（包括：事务提交：`commit；` 事务回滚：`rollback；`）

​	`DCL`：数据控制语言（授权 `grant`、撤销权限 `revoke`。。。）

### 查询语句

`select 字段名1，字段名2 from 表名；`

使用 **as** 或 **空格** 起别名，`select dname as deptname from dept`

注意，只是将显示的查询结果列名显示为`deptname`，不修改

#### 条件查询

`select 字段名1，字段名2 from 表名 where 条件；`

**条件**：`> < >= <= != = ` `between ... and ... 两个值之间`

`is null 为空（is not null 不为空）and 并且 or 或者 in 包含`

`like 模糊查询，支持%或下划线匹配，%匹配任意个字符，下划线匹配一个字符`

#### 排序

`select 字段名 from 表明 order by 字段；`  升序排序

`select 字段名 from 表明 order by 字段 desc；`  降序排序

`select 字段名 from 表明 order by 字段1，字段2 asc；` 指定升序排序，若字段1相等，再用字段2排序

#### 连接查询

##### 内连接

将a、b两张表中匹配上的数据查出

`select 字段名 from 表1 (inner) join 表2 on 连接条件`

##### 外连接

将join右边的表视作主表，将主表数据全部查出，并关联左边的表，反之为左连接

`select 字段名 from 表1 right (outer) join 表2 on 连接条件`

`select 字段名 from 表1 left (outer) join 表2 on 连接条件`

##### 多表连接

`select 字段名 from 表a join 表b on a和b的连接条件 join 表c on a和c的连接条件`

#### 子查询

查询语句中嵌套查询语句

##### 嵌套在where

`select 字段名 from 表名 where （select。。。）`

##### 嵌套在from

注意：from 后面的子查询，可以将子查询的查询结果当作一张表

`select t.*,s.grade from （select...）表t join 表s on t和s的连接条件`

#### union

`select ... union select ... union 将两个查询结果拼接，相对于表连接来说可以减少匹配的次数，效率更高`

注意：合并结果集时，要求两个结果集的列数相同，数据类型最好一致

#### limit

将查询结果的一部分取出，通常使用在分页查询中

`select 字段名 from 表名 order by 字段名 desc limit 起始下标（默认从0开始），长度（取出多少条记录）`

### 插入语句

`insert into 表名（字段名1，字段名2...）values（值1，值2...）；`

`str_to_date('字符串日期','日期格式'(%Y-%m-%d-%h-%i-%s)) 字符串转换为日期date类型`

### 修改语句

`update 表名 set 字段名1=值1，字段名2=值2...where 条件；`

没有条件限制会导致所有数据更新

### 删除语句

`delete from 表名 where 条件；`

没有条件限制会删除表中全部数据

### 数据处理函数 / 单行处理函数

单行处理函数特点：一个输入对应一个输出

lower /upper 转换小写/大写：`select lower/upper（字段名）from 表名`

length取长度： `select length（字段名）from 表名`

`substr` 取子串：`select substr（被截取的字符串，起始下标（从1开始），截取的长度）from 表名`

trim 去空格：`select trim（字段名）from 表名`

`str_to_data 将字符串转换成日期`

`date format 格式化日期`

`round（数字，保留小数位） 四舍五入 rand() 生成随机数`

`ifnull（数据，被当做哪个值） 可以将null转换一个具体值 `

`case...when...then...else...end`

`例子：当员工的工作岗位是MANAGER的时候，工资上调%10，当工资岗位是SALEMEN的时候，工资上调%50，其他正常`

`select ename，job，sal as oldsal （case job when ‘MANAGER’ then sal*1.1 when ‘SALEMEN’ then sal*1.5 else sal end）as newsal from emp`

### 分组函数 / 多行处理函数

注意：

1. 分组函数在使用的时候必须先分组，否则，整张表默认一组

2. 分组函数自动忽略NULL，不需要对NULL进行处理
3. 分组函数不能直接使用在where子句中

多行处理函数特点：多个输入对应一个输出

`count() 计数 max()最大值 min()最小值 sum()求和 avg()求平均值`

分组查询，先分组，然后对每一组的数据进行操作：

`selcet ... from ... group by...`

`having（配合group by一起用）对分完组后的数据再进行过滤`

`distinct 去重，只能出现在所有字段的最前方`

## 约束

### 非空约束：not null

约束的字段不能为空

### 唯一性约束：unique

约束的字段不能重复，可以为NULL

### 主键约束：primary key

`create table 表名（id int primary key auto_increment）; //auto_increment表示自增，从1开始 `

约束的字段不能为空且不能重复

自然主键：主键值是一个自然数，和业务无关

业务主键：主键值和业务紧密关联，例如银行卡账号做主键值

### 外键约束：foreign key

`主表： create table t_class(classno int primary key,classname varchar(255));`

`子表：create table t_student(no int primary key,name varchar(255),cno int,foreign key(cno) references t_class(classno))`

## 存储引擎

存储引擎是一个表存储/组织数据的方式，不同的存储引擎，表存储数据的方式不同

默认存储引擎是：`InnoDB`

### 常用的存储引擎

#### `MyISAM`存储引擎

管理的表具有以下特征：

使用三个文件表示每个表：
格式文件 -  存储表结构的定义（`mytable.frm`）

数据文件 - 存储表行的内容（`mytable.MYD`）

索引文件 - 存储表上索引(`mytable.MYI`)

特点：可被转换为压缩、只读表来节省空间

#### `InnoDB 引擎`

`mysql默认的存储引擎，也是一个重量级的存储引擎。`

## 事务 transaction

一个事务就是一个完整的业务逻辑，是一个最小的工作单元，不可再分

例：假设转账，从A账户向B账户中转账10000.

将A账户的钱先减去10000，再将B账户的钱加上10000，要么同时成功要么同时失败

只有 `DML` 语句才跟事务有关,设计数据的增删改

`开启事务：start transaction`

在事务的执行过程中，每一条`DML`的操作都会记录到事务性活动的日志文件中。在执行过程中，可以提交事务，也可以回滚事务

### 提交事务  commit

清空事务性活动的日志文件，将数据全部彻底持久化到数据库表中，提交事务标志着事务的结束，并且是一种全部成功的结束

### 回滚事务 rollback

将之前所有的`DML`操作全部撤销，并且清空事务性活动的日志文件。回滚事务标志着，事务的结束，并且是一种全部失败的结束

### 事务的特性

#### A: 原子性

说明事务是最小的工作单元，不可再分

#### C: 一致性

所有事务要求，在同一个事务当中，所有操作必须同时成功，或者同时失败，以保证数据的一致性

#### I: 隔离性

A事务和B事务之间具有一定的隔离，教室A和教室B之间有一道墙，这道墙就是隔离性。

查看隔离的级别：`select @@tx_isolation`

事务与事务之间的隔离级别：
**读未提交： `read uncommitted`（最低隔离级别）**

事务A可以读取到事务B未提交的数据。存在问题：读到脏数据，理论上的隔离级别

**读已提交：`read committed`**

事务A只能读取到事务B提交之后的数据，解决了读到脏数据的问题

存在问题：不可重复读取数据（在事务开启之后，第一次读到的数据是3条，当前事务未结束，可能第二次再次读取是4条，3不等于4，称为不可重复读取）

**可重复读： `repeatable read`**

提交之后也读不到，永远读取的是刚开启事务的数据

事务A开启之后，不管是多久，每一次在事务A读取到的数据都是一致的，即使事务B将数据已经修改，并且提交，事务A读取到的数据还是没有发生改变

解决了不可重复读取得问题，存在的问题：会出现幻影读，每一次读取到的数据都是幻象，不真实

**序列化/串行化：`serializable`（最高级别）**

效率最低，解决所有问题，表示事务排队，不能并发

#### D: 持久性

事务最终结束的一个保障。事务提交，就相当于将没有保存到硬盘的数据保存到硬盘上

## 索引

### 什么是索引

在数据库表中的字段上添加，为了提高查询效率存在的一种机制。索引相当于一本书的目录，是为了缩小扫描范围而存在的一种机制。

`MySQL`在查询方面主要是两种方式：

第一种是全表扫描  第二种是根据索引检索

### 索引的实现原理

1. 在任何数据库当中主键上都会自动添加索引对象，如果有unique约束的话，也会自动创建索引对象
2. 在任何数据库当中，任何一张表的任何一条记录在硬盘存储上都有一个硬盘的物理存储编号
3. 在`mysql`当中，索引是一个单独的对象，不同的存储引擎以不同的形式存在，在 `MyISAM`存储引擎中，索引存储在一个 `.MYI`文件中。在 `InnoDB`存储引擎中索引存储在一个逻辑名称叫做 ` tablespace`当中，在 `MEMORY`存储引擎中索引被存储在内存当中，不管索引存储在哪里，索引在`MySQL`当中都是一个树的形式存在（B-Tree）

### 创建

`create index 表名_字段名_index on 表名（字段名） `

给表的字段添加索引，起名：`表名_字段名_index`

### 删除

`drop index 表名_字段名_index on 表名 `

将表上的`表名_字段名_index`索引对象删除

### 查看

`explain select * from 表名 where 字段名=‘’；`

### 索引失效的情况

1. `select * from 表名 where 字段 like ‘%T’；` 模糊匹配当中以%开头

2. 使用or，如果其中一边有字段没有索引，则失效

3. 使用复合索引的时候，没有使用左侧的列查找，则失败

4. 在where当中索引列参加了运算

   ``select * from 表名 where 字段+1 = 800`

5. 在where当中索引列使用了函数

## 视图

### 什么是视图

站在不同的角度看待同一份数据

特点：通过对视图的操作，会影响到原表数据

### 创建

`create view 视图名 as select * from 表名；`

### 删除

`drop view 视图名；`

## 设计范式

### 第一范式

要求任何一张表必须有主键，每一个字段原子性不可再分

### 第二范式

建立在第一范式的基础上，要求所有非主键字段完全依赖主键，不要产生传递依赖

### 第三范式

建立在第二范式的基础上，要求所有非主键字段直接依赖主键，不要产生传递依赖