equalsIgnoreCase 不分大小写比较

## 多线程

## 常用POJO对象

POJO 是 DO / DTO / BO / VO 的统称.

- DO(Data Object) :此对象与数据库表结构一一对应,通过 DAO 层向上传输数据源对象。
- DTO(Data Transfer Object) :数据传输对象, Service 或 Manager 向外传输的对象。
- BO(Business Object) :业务对象,由 Service 层输出的封装业务逻辑的对象。
- AO (Application Object) :应用对象,在 Web 层与 Service 层之间抽象的复用对象模型,极为贴近展示层,复用度不高。
- VO(View Object) :显示层对象,通常是 Web 向模板渲染引擎层传输的对象。

## 时间转换

一种ISO格式

```
yyyy-MM-dd'T'HH:mm:ss.SSSXXX
```



```
@DateTimeFormat(iso = DATE_TIME)
private Date timeLimit;
```