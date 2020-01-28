```
<!--
    <select id="findUserByCondition" parameterType="com.swifter.domain.User" resultType="com.swifter.domain.User">
        select * from user where 1=1
        <if test="username != null">
            and username = #{username}
        </if>
        <if test="sex != null">
            and sex = #{sex}
        </if>
    </select>
    -->
    <select id="findUserByCondition" parameterType="com.swifter.domain.User" resultType="com.swifter.domain.User">
        select * from user
        <where> --避免加上1=1, 让SQL看起来更清晰简洁
            <if test="username != null">
                and username = #{username}
            </if>
            <if test="sex != null">
                and sex = #{sex}
            </if>
        </where>
    </select>

    <sql id="defaultUser">
        select * from user
    </sql>

    <select id="findUserInIds" parameterType="com.swifter.domain.QueryVo" resultType="com.swifter.domain.User">
--         select * from user
        <include refid="defaultUser"></include>
        <where>
            <if test="ids != null and ids.size()>0">
                <foreach collection="ids" open="and id in (" close=")" item="id" separator=",">
                    #{id}
                </foreach>
            </if>
        </where>
    </select>
```

```
/**
     * 根据传入的参数条件查询
     * @return
     */
    List<User> findUserByCondition(User user);

    /**
     * 根据queryvo中的ID集合, 实现查询用户列表
     * @param vo
     * @return
     */
    List<User> findUserInIds(QueryVo vo);
```

```
public class QueryVo {
    private List<Integer> ids;

    public List<Integer> getIds() {
        return ids;
    }

    public void setIds(List<Integer> ids) {
        this.ids = ids;
    }
}
```

```
    @org.junit.Test
    public void findUserByCondition() {
        User user = new User();
        user.setUsername("老王");
        user.setSex("女");
        List<User> users = userDao.findUserByCondition(user);
        for(User u: users) {
            System.out.println(u);
        }
    }

    @org.junit.Test
    public void findUserInIds() {
        QueryVo queryVo = new QueryVo();
        List<Integer> integers = new ArrayList<Integer>();
        integers.add(41);
        integers.add(42);
        queryVo.setIds(integers);
        List<User> users = userDao.findUserInIds(queryVo);
        for(User u: users) {
            System.out.println(u);
        }
    }
```

