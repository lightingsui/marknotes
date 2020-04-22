## Mybatis主键回显

> 主键回显功能只适用于MySQL，因为只有MySQL才有主键自增这个概念

在使用mybatis时候，有这样一个需求，mysql的表中主键是自增的，但是使用Mybatis时，插入后想获得这个主键，即使用回显功能。

**首先在xml文件中进行配置**

```xml
<insert id="insertSelective" useGeneratedKeys="true" keyColumn="notice_id" keyProperty="noticeId"
        parameterType="com.xxlm.xxlmservice.pojo.Notice">
    
  sql语句 ...
    
</insert>
```

+ useGeneratedKeys为true表示启用主键回显功能，false为关闭回显功能。
+ keyColumn为数据库中对应的字段
+ keyProperty为实体类对应的字段

**service中使用**

```java
noticeMapper.insertSelective(notice);

System.out.println(notice);
```

待插入后，即可在实体类中看到回显完成的主键。