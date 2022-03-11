# 库操作

```mysql
mysql> show databases;			/* 显示所有库 */
mysql> create database test;	/* 创建test库 */
mysql> use test;				/* 操作test库 */
mysql> drop database test;		/* 删除test库 */
```

# 表操作

### create table 创建表

```mysql
mysql> show tables;			/* 显示当前库里的所有表 */
create table person		/* 创建person表 */
(
	id int(8),
    name char(20),
    address char(20)	/* 最后一个字段不跟逗号 */
);

mysql> desc person;							/* describe描述 */
+---------+----------+------+-----+---------+-------+
| Field   | Type     | Null | Key | Default | Extra |
+---------+----------+------+-----+---------+-------+
| id      | int(6)   | YES  |     | NULL    |       |
| name    | char(20) | YES  |     | NULL    |       |
| address | char(20) | YES  |     | NULL    |       |
+---------+----------+------+-----+---------+-------+
3 rows in set (0.00 sec)

mysql> show create table person;			/* 查看创建person表的语句 */
+--------+-------------+
| Table  | Create Table|
+--------+-------------+
| person | CREATE TABLE `person` (
  `id` int(6) DEFAULT NULL,
  `name` char(20) DEFAULT NULL,
  `address` char(20) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1 |

```

### insert into 插入

```mysql
/* 向person表插入一条数据, (按默认位置,字符串要加引号) */
mysql> insert into person values (1,'zhangsan','beijing');
/* 向person表插入一条数据, (按指定位置,字符串要加引号) */
mysql> insert into person (name,id) values ('lisi',2);

mysql> select address,name from person;		/* 按指定顺序查询指定字段 */
+---------+----------+
| address | name     |
+---------+----------+
| beijing | zhangsan |
| NULL    | lisi     |
+---------+----------+
2 rows in set (0.00 sec)

/* 一次插入多条数据 */
mysql> insert into person values (3,'wangwu','shanghai'),(4,'zhaoliu','qingdao');

```

### select 查询

```mysql
/* 去重指定字段 */
mysql> select address from person;
+----------+
| address  |
+----------+
| beijing  |
| NULL     |
| shanghai |
| qingdao  |
| beijing  |
| qingdao  |
+----------+
6 rows in set (0.00 sec)

mysql> select distinct address from person;
+----------+
| address  |
+----------+
| beijing  |
| NULL     |
| shanghai |
| qingdao  |
+----------+
4 rows in set (0.00 sec)

```





### update	修改某一行

```mysql
update person set name='abc' where id=10;		/* 把id=10的这名字改成abc */
```



### alter		修改整张表

```mysql
mysql> alter table t2 character set utf8;		/* 把表的字符集改为utf8 */
```



### limit		指定行

**limit a,b		表示从第a行开始显示, 共显示b行	(行号下标从0开始)**

```mysql
SELECT DISTINCT score FROM `student` order by score desc LIMIT 0,2;
```





# SQL语言分类：

### 数据定义语言DDL

**Data Definition Languages**

创建数据库中的表、索引、视图、存储过程、触发器等，常用的语句关键字有：

CREATE, ALTER,DROP, TRUNCATE, COMMENT, RENAME

### 数据操纵语言DML

**Data Manipulation Language**

1) 查询：SELECT
2) 插入：INSERT
3) 更新：UPDATE
4) 删除：DELETE
5) MERGE, CALL, EXPLAIN PLAN, LOCK TABLE

### 数据控制语言DCL

**Data Control Language**

用于授权/撤销数据库及其字段的权限

1. 授权: GRANT
2. 撤权: REVOKE

### 事务控制语言TCL

**Transaction Control Language**

1. 提交: COMMIT
2. 回滚: ROLLBACK
3. 储存点: SAVEPOINT
4. SET TRANSACTION
