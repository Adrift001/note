# 目录

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

  ```sql
  -- 部门表
  CREATE TABLE dept (
    id INT PRIMARY KEY PRIMARY KEY, -- 部门id
    dname VARCHAR(50), -- 部门名称
    loc VARCHAR(50) -- 部门所在地
  );
  
  -- 添加4个部门
  INSERT INTO dept(id,dname,loc) VALUES 
  (10,'教研部','北京'),
  (20,'学工部','上海'),
  (30,'销售部','广州'),
  (40,'财务部','深圳');
  
  
  
  -- 职务表，职务名称，职务描述
  CREATE TABLE job (
    id INT PRIMARY KEY,
    jname VARCHAR(20),
    description VARCHAR(50)
  );
  
  -- 添加4个职务
  INSERT INTO job (id, jname, description) VALUES
  (1, '董事长', '管理整个公司，接单'),
  (2, '经理', '管理部门员工'),
  (3, '销售员', '向客人推销产品'),
  (4, '文员', '使用办公软件');
  
  
  
  -- 员工表
  CREATE TABLE emp (
    id INT PRIMARY KEY, -- 员工id
    ename VARCHAR(50), -- 员工姓名
    job_id INT, -- 职务id
    mgr INT , -- 上级领导
    joindate DATE, -- 入职日期
    salary DECIMAL(7,2), -- 工资
    bonus DECIMAL(7,2), -- 奖金
    dept_id INT, -- 所在部门编号
    CONSTRAINT emp_jobid_ref_job_id_fk FOREIGN KEY (job_id) REFERENCES job (id),
    CONSTRAINT emp_deptid_ref_dept_id_fk FOREIGN KEY (dept_id) REFERENCES dept (id)
  );
  
  -- 添加员工
  INSERT INTO emp(id,ename,job_id,mgr,joindate,salary,bonus,dept_id) VALUES 
  (1001,'孙悟空',4,1004,'2000-12-17','8000.00',NULL,20),
  (1002,'卢俊义',3,1006,'2001-02-20','16000.00','3000.00',30),
  (1003,'林冲',3,1006,'2001-02-22','12500.00','5000.00',30),
  (1004,'唐僧',2,1009,'2001-04-02','29750.00',NULL,20),
  (1005,'李逵',4,1006,'2001-09-28','12500.00','14000.00',30),
  (1006,'宋江',2,1009,'2001-05-01','28500.00',NULL,30),
  (1007,'刘备',2,1009,'2001-09-01','24500.00',NULL,10),
  (1008,'猪八戒',4,1004,'2007-04-19','30000.00',NULL,20),
  (1009,'罗贯中',1,NULL,'2001-11-17','50000.00',NULL,10),
  (1010,'吴用',3,1006,'2001-09-08','15000.00','0.00',30),
  (1011,'沙僧',4,1004,'2007-05-23','11000.00',NULL,20),
  (1012,'李逵',4,1006,'2001-12-03','9500.00',NULL,30),
  (1013,'小白龙',4,1004,'2001-12-03','30000.00',NULL,20),
  (1014,'关羽',4,1007,'2002-01-23','13000.00',NULL,10);
  
  
  
  -- 工资等级表
  CREATE TABLE salarygrade (
    grade INT PRIMARY KEY,   -- 级别
    losalary INT,  -- 最低工资
    hisalary INT -- 最高工资
  );
  
  -- 添加5个工资等级
  INSERT INTO salarygrade(grade,losalary,hisalary) VALUES 
  (1,7000,12000),
  (2,12010,14000),
  (3,14010,20000),
  (4,20010,30000),
  (5,30010,99990);
  
  -- 需求：
  
  -- 1.查询所有员工信息。查询员工编号，员工姓名，工资，职务名称，职务描述
  
  -- 2.查询员工编号，员工姓名，工资，职务名称，职务描述，部门名称，部门位置
     
  -- 3.查询员工姓名，工资，工资等级
  
  -- 4.查询员工姓名，工资，职务名称，职务描述，部门名称，部门位置，工资等级
  
  -- 5.查询出部门编号、部门名称、部门位置、部门人数
   
  -- 6.查询所有员工的姓名及其直接上级的姓名,没有领导的员工也需要查询
  ```

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

     2. 显式内连接

        * 语法

          ```sql
          select 字段列表 from 表名1 inner join 表名2 on 条件
          ```

        * 例如:

          ```sql
          SELECT * FROM emp INNER JOIN dept on emp.dept_id = dept.id;
          SELECT * FROM emp JOIN dept on emp.dept_id = dept.id;
          ```

     3. 内连接查询
        1. 从哪些表中查询数据
        2. 条件是什么
        3. 查询哪些字段

  2. 外连接查询

     1. 左外连接

        * 语法

          ```sql
          select 字段列表 from 表1 left outer join 表2 on 条件
          ```

        * 查询的是左边表所有记录, 以及其交集部分.

     2. 右外连接

        * 语法

          ```sql
          select 字段列表 from 表1 right outer join 表2 on 条件
          ```

        * 查询的是右边表所有记录, 以及其交集部分.

        > 表名换个位置,左外就变成了右外.

  3. 子查询

     * 概念: 查询中嵌套查询, 称嵌套查询为子查询.

       ```sql
       -- 查询工资最高的员工信息
       -- 1. 查询最高的工资是多少
       SELECT MAX(salary) FROM emp;
       -- 2. 查询员工信息, 并且工资是9000
       SELECT * FROM emp WHERE emp.salary = 50000;
       -- 综合
       SELECT * FROM emp WHERE emp.salary = (SELECT MAX(salary) FROM emp);
       ```

     * 子查询的不同情况

       1. 子查询结果是单行单列

          * 子查询可以作为条件使用运算符判断. (> < =等等)

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

            ```
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

    