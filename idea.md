## 自动生成pojo

显示数据库菜单

<img src="C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200618090401051.png" alt="image-20200618090401051" style="zoom:70%;" />

添加数据库连接

<img src="C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200618090550567.png" alt="image-20200618090550567" style="zoom:70%;" />

添加数据源配置

<img src="C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200618090642961.png" alt="image-20200618090642961" style="zoom:80%;" />

生成pojo

<img src="C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200618091214328.png" alt="image-20200618091214328" style="zoom:80%;" />

修改包名，数据对应

<img src="C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200618091315431.png" alt="image-20200618091315431" style="zoom:80%;" />

![image-20200618091336833](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200618091336833.png)

## 导入maven模块项目

open项目根目录

右键pom文件Add as Maven Project

maven面板＋其他的pom文件

## Application context not configured for this file 

未为此文件配置应用程序上下文 一般出现于配置文件与主启动类不在同一包中的情况

解决办法：打开file-Project Structure-Modules-工程名-spring-“+”号 添加配置文件

![image-20200623093700912](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200623093700912.png)

## 导入eclipse格式规范

安装Eclipse Code Formatter

https://github.com/hozuo/files/blob/master/EclipseFormatter.zip

升级到最新版本（重要）

下载规范文件

https://github.com/hozuo/files/blob/master/eclipse-codestyle-forma.xml

导入

<img src="C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200624102911372.png" alt="image-20200624102911372" style="zoom:80%;" />

选择使用

![image-20200624102941245](C:\Users\ghzfr\AppData\Roaming\Typora\typora-user-images\image-20200624102941245.png)