# 库语言

## 建库

````sql
create database [if not exists] 数据库名 [character 1 set '字符集的名字'];
````

````sql
create database test character set 'utf8';
````

## 修改库

````sql
alter database 数据库的名称 character set '新的编码集';
````

````sql
alter database test character set 'utf8mb4';
````

## 删除库

````sql
drop database 数据库的名称；
````

````sql
drop database test;
````

# 表操作

## 建表

````sql
create table 表的表名(
列名 数据类型  [约束],
列名 数据类型  [约束],
列名 数据类型  [约束]
) [engine = 存储引擎的名称];
````

````sql
create table student_info(
    id int,
    stu_no varchar(20),
    stu_name varchar(30),
    gender varchar(10),
    age int,
    edu_level varchar(20),
    graduate_school varchar(120)
);
````

## 修改表名

````sql
alter table 原表名 rename to 新表名;
````

````sql
alter table student_info rename to stu_info;
````

## 添加字段

````sql
alter table 表的表名 add [column] 字段名 数据类型 [约束];
````

## 修改字段

````sql
alter table 表的表名 change [column] 旧字段 新字段 数据类型 [约束];
````

````sql
alter table stu_info change open_class_date start_date date;
````

## 删除字段

````sql
alter table 表的表名 drop [column] 字段名称;
````

````sql
alter table stu_info drop start_date;
````

## 删除表

````sql
drop table 表的表名;
````

````sql
drop table teacher_info;
````

## 主键约束

建表时指定

````sql
create table user_info(
 id bigint primary key auto_incredible,
 user_name varchar(20),
 age int,
 gender int
);
````

建表后添加

````sql
create table user_info(
    id bigint,
    user_name varchar(20),
    age int,
    gender int
);
//语法
alter table 表的表名 add constraint 主键的名称 primary key(某一列);
alter table user_info add constraint pk_id primary key(id);
````

## 外键约束

建表时指定

````sql
create table 表的表名(
 列 数据类型 [约束],
 列 数据类型 [约束],
 外键列 数据类型 [约束],
 foreign key(外键列) references 主表的表名(id) [on delete cascade on update cascade] 
);
````

````sql
create table teacher_info(
    id int primary key,
    teacher_name varchar(20),
    fk_dept_id int,
    foreign key(fk_dept_id) references dept_info(id) [on delete cascade on update cascade]
);
````

建表后添加

````sql
alter table 表的表名 add constraint  [外键约束的名称]  foreign key(外键列) references 主表的名称(id) 
[on delete cascade on update cascade];
````

````sql
create table teacher_info(
    id int primary key,
    teacher_name varchar(20),
    fk_dept_id int
);
alter table teacher_info add constraint fk_dept_id foreign key(fk_dept_id) references dept_info(id) 
on delete cascade on update cascade;
````

删除外键约束：

````sql
alter table 表的表名 drop foreign key 外键约束的名称;
````

````sql
alter table teacher_info drop foreign key fk_dept_id;
````

## 自增长约束

````sql
主键 数据类型 主键约束 auto_increment
````

````sql
create table test_info (
    id int primary key auto_increment,
    test_name varchar(20)
);
````

## 唯一约束

````sql
列名 数据类型 unique
````

````sql
create table user_info (
    id int primary key auto_increment,
    user_name varchar(20),
    age int,
    gender int,
    id_card varchar(18) unique,
    telphone varchar(11) unique
);
````

## 非空约束

````sql
字段 数据类型  not null
````

````sql
create table user_info (
    id int primary key auto_increment,
    user_name varchar(20) not null,
    age int,
    gender int,
    id_card varchar(18) unique,
    telphone varchar(11) unique
);
````

## 检查约束

````sql
字段 set(值1，值2，值3)
````

````sql
create table user_info (
    id int primary key auto_increment,
    user_name varchar(20) not null,
    age int,
    gender set('0','1','-1'),
    id_card varchar(18) unique,
    telphone varchar(11) unique
);
````

## 默认值约束

````sql
字段 数据类型 default '默认值'
````

````sql
create table student_info(
    id bigint primary key auto_increment,
    stu_name varchar(20) default '蜗牛学生',
    login_name varchar(20) not null unique,
    pwd varchar(36) default '123456',
    edu_level set('大专','本科','研究生'),
    grade_school varchar(120)
);
````

# 增加数据Create

直接添加

````sql
insert into 表的表名(字段列表) values (值列表);
//全字段添都添加，可省略字段列表
````

直接批量添加

````sql
insert into 表的表名(字段列表) values (值列表),(值列表),(值列表),(值列表)……;
````

复制批量添加

````sql
insert into 表的表名(字段列表) select 字段列表 from 源表名;
````

````sql
insert into tongxunlu(user_name,grade_school) select stu_name,grade_school from student_info;
````

# 修改数据Update

````sql
update 表的表名 set 字段名=值,字段名=值,字段名=值 ……  [where 修改条件]
````

````sql
update student_info set pwd = '654321' where stu_name = '蜗牛学院' and edu_level = '本科';
````

# 删除数据Delete

delete删除

````sql
delete from 表的表名 [where 删除条件];  
````

````sql
delete from dept_info;
````

Truncate删除（重置）

````sql
truncate table 表的表名;
````

````sql
truncate table student_info;
````

# 查询数据Retrieve

## 基础查询

