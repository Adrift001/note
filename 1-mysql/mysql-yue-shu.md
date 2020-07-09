# MySQL约束

1. DQL: 查询语句 1. 排序查询 2. 聚合函数 3. 分组查询 4. 分页查询
2. 约束
3. 多表时间的关系
4. 范式
5. 数据库备份和还原

## DQL: 查询语句

1. 排序查询

   ```sql
   order by 子句;
       order by 排序字段1 排序方式2, 排序字段2 排序方式2...
   排序方式: 
       ASC: 升序
       DESC: 降序
   -- 按照数学排序, 如果数学一样, 则按照英语排序
   SELECT * FROM student ORDER BY math, english;
   -- 如果有多个排序条件, 则当前面的条件值一样时, 才会判断第二个条件.
   ```

2. 聚合函数: 将一列数据做整体, 进行纵向计算.
   1. count: 计算个数

      > 一般选择非空的列, 例如: 主键

   2. max: 计算最大值
   3. min: 计算最小值
   4. avg: 计算平均值

> 聚合函数的计算, 排除null值了.
>
> 解决方法:
>
> 1. 选择不包含非空的列
>    1. IFNULL函数

```sql
  SELECT COUNT(`name`) FROM student;

  SELECT COUNT(IFNULL(english,0)) FROM student;

  SELECT MAX(math) FROM student;

  SELECT MIN(math) FROM student;

  SELECT SUM(math) FROM student;

  SELECT AVG(math) FROM student;
```

1. 分组查询

   ```sql
   group by 分组字段;
   ```

   > 分组之后可以查询的字段: 分组字段, 聚合函数
   >
   > where 和 having区别:
   >
   > ​ where在分组之前限定, 如果不满足条件, 不参与分组
   >
   > ​ having在分组之后限定, 如果不满足having条件, 不被查询

   ```sql
   -- 按性别分组并计算平均分
   SELECT sex, AVG(math), COUNT(id) FROM student GROUP BY sex;

   -- 按性别分组并计算分数大于70分的平均分
   SELECT sex, AVG(math), COUNT(id) FROM student WHERE math > 70 GROUP BY sex;

   -- 按性别分组并计算分数大于70分的平均分, 并且分组之后分数大于2
   SELECT sex, AVG(math), COUNT(id) FROM student WHERE math > 70 GROUP BY sex HAVING COUNT(id) > 2;
   SELECT sex, AVG(math), COUNT(id) 人数 FROM student WHERE math > 70 GROUP BY sex HAVING 人数 > 2;
   ```

2. 分页查询

   ```sql
   limit 开始索引, 查询的条数
   ```

   ```sql
   -- 每页显示三条记录
   SELECT * FROM student LIMIT 0,3; -- 第一页
   SELECT * FROM student LIMIT 3,3; -- 第二页
   -- 公式: 开始的索引 = (当前页码 - 1) * 每页数
   SELECT * FROM student LIMIT 6,3;  -- 第三页
   ```

   > limit是一个MySQL的`方言`

## 约束

* 概念: 对表中的数据进行限定, 保证数据的正确性, 有效性和完整性.
* 分类:

  1. 主键约束: primary key
     1. 非空约束: not null
        1. 唯一约束: unique
           1. 外键约束: foreign key

  **主键约束**

  > 1. 非空且唯一
  > 2. 一个表只能有一个字段是主键
  > 3. 主键就是表中记录的唯一标识

  1. 创建表时添加主键

     ```sql
     CREATE TABLE stu(id INT PRIMARY KEY, name VARCHAR(20));
     ```

  2. 创建表之后添加主键

     ```sql
     ALTER TABLE stu MODIFY id INT PRIMARY KEY;
     ```

  3. 删除主键

     ```sql
     ALTER TABLE stu MODIFY id int; -- 错误,不生效
     ALTER TABLE stu DROP PRIMARY KEY;
     ```

  4. 自动增长
     * 如果某一列是数值类型, 使用`auto_increment`可以用来完成值的自动增长.

       ```sql
       -- 创建表时添加自动增长
       CREATE TABLE stu(id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(20));

       -- 删除自动增长
       ALTER TABLE stu MODIFY id INT;

       -- 添加自动增长
       ALTER table stu MODIFY id INT PRIMARY KEY AUTO_INCREMENT;
       ```

  **非空约束: not null**

  1. 创建表时添加约束

     ```sql
     CREATE TABLE stu(id INT, name VARCHAR(20) NOT NULL);
     ```

  2. 创建表完之后添加非空约束

     ```sql
     ALTER TABLE stu MODIFY `name` VARCHAR(20) NOT NULL;
     ALTER TABLE stu CHANGE `name` `name` VARCHAR(20) NOT NULL;
     ```

  3. 删除非空约束

     ```sql
     ALTER TABLE stu MODIFY `name` VARCHAR(20);
     ALTER TABLE stu CHANGE `name` `name` VARCHAR(20);
     ```

  **唯一约束: unique**

  同一列的值不能重复

  1. 创建表时添加唯一约束

     ```sql
     CREATE TABLE stu(id INT, phone VARCHAR(20) UNIQUE);
     -- 注意: MySQL中, 唯一约束限定的列的值可以有多个null
     ```

  2. 创建表后添加

     ```sql
     ALTER TABLE stu MODIFY phone VARCHAR(20) UNIQUE;
     ```

  3. 删除唯一约束

     ```sql
     ALTER TABLE stu MODIFY phone VARCHAR(20); -- 不能通过这种方式
     ALTER TABLE stu DROP INDEX phone;
     ```

