# 7-MyBatis多表操作

表之间的关系有几种:

1.  一对多 

   mybatis事务控制的分析 

举例:

 用户和订单,一对多

 一个人只能有一个身份证号,一对一

 一个学生可以被多个老师教, 多对多

特例: 如果每个订单只属于一个用户, 所以MyBatis就把多对一看成一对一.

```text
<!--封装account和user的resultMap-->
    <resultMap id="accountUserMap" type="com.swifter.domain.Account">
        <id property="id" column="aid"></id>
        <result property="uid" column="uid"></result>
        <result property="money" column="money"></result>
        <!--一对一的关系映射, 配置封装user的内容-->
        <association property="user" column="uid">
            <id property="id" column="id"></id>
            <result property="username" column="username"></result>
            <result property="address" column="address"></result>
            <result property="sex" column="sex"></result>
            <result property="birthday" column="birthday"></result>
        </association>
    </resultMap>

    <!-- ==============================================-->
    <!-- 配置查询所有操作 -->
    <select id="findAll" resultMap="accountUserMap">
        select u.*, a.id as aid, a.uid, a.money from account a, user u where u.id=a.UID
    </select>
```

```text
public class Account implements Serializable {
    private Integer id;
    private Integer uid;
    private Double money;
    //从表实体应该包含一个主表实体的对象引用
    private User user;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public Integer getUid() {
        return uid;
    }

    public void setUid(Integer uid) {
        this.uid = uid;
    }

    public Double getMoney() {
        return money;
    }

    public void setMoney(Double money) {
        this.money = money;
    }

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", uid=" + uid +
                ", money=" + money +
                ", user=" + user +
                '}';
    }
}
```

mybatis中的多表查询:

 示例: 用户和账户

 一个用户可以有多个账户

 一个账户只能属于一个用户

 步骤:

1. 先建立两张表, 一张用户表, 一张账户表.
2. 建立实体类, 用户实体类, 账户实体类
3. 建立两个配置文件
4. 实现配置

   当我们查询用户时, 可以同时得到用户下所包含的账户信息.

   当我们查询账户时,可以同时得到账户的所有信息.

```text
    <!--一对多  定义user resultMap-->
    <resultMap id="userAccountMap" type="user">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="address" column="address"></result>
        <result property="sex" column="sex"></result>
        <result property="birthday" column="birthday"></result>
<!--        配置user对象中account的映射-->
        <collection property="accounts" ofType="com.swifter.domain.Account">
            <id property="id" column="aid"></id>
            <result column="uid" property="uid"></result>
            <result column="money" property="money"></result>
        </collection>
    </resultMap>
    <!-- ==============================================-->
    <!-- 配置查询所有操作 -->
    <select id="findAll" resultMap="userAccountMap">
    select * from user u left join account a on u.id=a.uid
    </select>
```

```text
public class User implements Serializable{

    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;

    //一对多关系映射, 主表实体应该包含从表实体的集合引用
    private List<Account> accounts;

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

    public List<Account> getAccounts() {
        return accounts;
    }

    public void setAccounts(List<Account> accounts) {
        this.accounts = accounts;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                ", accounts=" + accounts +
                '}';
    }
}
```

示例: 用户和角色

步骤:

1. 两张表: 用户, 角色 还有中间表
2. 建立两个实体类, 用户角色各自包含对方一个集合引用
3. 两个配置文件: 用户配置文件, 角色配置文件
4. 实现配置:

   当查询用户时, 可以同时得到用户所包含的角色信息

   当查询角色时, 可以同时得到角色下的用户信息.

