# SQL

1. 什么是SQL

> 结构化查询语言

2. 通用语法

> 1. 单行或多行, 以分号结尾.
>
> 2. 用空格和缩进增强可读性
>
> 3. 不区分大小写, 关键字建议用大写.
>
> 4. 三种注释
>
>    ```sql,ignore
>    -- 单行注释
>    # 单行注释
>    /* */多行注释
>    ```

3. SQL分类

> 1. DDL(Data definition Language) 数据定义语言, 定义数据库对象. 例如: 数据库, 表, 列等. 关键字: create,  drop, alter等.
>
> 2. DML(Data Manipulation Language) 桑菊操作语言 用来对数据库中的表的数据进行增删改, 关键字: insert, delete, update等.
>
> 3. DQL(Data Query Language)数据查询语言 用来查询数据库中表的记录. 关键字: select, where等.
>
> 4. DCL(Data Control Language)数据控制语言.用来定义数据库的访问级别以及创建用户. 关键字: grant, revoke等.

## DDL: 操作数据库, 表

1. 操作数据库: CRUD

> 1. C(Create): 创建
>
>    create database if not exists db character set gbk;
>
> 2. R(Retrieve): 查询
>
>    * 查询所有数据库名称: show databases;
>    * 查询创建数据库语句: show create database mysql;
>
> 3. U(Update): 修改
>
>    * 修改数据库字符集: alter database db_name character set utf8;
>
> 4. D(Delete): 删除
>
>    * 删除数据库: drop database if exists db_name;
>
> 5. 使用数据库
>
>    * 查询当前数据库: select database();
>    * 使用数据库: use db_name;

2. 操作表

> 1. C(Create): 创建
>
>    create table 表名(
>
>    ​	列名1 数据类型1,
>
>    ​	列名2 数据类型2
>
>    ​	...
>
>    ​	列名n 数据类型n
>
>    );
>
>    数据库类型:
>
>     1. int类型
>
>     2. double(5, 2) 小数类型, 共五位, 小数点后保留两位.
>
>     3. date 日期类型,只包含年月日 yyyy-MM-dd
>
>     4. datetime 年月日时分秒 yyyy-MM-dd HH:mm:ss
>
>     5. timestamp 时间戳,年月日时分秒 yyyy-MM-dd HH:mm:ss
>
>        如果将来不给这个字段赋值或者赋值为null, 则默认使用当前系统时间自动赋值.
>
>    	6. varchar(20) 字符串类型,最大20个字符
>
> ```sql,ignore
> //创建表
> create table student(
> 	id int,
> 	name varchar(32),
> 	age int,
> 	score double(4,1),
> 	birthday date,
> 	insert_time timestamp
> );
> //复制表
> create table 表名 like 被复制表名;
> ```
>
> 1. R(Retrieve): 查询
>
>    * 查询所有表名称: show tables;
>    * 查询表结构: desc table_name;
> 2. U(Update): 修改
>
>    * 修改表名:             alter table 表名 rename to 新表名;
>    * 修改表的字符集:  alter table 表名 character set utf8;
>    * 添加一列:             alter table 表名 add 列名 数据类型;
>    * 修改列名称,类型: alter table 表名 change 列名 新列名 新数据类型;
>    * 修改类型:             alter table 表名 modify 列名 新数据类型;
>    * 删除列:                 alter table 表名 drop 列名;
> 3. D(Delete): 删除
>
>    * drop table if exists 表名;

## DML: 增删改表中的数据

1. 添加数据

   ```sql,ignore
   insert into 表名(列名1, 列名2,...) values (值1, 值2, ...);
   ```

2. 删除数据

   ```sql,ignore
   delete from 表名 [where 条件];
   TRUNCATE TABLE 表名; //删除表, 并创建一个一模一样的表
   ```

3. 修改数据

   ```sql,ignore
   update 表名 set 列名1=值1, 列名2=值2,...[where 条件];
   ```

## DQL: 查询表中的记录

1. 语法

   ```sql,ignore
   select 
   	字段列表
   from
   	表名列表
   where
   	条件列表
   group by
   	分组字段
   having
   	分组之后的条件
   order by
   	排序字段
   limit
   	分页
   ```

   2. 基础查询

      * 多个字段的查询

        ```sql
        select 字段1, 字段2... from 表名;
        ```

      * 去除重复

        ```sql
        SELECT DISTINCT 字段名 FROM 表名;
        ```

      * 计算列

        ```sql
        -- 计算math 和English分数之和
        SELECT `name`, english, math+english FROM student;
        -- 如果有null参数的运算, 计算结果都为null,要用IFNULL设置默认值
        SELECT `name`, math, english, math+IFNULL(english,0) FROM student;
        ```

      * 起别名

        ```sql
        SELECT `name`, math, english, math+IFNULL(english,0) as 总分 FROM student;
        SELECT `name` 名字, math 数学, english 英语, math+IFNULL(english,0) 总分 FROM student;
        -- AS 可以省略
        ```

3. 条件查询

   1. where子句后跟条件

   2. 运算符

      ```sql,ignore
      > < <= >= = <>
      BETWEEN...AND
      IN(集合)
      LIKE _单个任意字符 %多个任意字符
      IS NULL
      AND 或 &&
      OR 或 ||
      NOT 或 !
      ```

      ```sql,ignore
      -- 查询年龄大于20岁
      SELECT * FROM student WHERE age > 20;
      SELECT * FROM student WHERE age >= 20;
      -- 查询年龄等于20岁
      SELECT * FROM student WHERE age = 20;
      -- 查询年龄不等于20岁
      SELECT * FROM student WHERE age != 20;
      SELECT * FROM student WHERE age <> 20;
      -- 查询大于等于20, 小于等于30
      SELECT * FROM student WHERE age >= 20 && age <= 30;
      SELECT * FROM student WHERE age >= 20 AND age <= 30;
      SELECT * FROM student WHERE age BETWEEN 20 AND 30;
      -- 查询年龄22岁, 18岁, 25岁的信息
      SELECT * FROM student WHERE age = 22 OR age = 18 OR age = 25;
      SELECT * FROM student WHERE age IN(22, 18, 25);
      -- 查询英语成绩是null的记录, null不能使用=或者!=判断
      SELECT * FROM student WHERE english = NULL; -- 错误
      SELECT * FROM student WHERE english IS NULL;
      SELECT * FROM student WHERE english IS NOT NULL;
      
      -- 查询姓马的人
      SELECT * FROM student WHERE `name` LIKE '马%';
      -- 第二个字是化的人
      SELECT * FROM student WHERE `name` LIKE "_化%";
      -- 姓名是三个字的人
      SELECT * FROM student WHERE `name` LIKE "___";
      -- 查询姓名中包含马的人
      SELECT * FROM student WHERE `name` LIKE "%马%";
      ```

      