## 传值方式

**按序号**

[sql]
Public User selectUser(String name,String area);
对应的Mapper.xml
[sql]

```xml
<select id="selectUser" resultMap="BaseResultMap">
select * from user_user_t where user_name = #{0} and user_area=#{1}
</select>
```

#{0}代表接收的是dao层中的第一个参数，#{1}代表dao层中第二参数

**按Map**

Dao层的函数方法

Public User selectUser(Map paramMap);

```xml
<select id=" selectUser" resultMap="BaseResultMap">
select * from user_user_t where user_name = #{userName，jdbcType=VARCHAR} and user_area=#{userArea,jdbcType=VARCHAR}
</select>
```

Service层调用

Private User xxxSelectUser(){
Map paramMap=new hashMap();
paramMap.put(“userName”,”对应具体的参数值”);
paramMap.put(“userArea”,”对应具体的参数值”);
User user=xxx. selectUser(paramMap);
}
见到接口方法不能直接的知道要传的参数是什么。

**按注解**

[sql] view plain copy
Public User selectUser(@param(“userName”)Stringname,@param(“userArea”)String area);
[sql] 

```xml
<select id=" selectUser" resultMap="BaseResultMap">
select * from user_user_t where user_name = #{userName，jdbcType=VARCHAR} and user_area=#{userArea,jdbcType=VARCHAR}
</select>
```

## 打印Mybatis日志

```xml
# 指定日志记录
logging:
  level:
    #指定dao层，mapper文件所在包
    com.hozuo.xdw: debug
```

## Invalid bound statement (not found)错误的可能原因

检查Mapper interface所在的路径与xml文件中命名空间是否相同

```
<mapper namespace="com.hozuo.xdw.mapper.HardwareMapper">
```

检查mybatis扫描包路径是否和xml路径相同

```
# mybatis
mybatis:
  # 注意：一定要对应mapper映射xml文件的所在路径
  mapper-locations: classpath:/mapper/*.xml
```

检查Mapper interface的方法和xml中名称是否相同

Mapper interface的方法返回值是List<T>,而select元素没有正确配置ResultMap,或者只配置ResultType

检查*Mapper.xml文件是否放在resources目录下，如果放在了宝目录下，需要在pom文件中添加资源文件位置。

（由于在 **Maven** 工程中，**XML** 配置文件建议写在 **resources** 目录下，**Maven** 在运行时会忽略包下的 **XML** 文件。需要在 **pom.xml** 文件中重新指明资源文件位置）

```
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
    <!-- 重新指明资源文件位置 -->
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
        </resource>
    </resources>
</build>
```

## 多数据源{csdn}{github}

### 环境

springboot 2.3.1

Mybatis 2.1.3

MariaDB 10.4.13

IDEA 2020.1

jdk 1.8

### 方法

MyBatis配置多数据源可以用分包的方法，将不同的.xml文件放在不同的包中，关闭主启动类自动扫描，配置多个数据源配置文件分别扫描对应的包，使不同的数据源使用对应的方法。

### 项目结构

mapper文件夹中分包对应两个测试数据源，mapper.xml文件放在了资源目录下。mapper.xml也可以放在对应的包路径下，需要指定资源路径。

<img src="C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200623115324109.png" alt="image-20200623115324109" style="zoom:80%;" />

#### pom.xml

需要添加数据库依赖和Mybatis依赖。Maven 在运行时会忽略包下的 xml文件，将xml文件放在包目录下时需要指定资源文件位置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.hozuo</groupId>
    <artifactId>test</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>testMybatisDatasource</name>
    <description>测试mybatis多数据源配置</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- 数据库驱动依赖 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.19</version>
        </dependency>

        <!-- MyBatis依赖 -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.3</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
        <!-- 重新指明资源文件位置，资源文件放在包目录下需要 -->
