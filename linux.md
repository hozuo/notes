## 常用命令

echo 'Hello World!'>a.txt

cat b.txt>a.txt

查询后删除

```shell
find . -name '*log*' | xargs rm -f
```

### 查看oom日志

```
egrep -i -r 'killed process' /var/log
```

