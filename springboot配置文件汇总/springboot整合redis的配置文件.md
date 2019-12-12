## springboot整合redis的配置文件

**引入依赖**：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

**配置文件**:

```
spring:
  redis:
    #地址
    host: localhost
    #端口
    port: 6379
    #索引库
    database: 1
    #密码
    password: sadasdas
    #超时时间
    timeout: 5000ms
```

**config配置**：

```
@Configuration
public class BeanLoad {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory){
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(factory);

        //将key的序列化设置成StringRedisSerializer
        StringRedisSerializer keySerializer = new StringRedisSerializer();
        redisTemplate.setKeySerializer(keySerializer);
        redisTemplate.setHashKeySerializer(keySerializer);

        redisTemplate.afterPropertiesSet();
        return redisTemplate;
    }
}
```