<!--        <resources>-->
<!--            <resource>-->
<!--                <directory>src/main/java</directory>-->
<!--                <includes>-->
<!--                    <include>**/*.xml</include>-->
<!--                </includes>-->
<!--            </resource>-->
<!--            <resource>-->
<!--                <directory>src/main/resources</directory>-->
<!--            </resource>-->
<!--        </resources>-->
    </build>
</project>
```

#### application.yml

数据源配置test1和test2，对应两个测试数据源

```yml
server:
  port: 2020

spring:
  datasource:
    test1:
      jdbc-url: jdbc:mysql://localhost:3306/testD1?serverTimezone=CTT&useUnicode=true&characterEncoding=utf8
      driverClassName: com.mysql.cj.jdbc.Driver
      username: root
      password: root
    test2:
      jdbc-url: jdbc:mysql://localhost:3306/testD2?serverTimezone=CTT&useUnicode=true&characterEncoding=utf8
      driverClassName: com.mysql.cj.jdbc.Driver
      username: root
      password: root

mybatis:
  mapper-locations: classpath*:/mapper/**.xml
```

数据库地址一般使用url，此时要使用jdbc-url，使用url会报如下错误

```cmd
### Error querying database.  Cause: java.lang.IllegalArgumentException: jdbcUrl is required with driverClassName.
### The error may exist in com/hozuo/testMybatisDatasource/mapper/testD1/TestD1Mapper.xml
### The error may involve com.hozuo.testMybatisDatasource.mapper.testD1.TestD1Mapper.select
### The error occurred while executing a query
### Cause: java.lang.IllegalArgumentException: jdbcUrl is required with driverClassName.] with root cause
```

使用com.mysql.jdbc.Driver会提示如下警告

```html
Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.

加载类com.mysql.jdbc.Driver是不推荐的。新的驱动类是com.mysql.cj.jdbc.Driver. 驱动程序通过SPI自动注册，通常不需要手动加载驱动类。
```

#### **DataSourceConfig1.java**

注意设置mybatis的.xml所在位置

```java
package com.hozuo.testMybatisDatasource.config;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;

import javax.sql.DataSource;

/**
 * DataSourceConfig1
 * <p>2020/6/23
 * <p>主数据源配置
 * @author hozuo
 * @version 1.0
 */
@Configuration
@MapperScan(value  = "com.hozuo.testMybatisDatasource.mapper.testD1", sqlSessionFactoryRef = "test1SqlSessionFactory")
public class DataSourceConfig1 {

    // 表示这个数据源是默认数据源
    @Primary
    @Bean(name = "test1DataSource")
    // 读取application.properties中的配置参数映射成为一个对象
    // prefix表示yml配置文件中参数的前缀
    @ConfigurationProperties(prefix = "spring.datasource.test1")
    public DataSource getDateSource1() {
        return DataSourceBuilder.create().build();
    }

    @Primary
    @Bean(name = "test1SqlSessionFactory")
    // @Qualifier表示查找Spring容器中名字为test1DataSource的对象
    public SqlSessionFactory test1SqlSessionFactory(@Qualifier("test1DataSource") DataSource datasource)
            throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(datasource);
        bean.setMapperLocations(
                // 设置mybatis的.xml所在位置
                new PathMatchingResourcePatternResolver().getResources("classpath*:mapper/testD1/*.xml"));
        return bean.getObject();
    }

    @Primary
    @Bean("test1SqlSessionTemplate")
    public SqlSessionTemplate test1SqlSessionTemplate(
            @Qualifier("test1SqlSessionFactory") SqlSessionFactory sessionFactory) {
        return new SqlSessionTemplate(sessionFactory);
    }
}
```

#### DataSourceConfig2.java

```java
package com.hozuo.testMybatisDatasource.config;

import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;

import javax.sql.DataSource;

/**
 * DataSourceConfig2
 * <p>2020/6/23
 * <p>从数据源配置
 * @author hozuo
 * @version 1.0
 */
@Configuration
@MapperScan(value  = "com.hozuo.testMybatisDatasource.mapper.testD2", sqlSessionFactoryRef = "test2SqlSessionFactory")
public class DataSourceConfig2 {

