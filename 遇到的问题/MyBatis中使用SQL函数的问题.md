## MyBatis中使用SQL函数的问题

**问题描述**

在MyBatis中xml中，拼接SQL函数会报错

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-18_23-11-54.png)

例如使用DATE_ADD函数。

**解决办法**

```xml
<![CDATA[`in_time` <= DATE_ADD(#{inTime},INTERVAL 1 DAY) AND in_time >= #{inTime}]]>
```

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-18_23-13-40.png)