````sql
select 列名列表 from 表的表名 [where 查询条件]
````

````sql
select id,books_name,books_no,books_type from books_info;
````

````sql
select * from books_info;
````

````sql
select * from books_info where id in(1,3,5,9,);
````

## 别名（表和字段）

````sql
select name [as] n, age [as] a from student [as] s where s.id=10;
````

## 模糊查询

````sql
select * from books_info where books_name like '毛泽东%';
````

````sql
select * from books_info where books_name like '%毛泽东先生';
````

````sql
select * from books_info where books_name like '%毛泽东%';
````

````sql
select * from books_info where books_name like '张_';
````

````sql
select * from books_info where books_name like '张__';
````

````sql
select * from books_info where books_name like '__三';
````

````sql
select * from books_info where books_name like '_三_';
````

## 去重查询

````sql
select distinct books_name,books_type from books_info where books_no like 'WNCD%';
````

## 排序查询

````sql
select distinct books_name,books_type,books_no from books_info where books_no like 'WNCD%' order by books_no asc;
````

````sql
select distinct books_name,books_type,books_no from books_info where books_no like 'WNCD%' order by books_no desc;
````

## 分页查询

````sql
select 字段列表 from 表的表名 limit  3;
````

````sql
select 字段列表 from 表的表名 limit  4,9;
````

## 多条件查询

````sql
select 字段列表 from 表的表名 where 条件1 and 条件2 and 条件3 ……
````

````sql
select 字段列表 from 表的表名 where 条件1 or 条件2 or 条件3 ……
````

````sql
select * from books_info where price between 100 and 500;
````

## null值查询

````sql
select * from books_info where price is null;
````

````sql
select * from books_info where price is not null;
````

## 聚合查询

count()

````sql
select count(*) from books_info;
````

````sql
select count(price) from books_info;
````

````sql
select count(1) from books_info;
````

max()

````sql
select max(price) from books_info;
````

min()

````sql
select min(price) from books_info;
````

sum()

````sql
select sum(price) from books_info;
````

avg()

````sql
select avg(price) from books_info;
````

ifnull()

````sql
select avg(ifnull(price,0)) from books_info;
````

## 分组查询

````sql
select company,group_concat(name) from books group by company;
//group_concat(字段)，查看具体的分组详细
````

````sql
select publish_company as 出版社,books_type as 书籍类型,group_concat(books_name)as 名字详细 from books_info group by publish_company,books_type;
//多字段分组
````

````sql
select publish_company as 分组,group_concat(books_name),count(books_name) from books_info group by publish_company;
````

## Having再过滤

````sql
SELECT    publish_company AS 分组， group_concat(books_name),    avg(ifnull(price, 0)) AS avgp,    count(*) AS 出版数量 FROM    books_infoGROUP BY    publish_company HAVING    avgp >= 200;
````

## 隐式内连接

````sql
select * from admin_info as a,dept_info as d where a.fk_dept_id = d.id;
````

## 内连接

````sql
select * from 左边的表 inner join 右边的表 on 左边的外键 = 右表的主键;
````

````sql
select * from admin_info as a,dept_info as d where a.fk_dept_id = d.id;
````

## 左外连接

````sql
SELECT
    a.id,
    a.admin_name,
    d.dept_name
FROM
    admin_info AS a
LEFT JOIN dept_info AS d ON a.fk_dept_id = d.id;
````

## 右外连接

````sql
SELECT
    a.id,
    a.admin_name,
    d.dept_name
FROM
    admin_info AS a
RIGHT JOIN dept_info AS d ON a.fk_dept_id = d.id;
````

## 自关联

````sql
SELECT
    a1.id,
    a1.area_name
FROM
    area_info AS a1
INNER JOIN area_info AS a2 ON a1.pid = a2.id
INNER JOIN area_info AS a3 ON a2.pid = a3.id
WHERE
    a3.area_name = '四川省';
````

## 子查询

````sql
select 字段列表 from 表的表名 [where 查询语句] [group by分组语句] [having 聚合条件] [order by 排序字段 asc/desc] [limit 分页条件];
````

select

````sql
SELECT
    id,
    admin_name,
    (
        SELECT
            dept_name
        FROM
            dept_info
        WHERE
            id = fk_dept_id
    ) as 部门名称,
    (
        SELECT
            role_name
        FROM
            role_info
        WHERE
            id = fk_role_id
    ) as 角色名称
FROM
    admin_info;
````

where

````sql
SELECT
    *
FROM
    admin_info
WHERE
    fk_dept_id = (
        SELECT
            id
        FROM
            dept_info
        WHERE
            dept_name = '教学部'
    );
````

from

````sql
SELECT
    p.*,b.card_no
FROM
    person_info AS p,
    (SELECT * FROM borrow_card) AS b
WHERE
    p.fk_borrow_card_id = b.id;
````

in

````sql
SELECT
    *
FROM
    admin_info
WHERE
    fk_dept_id IN (
        SELECT
            id
        FROM
            dept_info
        WHERE
            dept_name IN ('教学部', '后勤部')
    );
````

exists

````sql
SELECT
    *
FROM
    admin_info
WHERE
    EXISTS (
        SELECT
            id
        FROM
            dept_info
        WHERE
            id = fk_dept_id
        AND dept_name IN ('教学部', '后勤部')
    );
````