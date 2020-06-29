## 查询当前主键自增值

```sql
SELECT auto_increment  FROM information_schema.tables where table_schema = "dbName" and table_name = "tableName";
```

dbName:数据库名

tableName：表名

## 超时设置

一段时间没有操作后，连接池中的连接会被释放，应用仍认为其可用，导致业务失败，mysql可以设置最大连接保持时间

查看超时设置

```sql
SHOW GLOBAL VARIABLES LIKE '%timeout%'
```

设置超时时间（单位ms）

```sql
SET GLOBAL wait_timeout = 28800
```



hikari可以设置max-lifetime一定时间后清除数据库连接，单位ms，不能小于30s（30000）否则重置为30分钟（默认是1800000）

```yml
hikari:
    minimum-idle: 3
    maximum-pool-size: 10
    max-lifetime: 30000
    connection-test-query: SELECT 1
```

## java.lang.IllegalStateException: dbType not support : null, url null

https://blog.csdn.net/LT_Future/article/details/103002553