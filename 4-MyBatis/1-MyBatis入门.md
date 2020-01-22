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