> 注意: MySQL中唯一约束限定的列的值可以有多个null

```text
###     外键约束
```

```sql
CREATE TABLE emp (
    id INT PRIMARY KEY AUTO_INCREMENT,
    NAME VARCHAR(30),
    age INT,
    dep_name VARCHAR(30),
    dep_location VARCHAR(30)
);

-- 添加数据
INSERT INTO emp (NAME, age, dep_name, dep_location) VALUES ('张三', 20, '研发部', '广州');
INSERT INTO emp (NAME, age, dep_name, dep_location) VALUES ('李四', 21, '研发部', '广州');
INSERT INTO emp (NAME, age, dep_name, dep_location) VALUES ('王五', 20, '研发部', '广州');
INSERT INTO emp (NAME, age, dep_name, dep_location) VALUES ('老王', 20, '销售部', '深圳');
INSERT INTO emp (NAME, age, dep_name, dep_location) VALUES ('大王', 22, '销售部', '深圳');
INSERT INTO emp (NAME, age, dep_name, dep_location) VALUES ('小王', 18, '销售部', '深圳');

-- 数据有冗余

-- 解决方案：分成 2 张表
-- 创建部门表(id,dep_name,dep_location)
-- 一方，主表
create table department(
    id int primary key auto_increment,
    dep_name varchar(20),
    dep_location varchar(20)
);

-- 创建员工表(id,name,age,dep_id)
-- 多方，从表
create table employee(
    id int primary key auto_increment,
    name varchar(20),
    age int,
    dep_id int,  -- 外键对应主表的主键
    CONSTRAINT emp_dept_fk FOREIGN KEY (dep_id) REFERENCES department(id)
);

-- 添加 2 个部门
insert into department values(null, '研发部','广州'),(null, '销售部', '深圳');
select * from department;

-- 添加员工,dep_id 表示员工所在的部门
INSERT INTO employee (NAME, age, dep_id) VALUES ('张三', 20, 1);
INSERT INTO employee (NAME, age, dep_id) VALUES ('李四', 21, 1);
INSERT INTO employee (NAME, age, dep_id) VALUES ('王五', 20, 1);
INSERT INTO employee (NAME, age, dep_id) VALUES ('老王', 20, 2);
INSERT INTO employee (NAME, age, dep_id) VALUES ('大王', 22, 2);
INSERT INTO employee (NAME, age, dep_id) VALUES ('小王', 18, 2);
select * from employee;

-- 删除外键
ALTER TABLE employee DROP FOREIGN KEY emp_dept_fk;

-- 添加外键
ALTER TABLE employee ADD CONSTRAINT emp_dept_fk FOREIGN KEY (dep_id) REFERENCES department(id);

emp_dept_fk是外键名称

-- 添加外键, 设置级联更新
ALTER TABLE employee ADD CONSTRAINT emp_dept_fk FOREIGN KEY (dep_id) REFERENCES department(id) ON UPDATE CASCADE;

-- 添加外键, 设置级联删除
ALTER TABLE employee ADD CONSTRAINT emp_dept_fk FOREIGN KEY (dep_id) REFERENCES department(id) ON DELETE CASCADE;

-- 同时设置
ALTER TABLE employee ADD CONSTRAINT emp_dept_fk FOREIGN KEY (dep_id) REFERENCES department(id) ON UPDATE CASCADE ON DELETE CASCADE;
```

## 数据库的设计

1. 多表之间的关系
   1. 分类
      1. 一对一:
      2. 人和身份证
      3. 分析: 一个人有一个身份证, 一个身份证对应一个人
         1. 一对多\(多对一\):
         2. 部门和员工
         3. 分析: 一个部门有多个员工, 一个员工只能对应一个部门
         4. 多对多:
         5. 如: 学生和课程
         6. 分析: 一个学生可以选择多个课程, 一个课程可以被多个学生选择
   2. 实现:

      1. 一对多:
      2. 部门和员工
      3. 实现方式: 在多的一方建立外键, 指向一的一方的主键.
         1. 多对多:
         2. 学生和课程
         3. 实现方式: 多对多关系实现需要借助第三张中间表,中间表至少包含两个字段, 这两个字段作为第三张表的外键, 分别指向其他两张表的主键.
         4. 一对一:
         5. 学生和身份证
         6. 实现方式: 可以在任意一方添加唯一\(unique\)外键指向另一方的主键.
      4. 案例: 

      分类表

      | cid | ame |
      | :--- | :--- |
      |  |  |

      线路表

      | rid | name | price | cid |
      | :--- | :--- | :--- | :--- |
      |  |  |  |  |

      用户表

      | uid | username | password |
      | :--- | :--- | :--- |
      |  |  |  |

      中间表

      | rid | uid |
      | :--- | :--- |
      |  |  |

      分类1 线路表N 一对多

      线路表N 用户M 多对多, 中间表
2. 数据库设计的范式
   1. 第一范式: 每一列都是不可分割的原子数据项
   2. 第二范式: 在1NF的基础上,非码属性必须完全依赖于候选码\(在1NF基础上消除非主属性对主码的部分函数依赖\)
   3. 第三方是: 在2NF的基础上, 任何非主属性不依赖于其他非主属性\(在2NF基础上消除传递依赖\)

## 数据库备份和还原

1. 命令行
   * mysqldump -u用户名 -p密码 数据库名称 &gt; 保存路径
   * 还原
     1. 登录数据库
     2. 创建数据库
     3. 使用数据库
     4. 执行文件. source 文件路径
2. 图形化工具