    @Bean(name = "test2DataSource")
    @ConfigurationProperties(prefix = "spring.datasource.test2")
    public DataSource getDateSource2() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "test2SqlSessionFactory")
    public SqlSessionFactory test2SqlSessionFactory(@Qualifier("test2DataSource") DataSource datasource)
            throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(datasource);
        bean.setMapperLocations(
                new PathMatchingResourcePatternResolver().getResources("classpath*:mapper/testD2/*.xml"));
        return bean.getObject();
    }

    @Bean("test2SqlSessionTemplate")
    public SqlSessionTemplate test2SqlSessionTemplate(
            @Qualifier("test2SqlSessionFactory") SqlSessionFactory sessionFactory) {
        return new SqlSessionTemplate(sessionFactory);
    }
}
```

#### User.java

```java
package com.hozuo.testMybatisDatasource.pojo;

import java.io.Serializable;
import java.util.Objects;

/**
 * User
 * <p>2020/6/23
 * <p>测试实体，模拟一个用户
 * @author hozuo
 * @version 1.0
 */
public class User implements Serializable {
    private Integer id;
    private String username;
    private String password;

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

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        User user = (User) o;
        return Objects.equals(id, user.id) &&
                Objects.equals(username, user.username) &&
                Objects.equals(password, user.password);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, username, password);
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

#### testD1Mapper.xml

命名空间要对应

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hozuo.testMybatisDatasource.mapper.testD1.TestD1Mapper">

    <select id="select" resultType="com.hozuo.testMybatisDatasource.pojo.User">
        SELECT * FROM user
    </select>
</mapper>
```

#### testD2Mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hozuo.testMybatisDatasource.mapper.testD2.TestD2Mapper">

    <select id="select" resultType="com.hozuo.testMybatisDatasource.pojo.User">
        SELECT * FROM user
    </select>
</mapper>
```

#### TestD1Mapper.java

```java
package com.hozuo.testMybatisDatasource.mapper.testD1;

import com.hozuo.testMybatisDatasource.pojo.User;
import org.apache.ibatis.annotations.Mapper;

import java.util.List;

@Mapper
public interface TestD1Mapper {
    List<User> select ();
}
```

#### TestD2Mapper.java

```java
package com.hozuo.testMybatisDatasource.mapper.testD2;

import com.hozuo.testMybatisDatasource.pojo.User;
import org.apache.ibatis.annotations.Mapper;

import java.util.List;

@Mapper
public interface TestD2Mapper {
    List<User> select ();
}
```

#### TestD1Service.java

```java
package com.hozuo.testMybatisDatasource.service;

import com.hozuo.testMybatisDatasource.mapper.testD1.TestD1Mapper;
import com.hozuo.testMybatisDatasource.pojo.User;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.List;

/**
 * TestD1Service
 * <p>2020/6/23
 * @author hozuo
 * @version 1.0
 */
public interface TestD1Service {
    List<User> select ();
}
```

#### TestD2Service.java

```java
package com.hozuo.testMybatisDatasource.service;

import com.hozuo.testMybatisDatasource.pojo.User;

import java.util.List;

/**
 * TestD2Service
 * <p>2020/6/23
 * @author hozuo
 * @version 1.0
 */
public interface TestD2Service {
    List<User> select ();
}
```

#### TestD1ServiceImpl.java

```java
package com.hozuo.testMybatisDatasource.service.impl;

import com.hozuo.testMybatisDatasource.mapper.testD1.TestD1Mapper;
import com.hozuo.testMybatisDatasource.pojo.User;
import com.hozuo.testMybatisDatasource.service.TestD1Service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * TestD1ServiceImpl
 * 2020/6/23
 *
 * @author hozuo
 * @version 1.0
 */
@Service
public class TestD1ServiceImpl implements TestD1Service {

    private TestD1Mapper testD1Mapper;

    @Autowired
    public void setTestD1Mapper(TestD1Mapper testD1Mapper) {
        this.testD1Mapper = testD1Mapper;
    }

    @Override
    public List<User> select() {
        return testD1Mapper.select();
    }
}
```

TestD2ServiceImpl.java

