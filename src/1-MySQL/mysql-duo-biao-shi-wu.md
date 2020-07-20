# MySQL多表&事务



1. 多表查询
2. 事务
3. DCL

## 多表查询 

* 查询语法

  ```sql
  select
      列名列表
  from
      表名列表
  where ...
  ```

  操作数据

  \`\`\`sql -- 部门表 CREATE TABLE dept \( id INT PRIMARY KEY PRIMARY KEY, -- 部门id dname VARCHAR\(50\), -- 部门名称 loc VARCHAR\(50\) -- 部门所在地 \);

  -- 添加4个部门 INSERT INTO dept\(id,dname,loc\) VALUES \(10,'教研部','北京'\), \(20,'学工部','上海'\), \(30,'销售部','广州'\), \(40,'财务部','深圳'\);

-- 职务表，职务名称，职务描述 CREATE TABLE job \( id INT PRIMARY KEY, jname VARCHAR\(20\), description VARCHAR\(50\) \);

-- 添加4个职务 INSERT INTO job \(id, jname, description\) VALUES \(1, '董事长', '管理整个公司，接单'\), \(2, '经理', '管理部门员工'\), \(3, '销售员', '向客人推销产品'\), \(4, '文员', '使用办公软件'\);

-- 员工表 CREATE TABLE emp \( id INT PRIMARY KEY, -- 员工id ename VARCHAR\(50\), -- 员工姓名 job\_id INT, -- 职务id mgr INT , -- 上级领导 joindate DATE, -- 入职日期 salary DECIMAL\(7,2\), -- 工资 bonus DECIMAL\(7,2\), -- 奖金 dept\_id INT, -- 所在部门编号 CONSTRAINT emp\_jobid\_ref\_job\_id\_fk FOREIGN KEY \(job\_id\) REFERENCES job \(id\), CONSTRAINT emp\_deptid\_ref\_dept\_id\_fk FOREIGN KEY \(dept\_id\) REFERENCES dept \(id\) \);

-- 添加员工 INSERT INTO emp\(id,ename,job\_id,mgr,joindate,salary,bonus,dept\_id\) VALUES \(1001,'孙悟空',4,1004,'2000-12-17','8000.00',NULL,20\), \(1002,'卢俊义',3,1006,'2001-02-20','16000.00','3000.00',30\), \(1003,'林冲',3,1006,'2001-02-22','12500.00','5000.00',30\), \(1004,'唐僧',2,1009,'2001-04-02','29750.00',NULL,20\), \(1005,'李逵',4,1006,'2001-09-28','12500.00','14000.00',30\), \(1006,'宋江',2,1009,'2001-05-01','28500.00',NULL,30\), \(1007,'刘备',2,1009,'2001-09-01','24500.00',NULL,10\), \(1008,'猪八戒',4,1004,'2007-04-19','30000.00',NULL,20\), \(1009,'罗贯中',1,NULL,'2001-11-17','50000.00',NULL,10\), \(1010,'吴用',3,1006,'2001-09-08','15000.00','0.00',30\), \(1011,'沙僧',4,1004,'2007-05-23','11000.00',NULL,20\), \(1012,'李逵',4,1006,'2001-12-03','9500.00',NULL,30\), \(1013,'小白龙',4,1004,'2001-12-03','30000.00',NULL,20\), \(1014,'关羽',4,1007,'2002-01-23','13000.00',NULL,10\);

-- 工资等级表 CREATE TABLE salarygrade \( grade INT PRIMARY KEY, -- 级别 losalary INT, -- 最低工资 hisalary INT -- 最高工资 \);

-- 添加5个工资等级 INSERT INTO salarygrade\(grade,losalary,hisalary\) VALUES \(1,7000,12000\), \(2,12010,14000\), \(3,14010,20000\), \(4,20010,30000\), \(5,30010,99990\);

-- 需求：

-- 1.查询所有员工信息。查询员工编号，员工姓名，工资，职务名称，职务描述

-- 2.查询员工编号，员工姓名，工资，职务名称，职务描述，部门名称，部门位置

-- 3.查询员工姓名，工资，工资等级

-- 4.查询员工姓名，工资，职务名称，职务描述，部门名称，部门位置，工资等级

-- 5.查询出部门编号、部门名称、部门位置、部门人数

-- 6.查询所有员工的姓名及其直接上级的姓名,没有领导的员工也需要查询

```text
* 笛卡尔积
  * 有两个集合A,B, 取这来能个集合的所有组成情况
  * 要完成多表查询,需要消除无用的数据.

