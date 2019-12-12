## springcloud部署时候的遇到的问题

在网关处遇到的问题，启动网关时报错：

```java
Caused by: java.lang.ClassNotFoundException: com.netflix.hystrix.contrib.javanica.aop.aspectj.HystrixCommandAspect
```

![1574843118337](C:\Users\LIGHTI~1\AppData\Local\Temp\1574843118337.png)

解决办法：

ClassNotFound，就是因为项目中少了hystrix的相关jar包，因此在项目中引入hystrix依赖，如下

```
 <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

问题解决！