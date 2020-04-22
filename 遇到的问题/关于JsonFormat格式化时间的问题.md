## 关于JsonFormat格式化时间的问题

在`model`中使用`@JsonFormat`注解时，发现格式化出来的时间是不对的。

原因就是时区的问题，`@JsonFormat`默认的时区是`Greenwich Time`， 默认的是格林威治时间 ，把时区改了就好了，改成咱们中国对应的时区`GTM+8`。

```java
@JsonFormat(pattern = "yyyy年MM月dd日 HH:mm:ss", timezone = "GMT+8")
private Date noticeDate;
```