* 多表查询分类:

  1. 内连接查询

     1. 隐式内连接(用where清除无用数据)

        ```sql
        -- 查询所有员工信息和对应部门信息
        SELECT * FROM emp, dept WHERE emp.dept_id = dept.id;

        -- 查询员工表姓名, 工资 以及部门名称
        SELECT emp.ename, emp.salary, dept.dname FROM emp, dept WHERE emp.dept_id = dept.id;
        SELECT t1.ename, t1.salary, t2.dname FROM emp t1, dept t2 WHERE t1.dept_id = t2.id;
```

1. 显式内连接
   * 语法

     ```sql
     select 字段列表 from 表名1 inner join 表名2 on 条件
     ```

   * 例如:

     ```sql
     SELECT * FROM emp INNER JOIN dept on emp.dept_id = dept.id;
     SELECT * FROM emp JOIN dept on emp.dept_id = dept.id;
     ```
2. 内连接查询 1. 从哪些表中查询数据 2. 条件是什么 3. 查询哪些字段
   
   1. 外连接查询
3. 左外连接
   * 语法

     ```sql
     select 字段列表 from 表1 left outer join 表2 on 条件
     ```

   * 查询的是左边表所有记录, 以及其交集部分.
4. 右外连接

   * 语法

     ```sql
     select 字段列表 from 表1 right outer join 表2 on 条件
     ```

   * 查询的是右边表所有记录, 以及其交集部分.

   > 表名换个位置,左外就变成了右外.

   1. 子查询

5. 概念: 查询中嵌套查询, 称嵌套查询为子查询.

   ```sql
   -- 查询工资最高的员工信息
   -- 1. 查询最高的工资是多少
   SELECT MAX(salary) FROM emp;
   -- 2. 查询员工信息, 并且工资是9000
   SELECT * FROM emp WHERE emp.salary = 50000;
   -- 综合
   SELECT * FROM emp WHERE emp.salary = (SELECT MAX(salary) FROM emp);
   ```

6. 子查询的不同情况
   1. 子查询结果是单行单列
      * 子查询可以作为条件使用运算符判断. \(&gt; &lt; =等等\)

        ```sql
        -- 查询员工工资小于平均工资的人
        SELECT * from emp WHERE emp.salary < (SELECT AVG(salary) FROM emp);
        ```
   2. 子查询结果是多行单列
      * 子查询可以作为条件, 使用运算符IN

        ```sql
        -- 查询财务部和销售部所有员工信息
        SELECT id FROM dept WHERE dname = "财务部" OR dname = "市场部";
        SELECT * FROM emp WHERE dept_id = 40;

        SELECT * FROM emp WHERE dept_id IN (SELECT id FROM dept WHERE dname = "财务部" OR dname = "销售部");
        ```
   3. 子查询结果是多行多列, 子查询可以作为一个虚拟表
      * 子查询

        ```text
        -- 查询员工入职日期是2011-11-11之后的员工信息和部门信息
        SELECT * FROM emp WHERE joindate > "2001-05-01";
        SELECT * FROM dept t1, (SELECT * FROM emp WHERE joindate > "2001-05-01") t2 WHERE t1.id = t2.dept_id;
        ```

      * 普通查询

        ```sql
        -- 普通内连接
        SELECT * FROM emp t1, dept t2 WHERE t1.dept_id = t2.id AND t1.joindate > "2001-05-01";
        ```

