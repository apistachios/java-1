# Mysql

## SQL分类

- 数据定义语言：简称**DDL(Data Definition Language)**，用来定义数据库对象：数据库，表，列等。关键字：create，alter，drop等
- 数据操作语言：简称**DML(Data Manipulation Language)**，用来对数据库中表的记录进行更新。关键字：insert，delete，update等
- 数据控制语言：简称**DCL(Data Control Language)**，用来定义数据库的访问权限和安全级别，及创建用户。
- 数据查询语言：简称**DQL(Data Query Language)**，用来查询数据库中表的记录。关键字：select，from，where等

## 常用sql语句

### 数据库操作

- 创建数据库 `create database 库名` 或者 `create database 库名 character set 编码`
- 查看编码 `show create database 库名`
- 删除数据库 `drop database 库名`
- 使用数据库 `use 库名`
- 查看当前操作的库 `select database()`
- 查看当前所有的数据库 `show databases`

### 表中数据类型

#### 数据库中表中的数据类型

|分类 |类型名称 |说明
|:-:|:-:|:-:|
|整数类型 |tinyInt |很小的整数
||smallint |小的整数
||mediumint |中等大小的整数
||int(integer) |普通大小的整数
|小数类型 |float |单精度浮点数
||double |双精度浮点数
||decimal（m,d） |压缩严格的定点数,m表示总位数,d表示小数点几位
|日期类型 |year |YYYY 1901~2155
||time |HH:MM:SS ­838:59:59~838:59:59
||date |YYYY-­MM-­DD 1000­01­01~9999­12­3
||datetime |YYYY-­MM-­DD HH:MM:SS 1000­01­01 00:00:00~ 9999­-12-­31 23:59:59
||timestamp |YYYY­-MM­-DD HH:MM:SS 1970~01~01 00:00:01 UTC~2038­01­19 03:14:07UTC
|文本、二进制类型 |CHAR(M) |M为0~255之间的整数
||VARCHAR(M) |M为0~65535之间的整数
||TINYBLOB |允许长度0~255字节
||BLOB |允许长度0~65535字节
||MEDIUMBLOB |允许长度0~167772150字节
||LONGBLOB |允许长度0~4294967295字节
||TINYTEXT |允许长度0~255字节
||TEXT |允许长度0~65535字节
||MEDIUMTEXT |允许长度0~167772150字节
||LONGTEXT |允许长度0~4294967295字节
||VARBINARY(M) |允许长度0~M个字节的变长字节字符串
||BINARY(M) |允许长度0~M个字节的定长字节字符串

#### 常用类型

|类型| 描述
|:-:|:-:|
|int| 整型|
|double| 浮点型|
|varchar| 字符型|
|date| 日期型|

#### 常用约束

|约束名称 |sql表示 |说明
|:-:|:-:|:-:|
|主键约束| primary key| 唯一性，非空性|
|唯一约束| unique |唯一性，可以空，但只能有一个|
|默认约束| default| 该数据的默认值|
|自增长| auto_increment| 一般用于主键自动增长|
|非空约束| not null| 数据不能为空|

#### 创建表

```sql
create table 表名(
    字段名 类型(长度) [约束],
    字段名 类型(长度) [约束],
    字段名 类型(长度) [约束]
);
```
```sql
create table t_student(
    sid int primary key auto_increment,
    sname varchar(5) default '张三',
    sage int 
);
```

```sql
--查看数据库中的表
show tables;

--查看表结构
desc t_student;

--删除表
drop table t_student;

--添加字段
alter table t_student add addr varchar(30) ;

--修改字段
alter table t_student modify addr varchar(35);

-- 删除字段
alter table t_student drop addr;

--修改列名
alter table t_student change sname name varchar(10);

--修改表名
rename table t_student to student;

--查看表结构
desc student;

--查看当前表的创建结构
show create table student;
``` 

### 数据库表中记录的操作(增删改查)
```sql
--向表中插入数据
insert into student(sid,name,sage) values(1,'tom',13); 
--所有字段赋值的时候可以简写,但是赋值的顺序必须按照字段的顺序给
insert into student values(2,'张三',14); 
--不给主键赋值
insert into student(name,sage) values('李四',111); 
insert into student values(null,'王五',17);
--查询所有
select * from student;

--删除所有数据 , delete删除数据后,自动增长的序号会保存, truncate删除数据后,自动增长的序号会删除
--表中主键的记录还在增加
delete from student; 
--表中主键会清空
truncate table student;
--删除表中特定数据
delete from student where sid=3; 
--删除sid 为1,2,3,4
delete from student where sid in (1,2,3,4);

--修改表中特定记录
update student set sid=3, name='zhangsan', sage=12 where sid=3;
update student set sid=3, name='zhang', sage=12 where name='zhangsan';
--如果不加条件,就会将表中所有的数据进行修改
update student set name='zhang', sage=12 where name='王五';

--查询所有数据, *代表所有字段
select * from student;
--查询特定数据
select sid from student;
select name from student;
select name from student where sid=2;
select sid,name from student where sid=2;
--查询结果去重
select distinct(name) from student;
```

