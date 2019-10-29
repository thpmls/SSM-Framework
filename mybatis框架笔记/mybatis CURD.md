# mybatis CURD

**下面的写法都是使用注解方式配置，在userDao接口里面写**

[toc]



### select

```java
public interface UserDao {
    @Select("select * from user")
    List<User> findAll();
}
```

要记得去SqlMapConfig.xml 设置配置 UserDao

### insert

```java
public interface UserDao {
    @Insert("insert into user(username,birthday,sex,address) values(#{username},#{birthday},#{sex},#{address})")
    // User类的getter，setter需要使用idea自动生成，才可以在values 后面写username，birthday...
    //如果不是自动生成就需要写get方法中get后面的单词，例如getUsername(),则写成Username
    void insertUser(User user);
}
```

在测试类里面进行insert测试

```java
package com.thp.myTest;

import com.thp.dao.UserDao;
import com.thp.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.Date;
import java.util.List;

public class MybatisTest {
    private InputStream in;
    private SqlSessionFactoryBuilder builder;
    private SqlSessionFactory factory;
    private SqlSession session;
    private UserDao userDao;

    @Before
    public void init() throws Exception{
         in = Resources.getResourceAsStream("SqlMapConfig.xml");
         builder = new SqlSessionFactoryBuilder();
         factory = builder.build(in);
         session = factory.openSession();
         userDao = session.getMapper(UserDao.class);
    }

    @After
    public void destroy() throws IOException{
        session.close();
        in.close();
    }
    
    
//测试insert
    @Test
    public void testInsert(){
        User user = new User();
        user.setAddress("佛山市");
        user.setSex("女");
        user.setBirthday(new Date());
        user.setUsername("THP");
        userDao.insertUser(user);

        //手动提交事务
        //自动提交 session = factory.openSession(true);
        session.commit();
    }
}

```



### update

```java
public interface UserDao {
    @Update("update user set username=#{username} where id=#{id}")
    void updateUser(User user);
}

```

测试(其余代码与insert测试代码相同)

```java
@Test
    public void testUpdate(){
        User user = new User();
        user.setId(50);
        user.setUsername("测试update");
        userDao.updateUser(user);

        //手动提交事务
        //自动提交 session = factory.openSession(true);
        session.commit();
    }
```



### Delete

```java
public interface UserDao {
     @Delete("delete from user where id=#{id}")
    void delete(int id);
}
```



```java
@Test
    public void testDelete(){
        userDao.delete(41);
        session.commit();
    }
```