* 多表查询练习

  ```sql
  -- 需求：
  -- 1.查询所有员工信息。查询员工编号，员工姓名，工资，职务名称，职务描述
  SELECT
      t1.id, t1.ename, t1.salary, t2.jname, t2.description
  FROM
      emp t1, job t2
  WHERE
      t1.job_id = t2.id;
  -- 2.查询员工编号，员工姓名，工资，职务名称，职务描述，部门名称，部门位置
  SELECT
      t1.id, t1.ename, t1.salary, t2.jname, t2.description, t3.dname, t3.loc
  FROM
      emp t1, job t2, dept t3
  WHERE
      t1.job_id = t2.id AND t1.dept_id = t3.id;
  -- 3.查询员工姓名，工资，工资等级
  SELECT 
      t1.ename,
      t1.salary, 
      t2.*
  FROM 
      emp t1, salarygrade t2
  WHERE
      t1.salary BETWEEN t2.losalary AND t2.hisalary;
  -- 4.查询员工姓名，工资，职务名称，职务描述，部门名称，部门位置，工资等级
  SELECT
      t1.id, t1.ename, t1.salary, t2.jname, t2.description, t3.dname, t3.loc, t4.*
  FROM
      emp t1, job t2, dept t3, salarygrade t4
  WHERE
      t1.job_id = t2.id AND t1.dept_id = t3.id AND (t1.salary BETWEEN t4.losalary AND t4.hisalary);
  -- 5.查询出部门编号、部门名称、部门位置、部门人数
  SELECT
      t1.id, t1.dname, t1.loc, t2.total
  FROM dept t1,
          (SELECT dept_id, COUNT(id) total
              FROM emp
              GROUP BY dept_id) t2
  WHERE t1.id = t2.dept_id;
  -- 6.查询所有员工的姓名及其直接上级的姓名,没有领导的员工也需要查询
   SELECT 
      t1.ename, t1.mgr, t2.id, t2.ename
   FROM 
      emp t1, emp t2
   WHERE 
      t1.mgr = t2.id; -- 自关联

  -- 没有领导的员工也需要查询
  SELECT 
      t1.ename, t1.mgr, t2.id, t2.ename
   FROM 
      emp t1
  LEFT JOIN
      emp t2
  ON 
      t1.mgr = t2.id; -- 自关联
  ```

## 事务

1. 事务的基本介绍 1. 概念:
   * 如果一个包含多个步骤的业务操作被事务管理, 要么这些操作同时成功, 或者同时失败.

     1. 操作
     2. 开启事务 `start transaction`
     3. 回滚: `rollback`
     4. 提交: `commit`
     5. 例子

     ```sql
     CREATE TABLE `account` (
     `id` int(11) NOT NULL AUTO_INCREMENT,
     `name` varchar(10) COLLATE utf8mb4_general_ci DEFAULT NULL,
     `balance` double DEFAULT NULL,
     PRIMARY KEY (`id`)
     ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;

     -- 开启事务
     START TRANSACTION;
     UPDATE account SET balance = balance - 500 WHERE name = "张三";
     UPDATE account SET balance = balance - 500 WHERE name = "李四";
     -- 如果出错
     ROLLBACK;
     -- 如果没错
     COMMIT;
     ```

     1. MySQL数据库中事务默认自动提交
     2. 事务提交的两种方式
     3. 自动提交
     4. MySQL就是自动提交的
     5. 一条DML\(增删改\)语句会自动提交一次事务
     6. 手动提交:
     7. Oracle数据库默认是手动提交事务
     8. 需要先开启事务再提交
     9. 修改事务的默认提交方式
     10. 查看事务的默认提交方式:  `SELECT @@autocommit;`
     11. 修改事务的默认提交方式: `set @@autocommit = 1; 1开启自动提交, 0关闭自动提交`

   1. 事务的四大特征
      1. 原子性: 是不可分割的最小单位, 要么同时成功, 要么同时失败.
      2. 持久性: 当事务提交或回滚后, 数据库会持久化的保存数据.
      3. 隔离性: 多个事务之间, 相互独立.
      4. 一致性: 开始和结束之间的状态不会被其他事务看到.
   2. 事务的隔离级别\(了解\)
