## springboot配置数据库遇到的问题

**启动报错**：

```java
Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource
could be configured.
```

**问题分析**：

原因是我在maven中导入了oracle的jdbc依赖，但是并没有在application.yml中配置datasource，因此就会报这个错误。

**解决办法**：

在application.yml中配置datasource关于jdbc的属性即可解决问题