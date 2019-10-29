###  mybatis初始教程(使用xml配置)

[toc]



##### 1.新建一个maven工程

##### 2.pom.xml输入一下内容

```xml
<dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>
    </dependencies>
```

##### 3.在src>java>下面新建两个个package命名为dao和domain

![文件目录](mybatis初始教程(使用xml配置).assets/文件目录.PNG)

##### 4.在dao文件新建接口

```java
package com.itheima.dao;

import com.itheima.domain.User;

import java.util.List;

/***
 * 用户的持久层接口
 */
public interface IUserDao {

    /***
     * 查找所有
     * @return
     */
    List<User> findAll();
}
```

##### 5.带domain新建类(model层)

**注意要继承Serializable接口**

```java
package com.itheima.domain;

import java.io.Serializable;
import java.util.Date;

//要实现 Serializable 接口才能序列化
public class User implements Serializable {
    private Integer id;
    private String username;
    private Date birthday;
    private  String sex;
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

##### 6.要完全按照dao层接口的 文件目录层次 去新建接口的xml配置文件

**IUserDao.xml文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 <mapper namespace="com.itheima.dao.IUserDao" >
    <!--配置查询所有-->
    <!--这个id是方法名，不可以乱写-->
    <select id="findAll" resultType="com.itheima.domain.User">
        select * from user ;
    </select>
</mapper>
```



##### 7.在resource文件夹下新建SqlMapConfig.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="mysql">
        <!--配置mysql环境-->
        <environment id="mysql">
            <!--配置事务类型-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源（连接池）-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/eesy_mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="645616987"/>
            </dataSource>
        </environment>
    </environments>
    <!--指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
    <!--如果是使用注解来配置的话，此处应该使用class属性指定被注解的dao全限定类名-->
    <mappers>
        <mapper resource="com/itheima/dao/IUserDao.xml"/>
        <!--这是使用注解方式-->
        <mapper class="com.itheima.dao.IMyTest"/>
    </mappers>
</configuration>
```



##### 8.进行测试，在test>java下新建测试类 MybatisTest

```java
package com.itheima.test;
import com.itheima.dao.IMyTest;
import com.itheima.dao.IUserDao;
import com.itheima.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.util.List;

/***
 * mybatis的入门
 */
public class MybatisTest {
    /***
     * 入门案例
     * @param args
     */
    public static void main(String[] args) throws Exception {
        //1.读取配置文件
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");

        //2.创建SqlSessionFactory工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(inputStream);

        //3.使用工厂生产SqlSession对象
        SqlSession session = factory.openSession();

        //4.使用SqlSession创建接口的代理对象
        IMyTest userDao = session.getMapper(IMyTest.class);

        //5.使用代理对象执行方法
        List<User> users = userDao.findMen();
        for(User user:users){
            System.out.println(user);
        }

        //6.释放资源
        session.close();
        inputStream.close();
    }
}

```

