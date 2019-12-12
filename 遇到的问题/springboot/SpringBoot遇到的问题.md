# SpringBoot遇到的问题

## 1、子工程进行打包之前，要先将父工程打包

​	这种会报包无法找到的错误，错误类型为 （Failed to collect dependencies）

## 2、子工程使用父工程中的spring-boot-starter-parent

​	由于子工程的parent已经被占用了，所以不能采用parent方法引用，所以应该在父工程中去掉parent标签内的内容。

![1570986696597](C:\Users\LIGHTI~1\AppData\Local\Temp\1570986696597.png)

然后在dependencyManagement标签内中，加入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.1.9.RELEASE</version>
    <type>pom</type>
    <scope>provided</scope>
</dependency>
```

这样在子工程中就可以使用springboot了