### 条件查询

|运算符分类 |运算符表示 |说明
|:-:|:-:|:-:|
|比较运算符| < <= >= = <> |大于、小于、大于(小于)等于、不等于|
||BETWEEN …AND…| 显示在某一区间的值(含头含尾)|
||IN(set) |显示在in列表中的值，例：in(100,200)|
||LIKE 通配符 |模糊查询，Like语句中有两个通配符: **%** 用来匹配多个字符；例`first_name like 'a%';` **_** 用来匹配一个字符。例`first_name like 'a_';`|
||IS NULL |判断是否为空`is null;` 判断为空 `is not null;` 判断不为空|
|逻辑运算符 |and |多个条件同时成立|
||or |多个条件任一成立|
||not |不成立，例：`where not(salary>100);`|

```sql
--数据类型参与运算
select sage+10 from student;

-不等于3
select * from student where sid <> 3;
--1到4,包含1和4
select * from student where sid between 1 and 4;
--查询sid为5和6
select * from student where sid in (5,6);

--查询name是否为null
select * from student where name is null;
select * from student where name is not null;

--第二个为h
select * from student where name like '_h*';

--and和
select * from student where sid > 2 and sage = 12;
--or或
select * from student where sid > 2 or sage = 11;
```

### 别名
```sql
--只用于获取结果的显示,不会去修改表中字段的名称
--关键字叫as as可以省略
select sid as 编号, name 姓名 from student as s;
select s.sid as 编号, s.name 姓名 from student as s;
```

### 模糊查询
```sql
--必须添加''
--包含z,前面可以为空
select * from student where name like '%z%';
--一般用下面这种格式
select * from student where name like '%' 'z' '%';
--第二个为h
select * from student where name like '_h*';
```

### 排序
```sql
--升序
select * from product order by price ;
select * from product order by price asc;
--降序
select * from product order by price desc;
--两个参数
select * from product order by price,pid desc;
select * from product order by price desc,pid desc;
```

### 聚合函数 
```sql
--求和, 最大值，最小值，平均数
select sum(price) '总价格' from product;
select max(price) '最大值' from product;
select min(price) '最小值' from product;
select avg(price) '平均数' from product;
--保留两位小数
select round(avg(price),2) '平均数' from product;
--查询总条数
select count(*) '总条数' from product;
select count(1) '总条数' from product;
```

### 分组操作

```sql
--pname为第一个数据,price为总和
select pname,sum(price) from product;
--pname一样的总和
select pname,sum(price) from product group by pname;
--总和大于5000的pname
select pname,sum(price) s from product group by pname having s >5000;
```

### 分页
```sql
--知道总条数和每页数计算多少页
--每页显示的内容如何使用sql查询

--获取前五条数据
select * from product limit 0,5;
--获取6到10
select * from product limit 5,5;
--获取a页数据,每页b条记录
select * from product limit (a-1)*b,b;
--每页有b条记录,那么一共有count(*)条记录,应该分(count(*)+b-1)/b页 
```

## 多表查询

### 交叉连接(基本不用)
使用的关键字 `CROSS JOIN` ,可以省略写成 `,`
```sql
-- 单独连接两张表,没有任何条件,相当于两张表做笛卡尔积(将a表和b表变成了一个a*b表)
select * from staff CROSS JOIN department;
select * from staff,department;
```

### 内连接
使用关键字`inner join`, `inner`可以省略
```sql
--隐式连接,交叉连接+多表之间的关联条件
select * from staff s,department d where did = d.id;
select d.name from staff s,department d where s.id = 6 and did = d.id ;
--显式连接,表与表之间通过关键字inner join进行连接,并且关联条件用on
select * from staff s inner join department d on did = d.id;
```

### 外连接
关键字是`outer join`, `outer`可以省略,  
```sql
--左外链接,会显示左表所有数据(包含没有关联的)
select * from staff s right outer join department d on did = d.id;
--右外链接,会显示右表所有数据(包含没有关联的)
select * from staff s left outer join department d on did = d.id;
```

### 子查询
一个查询的结果作为另一个查询的条件
```sql
select name from staff s left outer join department d on did = d.id;
select name from department where id = (select did from staff where id = 6);
```