2. 概念: 多个事务是隔离的, 相互独立的. 但是如果多个事务操作同一批数据, 则会引发一些问题,设置不同的隔离级别就可以解决这些问题.
3. 存在的问题: 1. 脏读: 一个事务读取到另一个事务没有提交的数据 2. 不可重复读\(虚读\): 在同一个事务中, 两次读取的数据不一样. 3. 幻读: 数据表中所有的记录, 另一个事物添加了一条数据, 则第一个事务查询不到自己的修改.
4. 隔离级别:

   1. read umcommited: 读未提交

      产生的问题: 脏读, 不可重复读, 幻读

   2. read commited: 读已提交

      产生的问题: 不可重复读, 幻读

   3. repeatable read: 可重复读

      产生的问题: 幻读

   4. serializable: 串行化

      可以解决所有问题

   > 注意: 隔离级别从小到大安全性越来越高, 但是性能越来越低
   >
   > 数据库查询隔离级别: `select @@transaction_isolation;`
   >
   > 设置数据库隔离级别: `SET transaction_isolation = value;`

```sql
                   set global transaction isolation level read uncommitted;
                   start transaction;
                   -- 转账操作
                   update account set balance = balance - 500 where id = 1;
                   update account set balance = balance + 500 where id = 2;
```

## DCL

* SQL分类
  1. DDL: 操作数据库和表
  2. DML: 增删改表中数据
  3. DQL: 查询表中的数据
  4. DCL: 管理用户, 授权
* DBA: 数据库管理员
* DCL: 管理用户, 授权
  1. 管理用户

     ```sql
     -- 增加用户
     CREATE USER 'tomcat'@'localhost' IDENTIFIED BY 'root';
     -- 修改密码
     UPDATE USER SET PASSWORD = PASSWORD('新密码') WHERE USER = '用户名';
     UPDATE USER SET PASSWORD = PASSWORD('abc') WHERE USER = 'lisi';
     SET PASSWORD FOR '用户名'@'主机名' = PASSWORD('新密码');
     SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123');
     -- 删除用户
     DROP USER '用户名'@'主机名';
     -- 查询用户
     -- 1. 切换到mysql数据库
     USE myql;
     -- 2. 查询user表
     SELECT * FROM USER;
     * 通配符： % 表示可以在任意主机使用用户登录数据库
                 权限管理
                 1. 查询权限：
                     -- 查询权限
                     SHOW GRANTS FOR '用户名'@'主机名';
                     SHOW GRANTS FOR 'lisi'@'%';

                 2. 授予权限：
                     -- 授予权限
                     grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
                     -- 给张三用户授予所有权限，在任意数据库任意表上

                     GRANT ALL ON *.* TO 'zhangsan'@'localhost';
                 3. 撤销权限：
                     -- 撤销权限：
                     revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';
                     REVOKE UPDATE ON db3.`account` FROM 'lisi'@'%';
     ```

     * MySQL中忘记密码:

       ```sql
       1. cmd -- > net stop mysql 停止mysql服务
       * 需要管理员运行该cmd
       2. 使用无验证方式启动mysql服务： mysqld --skip-grant-tables
       3. 打开新的cmd窗口,直接输入mysql命令，敲回车。就可以登录成功
       4. use mysql;
       5. update user set password = password('你的新密码') where user = 'root';
       6. 关闭两个窗口
       7. 打开任务管理器，手动结束mysqld.exe 的进程
       8. 启动mysql服务
       9. 使用新密码登录。
       ```

