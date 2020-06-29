## docker安装

**删除原安装**

```shell
sudo yum remove docker-ce \
                docker-ce-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine
```

**安装一组工具**

```shell
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

**设置 yum 仓库地址**

```shell
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
sudo yum-config-manager \
     --add-repo \
     http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

**更新 yum 缓存**

```shell
sudo yum makecache fast
```

**安装新版 docker**

```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```

### 测试

**启动 docker**

```shell
sudo systemctl start docker
```

**运行 hello-world 镜像，验证 docker**

```shell
sudo docker run hello-world
```

**设置 docker 开机启动**

```shell
sudo systemctl enable docker
```

## 镜像加速

由于国内网络问题，需要配置加速器来加速。修改配置文件 `/etc/docker/daemon.json`

下面命令直接生成文件 daemon.json

```shell
cat <<EOF > /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://hub-mirror.c.163.com"
  ]
}
EOF
```

**重新启动服务。**

```shell
# 重新加载docker配置
sudo systemctl daemon-reload

#重启docker服务
sudo systemctl restart docker
```

**查看镜像配置**

```shell
docker info
```

## 安装CentOS7镜像

**下载 CentOS 镜像**

```shell
docker pull centos:7
```

**查看centos7镜像**

```shell
docker images
```

![image-20200616105511212](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200616105511212.png)

**运行 centos7**

```
docker run -it xxxx bash
或者
docker run -it centos:7
```

- xxxx：镜像名, 或 image id 的前几位
- -i：交互式操作
- -t：终端操作，进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
- -d：后台运行容器
- bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash。

**删除镜像**

```shell
docker image rm
```

**查看所有容器**

```shell
docker ps -a
```

**清理所有终止状态容器**

```shell
docker container prune
```

![image-20200616111720141](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200616111720141.png)

## 数据卷

### 创建数据卷

```shell
docker volume create my-vol
```

### 查看所有数据卷

```shell
docker volume ls
```

![image-20200616111817079](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200616111817079.png)

### 查看指定 数据卷 的信息

```shell
docker volume inspect my-vol
```

![image-20200616111927959](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200616111927959.png)

### 启动挂载数据卷的容器

```shell
docker run -it --mount source=my-vol,target=/webapp centos:7 bash

# 或者：

docker run -it -v my-vol:/webapp centos:7 bash
```

## 数据映射

### 创建包含数据映射的容器

```shell
docker run --name testGZ -v /opt/data -t -i docker.io/ubuntu /bin/bash
```

### 查看数据映射路径

```
docker inspect testGZ|grep /var/lib/docker/volumes
```

```
"Source": "/var/lib/docker/volumes/60f48cf8c6257f738c7bff9e326986dd627e66d24c21237aeb2f83ca45597974/_data"
```

## 虚拟网络

### 创建虚拟网络

```shell
docker network create --subnet=172.19.0.0/24 dockernet
```

## 数据库备份

格式：mysqldump -h主机名 -P端口 -u用户名 -p密码 --database 数据库名 > 文件名.sql

```
mysqldump -h 172.19.0.11 -P3306 -uroot -proot --database TestMySqlBackup > /var/lib/mysql/testbk.sql
```

> 1、备份命令
>
> 格式：mysqldump -h主机名 -P端口 -u用户名 -p密码 --database 数据库名 > 文件名.sql
>
> 例如： mysqldump -h 192.168.1.100 -p 3306 -uroot -ppassword --database cmdb > /data/backup/cmdb.sql
>
> 2、备份压缩
>
> 导出的数据有可能比较大，不好备份到远程，这时候就需要进行压缩
>
> 格式：mysqldump -h主机名 -P端口 -u用户名 -p密码 --database 数据库名 | gzip > 文件名.sql.gz
>
> 例如： mysqldump -h192.168.1.100 -p 3306 -uroot -ppassword --database cmdb | gzip > /data/backup/cmdb.sql.gz
>
> 3、备份同个库多个表
>
> 格式：mysqldump -h主机名 -P端口 -u用户名 -p密码 --database 数据库名 表1 表2 .... > 文件名.sql
>
> 例如 mysqldump -h192.168.1.100 -p3306 -uroot -ppassword cmdb t1 t2 > /data/backup/cmdb_t1_t2.sql
>
> 4、同时备份多个库
>
> 格式：mysqldump -h主机名 -P端口 -u用户名 -p密码 --databases 数据库名1 数据库名2 数据库名3 > 文件名.sql
>
> 例如：mysqldump -h192.168.1.100 -uroot -ppassword --databases cmdb bbs blog > /data/backup/mutil_db.sql
>
> 5、备份实例上所有的数据库
>
> 格式：mysqldump -h主机名 -P端口 -u用户名 -p密码 --all-databases > 文件名.sql
>
> 例如：mysqldump -h192.168.1.100 -p3306 -uroot -ppassword --all-databases > /data/backup/all_db.sql
>
> 6、备份数据出带删除数据库或者表的sql备份
>
> 格式：mysqldump -h主机名 -P端口 -u用户名 -p密码 --add-drop-table --add-drop-database 数据库名 > 文件名.sql
>
> 例如：myqldump -uroot -ppassword --add-drop-table --add-drop-database cmdb > /data/backup/all_db.sql
>
> 7、备份数据库结构，不备份数据
>
> 格式：mysqldump -h主机名 -P端口 -u用户名 -p密码 --no-data 数据库名1 数据库名2 数据库名3 > 文件名.sql
>
> 例如：mysqldump --no-data –databases db1 db2 cmdb > /data/backup/structure.sql