- 每次scheduler执行job时都会创建新实例，job类必须有无参构造，并且不能包含状态（不能给成员变量赋值试图在两次job执行中保存）

- job中增加set方法可以在穿件时自动注入值

- job拿到的map是JobDetail中的JobDataMap和Trigger中的JobDataMap的并集，但是如果存在相同的数据，则后者会覆盖前者的值。

- 设置时间的方法

  ```
  trigger = newTrigger()
  .withIdentity("trigger3", "group1")
  .startAt(myTimeToStartFiring)
  .withSchedule(simpleSchedule()
  	.withIntervalInSeconds(10)
  	.withRepeatCount(10)) // note that 10 repeats will give a total of 11 firings
  .forJob(myJob) // identify job with handle to its JobDetail itself
  .build();
  ```

  

