# 4-MyBatis Dao开发

```text
    <!--resource 用户指定配置文件的位置,是按照类路径的写法来写, 并且必须存在于类路径下-->
    <!--<properties resource="jdbcConfig.properties">-->

    <!-- 按照URL方式来写地址 -->
    <properties url="file:///User/document/...">
    <!--<property name="driver" value="com.mysql.jdbc.Driver"/>-->
    <!--<property name="url" value="jdbc:mysql://localhost:3306/eesy?characterEncoding=UTF-8"/>-->
    <!--<property name="username" value="root"/>-->
    <!--<property name="password" value="jingxuetao"/>-->
    </properties>
```

jdbcConfig.properties

```text
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/eesy?characterEncoding=UTF-8
jdbc.username=root
jdbc.password=jingxuetao
```

typeAliases使用

```text
    <!--使用typeAliases配置别名, 他只能配货车domain中的别名-->
    <typeAliases>
        <typeAlias type="com.swifter.domain.User" alias="user"></typeAlias>
    </typeAliases>
```

package使用

```text
    <mappers>
        <package name="com.swifter.dao"/>
    </mappers>
```

