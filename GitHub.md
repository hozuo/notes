## 远程仓库和本地仓库失去关联

在远程仓库和本地仓库分别修改后会出现无法提交的情况，同时会无法拉取，报如下错误

refusing to merge unrelated histories

解决办法是合并两个仓库，再次提交

```git
git pull [你的远程仓库的地址] [要拉取的分支名] --allow-unrelated-histories
```

例如：

```cmd
C:\Users\ghzfr\IdeaProjects\xdw>git pull https://github.com/hozuo/xdwManager master --allow-unrelated-histories
From https://github.com/hozuo/xdwManager
 * branch            master     -> FETCH_HEAD
Merge made by the 'recursive' strategy.
 README.md | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```



## idea报错 Repository not found. Authentication failed

idea上传代码时报错

```cmd
Invocation failed Server returned invalid Response. java.lang.RuntimeException: Invocation failed Server returned invalid Response. at org.jetbrains.git4idea.http.GitAskPassXmlRpcClient.askUsername(GitAskPassXmlRpcClient.java:50) at org.jetbrains.git4idea.http.GitAskPassApp.main(GitAskPassApp.java:64) Caused by: java.io.IOException: Server returned invalid Response. at org.apache.xmlrpc.LiteXmlRpcTransport.sendRequest(LiteXmlRpcTransport.java:242) at org.apache.xmlrpc.LiteXmlRpcTransport.sendXmlRpc(LiteXmlRpcTransport.java:90) at org.apache.xmlrpc.XmlRpcClientWorker.execute(XmlRpcClientWorker.java:72) at org.apache.xmlrpc.XmlRpcClient.execute(XmlRpcClient.java:194) at org.apache.xmlrpc.XmlRpcClient.execute(XmlRpcClient.java:185) at org.apache.xmlrpc.XmlRpcClient.execute(XmlRpcClient.java:178) at org.jetbrains.git4idea.http.GitAskPassXmlRpcClient.askUsername(GitAskPassXmlRpcClient.java:47) ... 1 more remote: Repository not found. Authentication failed for 'https://github.com/hozuo/xdwManager/'
```

win凭据管理器删掉github的凭据即可解决