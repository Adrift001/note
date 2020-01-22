1. 创建maven工程并导入坐标
2. 创建实体类和dao接口
3. 创建mybatis的主配置文件`SqlMapConfig.xml`
4. 创建映射配置文件`UserDao.xml`

```
.
├── main
│   ├── java
│   │   └── com
│   │       └── swifter
│   │           ├── dao
│   │           │   └── UserDao.java
│   │           └── domain
│   │               └── User.java
│   └── resources
│       ├── SqlMapConfig.xml
│       ├── com
│       │   └── swifter
│       │       └── dao
│       │           └── UserDao.xml
│       └── log4j.properties
└── test
    └── java
        └── com
            └── swifter
                └── test
                    └── MybatisTest.java
```

```
SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(32) NOT NULL COMMENT '用户名称',
  `birthday` datetime DEFAULT NULL COMMENT '生日',
  `sex` char(1) DEFAULT NULL COMMENT '性别',
  `address` varchar(256) DEFAULT NULL COMMENT '地址',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=49 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of user
-- ----------------------------
BEGIN;
INSERT INTO `user` VALUES (41, '老王', '2018-02-27 17:47:08', '男', '北京');
INSERT INTO `user` VALUES (42, '小二王', '2018-03-02 15:09:37', '女', '北京金燕龙');
INSERT INTO `user` VALUES (43, '小二王', '2018-03-04 11:34:34', '女', '北京金燕龙');
INSERT INTO `user` VALUES (45, '传智播客', '2018-03-04 12:04:06', '男', '北京金燕龙');
INSERT INTO `user` VALUES (46, '老王', '2018-03-07 17:37:26', '男', '北京');
INSERT INTO `user` VALUES (48, '小马宝莉', '2018-03-08 11:44:00', '女', '北京修正');
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;
```

1. maven坐标

   ```
       <dependencies>
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.5.3</version>
           </dependency>
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>5.1.6</version>
           </dependency>
           <dependency>
               <groupId>log4j</groupId>
               <artifactId>log4j</artifactId>
               <version>1.2.12</version>
           </dependency>
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.10</version>
           </dependency>
       </dependencies>
   ```

2. 创建实体类和dao接口

   ```
   package com.swifter.domain;
   
   import java.io.Serializable;
   import java.util.Date;
   
   /**
    * @author 黑马程序员
    * @Company http://www.ithiema.com
    */
   public class User implements Serializable{
   
       private Integer id;
       private String username;
       private Date birthday;
       private String sex;
       private String address;
   
       public Integer getId() {
           return id;
       }
   
       public void setId(Integer id) {
           this.id = id;
       }
   
       public String getUsername() {
           return username;
       }
   
       public void setUsername(String username) {
           this.username = username;
       }
   
       public Date getBirthday() {
           return birthday;
       }
   
       public void setBirthday(Date birthday) {
           this.birthday = birthday;
       }
   
       public String getSex() {
           return sex;
       }
   
       public void setSex(String sex) {
           this.sex = sex;
       }
   
       public String getAddress() {
           return address;
       }
   
       public void setAddress(String address) {
           this.address = address;
       }
   
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", username='" + username + '\'' +
                   ", birthday=" + birthday +
                   ", sex='" + sex + '\'' +
                   ", address='" + address + '\'' +
                   '}';
       }
   }
   ```

   ```
   package com.swifter.dao;
   
   import com.swifter.domain.User;
   
   import java.util.List;
   
   /**
    * @author 黑马程序员
    * @Company http://www.ithiema.com
    *
    * 用户的持久层接口
    */
   public interface IUserDao {
   
       /**
        * 查询所有操作
        * @return
        */
       List<User> findAll();
   }
   ```

3. 创建SqlMapConfig.xml文件

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <!-- mybatis的主配置文件 -->
   <configuration>
       <!-- 配置环境 -->
       <environments default="mysql">
           <!-- 配置mysql的环境-->
           <environment id="mysql">
               <!-- 配置事务的类型-->
               <transactionManager type="JDBC"></transactionManager>
               <!-- 配置数据源（连接池） -->
               <dataSource type="POOLED">
                   <!-- 配置连接数据库的4个基本信息 -->
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://localhost:3306/eesy?characterEncoding=UTF-8"/>
                   <property name="username" value="root"/>
                   <property name="password" value="jingxuetao"/>
               </dataSource>
           </environment>
       </environments>
   
       <!-- 指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件 -->
       <mappers>
           <mapper resource="com/itheima/dao/IUserDao.xml"/>
       </mappers>
   </configuration>
   ```

4. 创建映射配置文件

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.swifter.dao.IUserDao">
   		<!--namespace dao接口的全限定类名-->
       <!--配置查询所有-->
       <select id="findAll" resultType="com.swifter.domain.User">
           select * from user
       </select>
   </mapper>
   ```

5. 测试

   ```
   package com.swifter;
   
   import com.swifter.dao.IUserDao;
   import com.swifter.domain.User;
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   import java.io.InputStream;
   import java.util.List;
   
   public class MyBatisTest {
       public static void main(String[] args) throws Exception {
           //1. 读取配置文件
           InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
           //2. 创建SQLSessionFactory
           SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
           SqlSessionFactory factory = builder.build(in);
           //3. 使用工厂生产SQLSession对象
           SqlSession session = factory.openSession();
           //4. 使用SQLSession创建dao接口的代理对象
           IUserDao userDao = session.getMapper(IUserDao.class);
           //5.使用代理对象执行方法
           List<User> all = userDao.findAll();
           for (User user : all) {
               System.out.println(user);
           }
           //6.释放资源
           session.close();
           in.close();
       }
   }
   ```

> 注意实现
>
> 1. 创建UserDao.xml 和 UserDao.java时, 名称是为了和之前的知识保持一致,mybatis把持久层操作接口名称和映射文件(Mapper) UserMapper和UserDao 应该只是名称的区别
> 2. 在idea中创建目录时和包不一样, 包创建com.swifter.dao是三级目录, 目录创建是一级目录.
> 3. mybatis映射配置文件必须和dao接口包结构相同
> 4. 映射配置文件的mapper标签namespace属性取值,必须是dao接口的全限定类名.
> 5. 映射配置文件的操作配置,id属性取值必须是dao接口的方法名.