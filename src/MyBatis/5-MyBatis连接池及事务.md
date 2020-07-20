1. mybatis中的连接池以及事务控制

   mybatis中连接池使用及分析

   mybatis事务控制的分析

2. mybatis基于XML配置的动态SQL语句使用

   mappers配置文件中的几个标签:

   ​	<if> <where> <foreach> <sql>

3. mybatis的多表查询
   1. 一对多
   2. 一对一
   3. 多对多

1. 连接池:

   我们在实际开发中都会使用连接池, 可以减少我们获取链接所消耗的时间.

   连接池就是一个容器,就是一个集合, 必须是线程安全的,不能两个线程拿到同一个链接.

2. mybatis连接池

   SqlMapConfig.xml, dataSource标签的type属性

   1. POOLED, 采用传统的javax.sql.DataSource规范中的连接池

   2. UNPOOLED, 虽然也实现了javax.sql.DataSource, 但是没有池的思想

   3. JNDI, 采用服务器提供的JNDI技术实现, 来获取DataSource对象,不同服务器所拿到的对象不一样

      如果不是web或者maven的war工程,不能使用.