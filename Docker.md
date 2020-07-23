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

### 删除无主数据卷

```
docker volume prune
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

## Docker命令

**进入容器**

```shell
docker exec -it mysqlBackup /bin/bash
```

**移除容器**

```shell
docker rm 容器id
```

**移除所有容器**

```
docker rm $(docker ps -a -q)
```

docker run -it --name tomcatTest -p 8080:8080 --privileged=true -v tomcat_data:/usr/local/tomcat/webapps tomcat /bin/bash

docker run -it --name tomcatInit -p 2000:8080 consol/tomcat-7.0 /bin/bash

**查看所有容器**

```shell
docker ps -a
```

**清理所有终止状态容器**

```shell
docker container prune
```

## jar设置时区

java -jar -Duser.timezone=GMT+08 /usr/local/src/xdw-0.0.1-SNAPSHOT.jar

## 上线Vue项目

在项目根目录下创建文件`default.conf`，写入如下内容

```
server {
    listen       80;
    server_name  localhost; # 修改为docker服务宿主机的ip

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html =404;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

该配置文件定义了首页的指向为 `/usr/share/nginx/html/index.html`, 所以我们可以一会把构建出来的index.html文件和相关的静态资源放到`/usr/share/nginx/html`目录下。

在项目根目录下创建dockerfile文件，写入下面内容

```
FROM nginx

MAINTAINER hozuo

RUN rm /etc/nginx/conf.d/default.conf

ADD default.conf /etc/nginx/conf.d/

COPY dist/ /usr/share/nginx/html/
```

说明

```
FROM nginx：该镜像是基于nginx:latest镜像构建的

MAINTAINER hozuo：添加说明

RUN rm /etc/nginx/conf.d/default.conf：删除目录下的default.conf文件

ADD default.conf /etc/nginx/conf.d/：将default.conf复制到/etc/nginx/conf.d/下，用本地的default.conf配置来替换nginx镜像里的默认配置

COPY dist/ /usr/share/nginx/html/：将项目根目录下dist文件夹（构建之后才会生成）下的所有文件复制到镜像/usr/share/nginx/html/目录下
```



```
FROM nginx

MAINTAINER hozuo

RUN rm /etc/nginx/conf.d/default.conf

ADD default.conf /etc/nginx/conf.d/


```



安装依赖

```
npm install
```

构建

```
npm run build
```

```
docker build -t xdw-nginx .
```

```
docker run -d -p 8080:80 --name xdw-web xdw-xeb-image-v0.0.3
```

## 设置swarm集群

swarm是一系列主机的集合，主机是集合中的一个节点(node)，任务(task)是最小的调度单位，一些任务的集合是服务(service).

节点分为管理 (manager) 节点和工作 (worker) 节点。

**初始化swarm**

```shell
docker swarm init
```

```bash
[root@ecs6 mysql]# docker swarm init
Swarm initialized: current node (blgiqwskjig8uw7ft5a72uogv) is now a manager.

To add a worker to this swarm, run the following command:

docker swarm join --token SWMTKN-1-0b7ft3vxf7d8utedtxjdba5f2bogcdbb0tk99m9jnde1ephy10-75tnitlkbxxd73w412provc75 172.17.203.109:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

**查看节点信息**

```shell
docker node ls
```

```bash
[root@ecs6 mysql]# docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
blgiqwskjig8uw7ft5a72uogv *   ecs6                Ready               Active              Leader              19.03.11

```

**查看swarm的当前状态**

```
docker info
```

```
...
Swarm: active
  NodeID: blgiqwskjig8uw7ft5a72uogv
  Is Manager: true
  ClusterID: 9t178mftc4ed5bram7cu4p1jz
  Managers: 1
  Nodes: 1
  Default Address Pool: 10.0.0.0/8  
  SubnetSize: 24
  Data Path Port: 4789
  Orchestration:
   Task History Retention Limit: 5
...
```

**创建服务**

```shell
[root@ecs6 mysql]# docker service create --replicas 3 -p 80:80 --name nginx nginx
qbxbhop7u34abege6f9wzg1pf
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
verify: Service converged 
[root@ecs6 mysql]# docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                    NAMES
f6fd1c803c79        nginx:latest           "/docker-entrypoint.…"   18 seconds ago      Up 15 seconds       80/tcp                   nginx.1.zp4v0h5ydnws6g8a6ulvuncap
ac15f3a470bc        nginx:latest           "/docker-entrypoint.…"   18 seconds ago      Up 15 seconds       80/tcp                   nginx.3.kgmgw90bwrej01rgphdg4sdkf
9c9eae820ac4        nginx:latest           "/docker-entrypoint.…"   18 seconds ago      Up 15 seconds       80/tcp                   nginx.2.39yl8yb19n4nvtixom4wx0zip
f4a09cbb68cf        mariadb                "docker-entrypoint.s…"   5 hours ago         Up 5 hours          0.0.0.0:3306->3306/tcp   xdw-database-mariadb
e8132a4db2cd        xdw-xeb-image-v0.0.4   "/docker-entrypoint.…"   29 hours ago        Up 29 hours         0.0.0.0:8080->80/tcp     xdw-web
84878f17cef5        java:8                 "bash"                   13 days ago         Up 8 days           0.0.0.0:80->2000/tcp     clever_heisenberg
```

**查看详情**

```
[root@ecs6 mysql]# docker service ps nginx
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
zp4v0h5ydnws        nginx.1             nginx:latest        ecs6                Running             Running about a minute ago                       
39yl8yb19n4n        nginx.2             nginx:latest        ecs6                Running             Running about a minute ago                       
kgmgw90bwrej        nginx.3             nginx:latest        ecs6                Running             Running about a minute ago   
```

**扩容**

```
[root@ecs6 mysql]# docker service scale nginx=5
nginx scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service converged 
```

## Dockerfile设置时区

```
#1.指定基础镜像，并且必须是第一条指令
FROM centos:7

#2.指明该镜像的作者和其电子邮件
LABEL maintainer=hozuo

#3.在构建镜像时，指定镜像的工作目录，之后的命令都是基于此工作目录，如果不存在，则会创建目录
WORKDIR /docker_workdir/

#修改yum源
RUN rm -rf /etc/yum.repos.d/*
ADD /etc/yum.repos.d /etc/yum.repos.d/
RUN yum clean all

#安装mysqldump
RUN yum -y install holland-mysqldump.noarch

#4.一个复制命令，把jdk安装文件复制到镜像中，语法：ADD <src>... <dest>,注意：jdk*.tar.gz使用的是相对路径
ADD jdk-8u221-linux-x64.tar.gz /docker_workdir/jdk/

#5.配置环境变量
ENV JAVA_HOME=/docker_workdir/jdk/jdk1.8.0_221
ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV PATH=$JAVA_HOME/bin:$PATH

VOLUME ["/usr/local/src/jar", "/usr/local/src/jar"]

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone

EXPOSE 8080

#容器启动时需要执行的命令
CMD ["java","-jar","/usr/local/src/jar/*.jar"]
```



数据库root用户密码（63位）：SfSPN8ujTG97ctCQtF0GTyIeMil4oHbeANviZVU2SXLnsCWsT5DHcBxjRyH6Isk



docker run -it -v xdw-java8-data:/usr/local/src/jar/ -p 2000:2000 --network dockernet xdw-java8

docker service create --mount source=xdw-java8-data,target=

## 上线Java项目

```shell
docker service create --mount source=xdw-java8-data,target=/usr/local/src/jar --mount source=xdw-mysql-backup,target=/usr/local/src/mysql/backup --mount source=xdw-java8-cmd,target=/usr/local/src/cmd --mount source=xdw-java8-logs,target=/springLogs --network my-multihost-network --replicas 1 -p 2000:2000 -d --name xdw-java8-service xdw-java8
```

## docker stack deploy

```shell
docker stack deploy --compose-file docker-compose.yml xdw-swamp
```