```java
package com.hozuo.testMybatisDatasource.service.impl;

import com.hozuo.testMybatisDatasource.mapper.testD2.TestD2Mapper;
import com.hozuo.testMybatisDatasource.pojo.User;
import com.hozuo.testMybatisDatasource.service.TestD2Service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * TestD1ServiceImpl
 * 2020/6/23
 *
 * @author hozuo
 * @version 1.0
 */
@Service
public class TestD2ServiceImpl implements TestD2Service {

    private TestD2Mapper testD2Mapper;

    @Autowired
    public void setTestD2Mapper(TestD2Mapper testD2Mapper) {
        this.testD2Mapper = testD2Mapper;
    }

    @Override
    public List<User> select() {
        return testD2Mapper.select();
    }
}
```

#### TestD2ServiceImpl.java

```java
package com.hozuo.testMybatisDatasource.service.impl;

import com.hozuo.testMybatisDatasource.mapper.testD2.TestD2Mapper;
import com.hozuo.testMybatisDatasource.pojo.User;
import com.hozuo.testMybatisDatasource.service.TestD2Service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * TestD2ServiceImpl
 * 2020/6/23
 *
 * @author hozuo
 * @version 1.0
 */
@Service
public class TestD2ServiceImpl implements TestD2Service {

    private TestD2Mapper testD2Mapper;

    @Autowired
    public void setTestD2Mapper(TestD2Mapper testD2Mapper) {
        this.testD2Mapper = testD2Mapper;
    }

    @Override
    public List<User> select() {
        return testD2Mapper.select();
    }
}
```

#### TestController.java

```java
package com.hozuo.testMybatisDatasource.controller;

import com.hozuo.testMybatisDatasource.pojo.User;
import com.hozuo.testMybatisDatasource.service.TestD1Service;
import com.hozuo.testMybatisDatasource.service.TestD2Service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * TestController
 * <p>2020/6/23
 * <p> 测试控制器
 * @author hozuo
 * @version 1.0
 */
@RestController
public class TestController {

    private TestD1Service testD1Service;

    private TestD2Service testD2Service;

    @Autowired
    public void setTestD1Service(TestD1Service testD1Service) {
        this.testD1Service = testD1Service;
    }

    @Autowired
    public void setTestD2Service(TestD2Service testD2Service) {
        this.testD2Service = testD2Service;
    }

    @RequestMapping("/d1")
    public List<User> testD1() {
        return testD1Service.select();
    }

    @RequestMapping("/d2")
    public List<User> testD2() {
        return testD2Service.select();
    }
}
```

#### Application.java 主启动类

```java
package com.hozuo.testMybatisDatasource;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;

/**
 * TestMybatisDatasourceApplication
 * <p>2020/6/23
 * <p>关闭mapper自动扫描
 * @author hozuo
 * @version 1.0
 */
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
public class TestMybatisDatasourceApplication {

   public static void main(String[] args) {
      SpringApplication.run(TestMybatisDatasourceApplication.class, args);
   }

}
```



#### 数据库

![image-20200623141345479](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200623141345479.png)

<img src="C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200623141308343.png" alt="image-20200623141308343" style="zoom: 67%;" />

测试结果

![image-20200623141437048](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200623141437048.png)

![image-20200623141453224](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200623141453224.png)

源码地址

https://github.com/hozuo/TestMybatisDatasource

参考资料

https://blog.csdn.net/tuesdayma/article/details/81081666

https://www.hangge.com/blog/cache/detail_2539.html



## MyBatis获取刚插入的主键

```sql
<insert id="insertUser" parameterType="com.mybatis.model.User">
		<selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
			SELECT LAST_INSERT_ID()
		</selectKey>
		insert into user (username,password,gender,email)
		values (#{username},#{password},#{gender},#{email})
</insert>
```

```sql
<insert id="insertUser" parameterType="com.mybatis.model.User" useGeneratedKeys="true" keyProperty="id">
		insert into user (username,password,gender,email)
		values (#{username},#{password},#{gender},#{email})
</insert>
```

在pojo返回值使用get方法获取主键

## Springboot开启事务

启动类添加注解

```java
@EnableTransactionManagement
```

开启时添加注解

```
@Transactional
```

