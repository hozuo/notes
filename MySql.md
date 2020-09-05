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

## 连接数据库的docker命令行

```shell
docker run -it --network dockernet --rm mariadb mysql -hsome-mariadb -uexample-user -p
```

使用指令开启一个命令行连接数据库

dockernet指连接的网络

mariadb mysql固定不变

some-mariadb指要连接的原始数据库docker名

example-user用户名

-p后面填密码

## 启动mariadb容器



```shell
docker run --name xdw-database-mariadb --network dockernet -p 3306:3306 -v "/usr/local/src/mysql/datadir:/var/lib/mysql" -v "/usr/local/src/mysql/backup:/usr/local/src/mysql/backup" -e MYSQL_ROOT_PASSWORD=xdwDatabaseRootAdminHozuoPassword980305 -e MYSQL_DATABASE=xdw-manager -d mariadb --character-set-server=utf8mb4
```



```shell
docker exec -i xdw-database-mariadb sh -c 'exec mysql -uroot -pxdwDatabaseRootAdminHozuoPassword980305' < /usr/local/src/xdwManagerDatabaseBackup20200715.sql
```

## 数据库备份

```
mysqldump -h 远程服务器IP或域名 -u 远端数据库账号 -p'远端数据库密码' --default-character-set=utf8 数据库名字 > /tmp/database_db.sql
```

```
mysqldump -h 172.19.0.11 -u root -p'root' --default-character-set=utf8 xdwManager > /usr/local/src/database_db.sql
```

```
mysqldump -h 10.0.1.225 -u root -p'SfSPN8ujTG97ctCQtF0GTyIeMil4oHbeANviZVU2SXLnsCWsT5DHcBxjRyH6Isk' xdwManager > /usr/local/src/backup/xdwBak_guest_20200718_092707.sql
```

ip填服务的虚拟ip（10.0.1.218），或者容器的虚拟IP（10.0.1.225），容器ip和my-multihost-network同一网段，不能填ingress网段的ip

容器IP会改变，服务IP不会

```
mysqldump -h 10.0.0.109 -u root -p'SfSPN8ujTG97ctCQtF0GTyIeMil4oHbeANviZVU2SXLnsCWsT5DHcBxjRyH6Isk' xdwManager > /usr/local/src/backup/xdwBak_guest_20200718_092707.sql
```



创建备份脚本（放在/usr/local/src/mysql/cmd/backup.sh）

$1:数据库ip

$2:操作用户，自动填auto

$3:创建时间，手动由java服务器提供，自动由当前操作系统提供

```shell
#!/bin/bash

DATE=$(date "+%Y%m%d_%H%M%S")

mysqldump -h $1 -u root -p'SfSPN8ujTG97ctCQtF0GTyIeMil4oHbeANviZVU2SXLnsCWsT5DHcBxjRyH6Isk' xdwManager > /usr/local/src/mysql/backup/xdwBak_$2_$DATE.sql
```

执行脚本

```shell
sh /usr/local/src/cmd 10.0.1.218 admin 
```

## 启动mariadb服务

```shell
docker service create -p 3306:3306 --mount source=xdw-mysql-data,target=/var/lib/mysql --mount source=xdw-mysql-backup,target=/usr/local/src/mysql --replicas 1 --name mysql-service --network my-multihost-network -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/mysql-password --secret mysql-password mariadb
```

## 还原数据

```shell
docker exec -i 283 sh -c 'exec mysql -uroot -p"$mysql-password"' < /usr/local/src/mysql/backup
/xdwManagerBackup2020-07-16_16_51_32.sql
```

**远程还原**

restore.sh(/usr/local/src/mysql/cmd/restore.sh)

```shell
mysql -h 10.0.1.218 -uroot -pSfSPN8ujTG97ctCQtF0GTyIeMil4oHbeANviZVU2SXLnsCWsT5DHcBxjRyH6Isk -DxdwManager< /usr/local/src/mysql/backup/xdwBak_shell_20200718_154733.sql
```

```
mysql -h $1 -uroot -pSfSPN8ujTG97ctCQtF0GTyIeMil4oHbeANviZVU2SXLnsCWsT5DHcBxjRyH6Isk -DxdwManager< /usr/local/src/mysql/backup/$2
```

## 删除数据库中重复数据

```sql
#删除cdk_code相同的数据，仅保留create_time最新的一条
DELETE FROM `xdw_cdk` WHERE cdk_id NOT IN (
    SELECT cdk_id FROM `xdw_cdk` WHERE create_time IN (
        SELECT MAX(create_time) FROM `xdw_cdk` GROUP BY cdk_code));
        
#如果数据的cdk_cdoe和create_time相等将无法删除干净，删除这些重复的数据并保留id最大的一条
DELETE FROM `xdw_cdk` WHERE cdk_id NOT IN (
    SELECT cdk_id FROM `xdw_cdk` WHERE cdk_id IN (
        SELECT MAX(cdk_id) FROM `xdw_cdk` GROUP BY cdk_code));
        

```

## 使用unix时间戳入库

```sql
from_unixtime(Unix timestamp)
```

