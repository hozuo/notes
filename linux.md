## 常用命令

echo 'Hello World!'>a.txt

cat b.txt>a.txt

查询后删除

```shell
find . -name '*log*' | xargs rm -f
```

### 查看oom日志

```shell
egrep -i -r 'killed process' /var/log
```

### 查看端口占用

```shell
netstat -tunlp
```

```shell
netstat -tunlp|grep 80
```

```shell
lsof -i
```

```
lsof -i:80
```

