与xml配置不同的地方，在于接口编写和SqlMapConfig.xml配置

UserDao接口

```java
package com.thp.dao;

import com.thp.domain.User;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;

import java.util.List;

public interface UserDao {
    @Select("select * from user")
    List<User> findAll();

    @Insert("insert into user(username,birthday,sex,address) values(#{username},#{birthday},#{sex},#{address})")
    void insertUser(User user);
}

```



SqlMapConfig.xml 

主要在mapper标签的写法

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/eesy_mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="645616987"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!--注解方式-->
        <mapper class="com.thp.dao.UserDao"/>
    </mappers>
</configuration>
```

