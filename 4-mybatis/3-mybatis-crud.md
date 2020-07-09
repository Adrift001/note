# 3-MyBatis CRUD

## dao 

```java
package com.swifter.dao;

import com.swifter.domain.QueryVo;
import com.swifter.domain.User;

import java.util.List;

/**
 * @author 黑马程序员
 * @Company http://www.ithiema.com
 *
 * 用户的持久层接口
 */
public interface UserDao {

    /**
     * 查询所有操作
     * @return
     */
    List<User> findAll();

    /**
     * 保存用户
     * @param user
     */
    void saveUser(User user);

    /**
     * 更新用户
     * @param user
     */
    void updateUser(User user);

    /**
     * 删除用户
     * @param id
     */
    void deleteUser(Integer id);

    /**
     * 根据ID查用户
     * @param id
     */
    User findById(Integer id);

    /**
     * 模糊查询
     * @param name
     * @return
     */
    List<User> findByName(String name);

    /**
     * 总记录数
     * @return
     */
    int findTotal();

    /**
     * 根据QueryVo中的条件查询用户
     * @param vo
     * @return
     */
    List<User> findUserByVo(QueryVo vo);
}
```

## domain

QueryVo.java

```java
package com.swifter.domain;

public class QueryVo {

    private User user;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }
}
```

User.java

```java
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

## xml

com/swifter/dao

```markup
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE mapper

        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"

        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.swifter.dao.UserDao">

    <!-- 当属性名和列名不一致时 -->
    <!-- 配置结果列名和实体类的属性名的对应关系 -->
    <resultMap id="userMap" type="com.swifter.domain.User">
        <!-- 主键对应 -->
        <id property="userId" column="id"></id>
        <!-- 非主键字段 -->
        <result property="userName" column="username"></result>
        <result property="userAddress" column="address"></result>
        <result property="userSex" column="sex"></result>
        <result property="userBirthday" column="birthday"></result>
    </resultMap>

    <!-- 配置查询所有操作 -->
    <select id="findAll" resultMap="userMap">
--         select id as userId ...
        select * from user
    </select>
    <!-- ==============================================-->
    <!-- 配置查询所有操作 -->
    <select id="findAll" resultType="com.swifter.domain.User">
    select * from user
    </select>

    <!-- 插入用户-->
    <insert id="saveUser" parameterType="com.swifter.domain.User">
--         配置插入用户后的ID
        <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
            select last_insert_id();
        </selectKey>
        insert into user(username, address, sex, birthday) values(#{username}, #{address}, #{sex}, #{birthday})
    </insert>

    <!-- 更新用户-->
    <update id="updateUser" parameterType="com.swifter.domain.User">
        update user set username=#{username}, address=#{address}, sex=#{sex}, birthday=#{birthday} where id = #{id}
    </update>

    <delete id="deleteUser" parameterType="java.lang.Integer">
--         id就是个占位符, id, uid, userId都可以
        delete from user where id = #{id}
    </delete>

    <!-- 根据ID查用户 -->
    <select id="findById" parameterType="int" resultType="com.swifter.domain.User">
--         id就是个占位符, id, uid, userId都可以
        select * from user where id = #{id}
    </select>
<!--模糊查询-->
    <select id="findByName" parameterType="string" resultType="com.swifter.domain.User">
        select * from user where username like #{name}
    </select>

<!--    获取总记录条数-->
    <select id="findTotal" resultType="int">
        select count(id) from user
    </select>

<!--    根据QueryVo的条件查询用户-->
    <select id="findUserByVo" parameterType="com.swifter.domain.QueryVo" resultType="com.swifter.domain.User">
        select * from user where username like #{user.username}
    </select>
</mapper>
```

## Test

```java
import com.swifter.dao.UserDao;
import com.swifter.domain.QueryVo;
import com.swifter.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;

import java.io.InputStream;
import java.util.Date;
import java.util.List;

public class Test {

    private InputStream in;
    private SqlSessionFactory factory;
    private SqlSession session;
    private UserDao userDao;

    @Before
    public void init() throws Exception {
        //1. 读取配置文件
        in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2. 创建SQLSessionFactory
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        factory = builder.build(in);
        //3. 使用工厂生产SQLSession对象
        session = factory.openSession();
        //4. 使用SQLSession创建dao接口的代理对象
        userDao = session.getMapper(UserDao.class);
    }

    @After
    public void destory() throws Exception {
        //提交事务
        session.commit();
        session.close();
        in.close();
    }

    @org.junit.Test
    public void testFindAll() throws Exception {
        //5.使用代理对象执行方法
        List<User> all = userDao.findAll();
        for (User user : all) {
            System.out.println(user);
        }
    }

    @org.junit.Test
    public void testInsertUser() throws Exception {
        User user = new User();
        user.setUsername("哈哈哈");
        user.setSex("男");
        user.setAddress("北京市朝阳区");
        user.setBirthday(new Date());
        System.out.println(user);
        userDao.saveUser(user);
        System.out.println(user);
    }

    @org.junit.Test
    public void updateUser() {
        User user = new User();
        user.setId(50);
        user.setUsername("哈哈哈");
        user.setSex("女");
        user.setAddress("北京市朝阳区");
        user.setBirthday(new Date());
        userDao.updateUser(user);
    }

    @org.junit.Test
    public void deleteUser() {
        userDao.deleteUser(50);
    }

    @org.junit.Test
    public void findById() {
        User user = userDao.findById(48);
        System.out.println(user);
    }

    @org.junit.Test
    public void findByName() {
        List<User> users = userDao.findByName("%王%");
        for (User user: users) {
            System.out.println(user);
        }
    }

    @org.junit.Test
    public void findTotal() {
        int total = userDao.findTotal();
        System.out.println(total);
    }

    @org.junit.Test
    public void findByVo() {
        QueryVo queryVo = new QueryVo();
        User user = new User();
        user.setUsername("%王%");
        queryVo.setUser(user);
        List<User> users = userDao.findUserByVo(queryVo);
        for (User u: users) {
            System.out.println(u);
        }
    }
}
```

