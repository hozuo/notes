### 自定义属性

application.yml里配置

```yml
#********************************自定义属性********************************
prop:
  # 数据库名称
  dataBaseName: xdwManager
  # 序列号表名称
  snTableName: xdw_sn
```

使用@Value(${})读取值

```java
// @ConfigurationProperties("")
@Value("${prop.snTableName}")
private String snTableName;

@Value("${prop.dataBaseName}")
private String dataBaseName;
```

### @ConfigurationProperties驼峰规则禁止

springboot启动时报如下错误

```hxml
***************************
APPLICATION FAILED TO START
***************************

Description:

Configuration property name 'spring.datasource.xdwManager' is not valid:

    Invalid characters: 'M'
    Bean: hardwareController
    Reason: Canonical names should be kebab-case ('-' separated), lowercase alpha-numeric characters and must start with a letter

Action:

Modify 'spring.datasource.xdwManager' so that it conforms to the canonical names requirements.
```

由于以下代码引起

```java
@ConfigurationProperties(prefix = "spring.datasource.xdwManager")
public DataSource getDateSourceXdw() {
    return DataSourceBuilder.create().build();
}
```

修改为短线连接，可解决

```java
@ConfigurationProperties(prefix = "spring.datasource.xdw-manager")
public DataSource getDateSourceXdw() {
    return DataSourceBuilder.create().build();
}
```

此前缀需要和application.yml中定义值相等，例如以下

```yml
spring:
  application:
    name: xdwManage
  datasource:
    xdw-manager:
      # 引入druid数据源
      jdbc-url: jdbc:mysql://47.94.232.155:3306/xdwManager?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8&autoReconnect=true&allowMultiQueries=true
      driver-class-name: com.mysql.cj.jdbc.Driver
      username: root
      password: root
```

不过也支持前缀使用短线，yml使用驼峰，可以自动映射，例如以下

```yml
spring:
  application:
    name: xdwManage
  datasource:
    xdwManager:
      # 引入druid数据源
      jdbc-url: jdbc:mysql://47.94.232.155:3306/xdwManager?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8&autoReconnect=true&allowMultiQueries=true
      driver-class-name: com.mysql.cj.jdbc.Driver
      username: root
      password: root
```

但如果对应不上，启动时不会报错，使用该数据源时会提示url缺失

```html
### Error querying database.  Cause: java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.
### The error may exist in file [C:\Users\ghzfr\IdeaProjects\xdw\target\classes\mapper\xdw\ProdMapper.xml]
### The error may involve com.hozuo.xdw.mapper.xdw.ProdMapper.findPage
### The error occurred while executing a query
### Cause: java.lang.IllegalArgumentException: dataSource or dataSourceClassName or jdbcUrl is required.] with root cause

```

