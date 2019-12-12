# SpringCloud配置文件

## Eureka

```yaml
# 注册到的主机
eureka.instance.hostname: xxx

# 确保其他主机得到真实的ip地址而不是主机名称, 默认为 false
eureka.instance.prefer-ip-address: true

# eureka 界面显示的名称
eureka.instance.instance-id: xxxxx

# 是否将自己注册到 eureka， 默认为 true
eureka.client.register-with-eureka: true

# 是否缓存拉取的服务列表，因此eureka服务可以不缓存，我觉得更能确保eureka之间的一致, 默认为 true
eureka.client.fetch-registry: true

# 注册到的地址, hash 类型，缺省为本机，缺省端口为 8761
eureka.client.service-url:
	defaultZone: xxxxx
	
# eureka client 拉取服务列表更新缓存的周期, 默认为 30（秒）     -----------客户端
eureka.client.registry-fetch-interval-seconds: 10

# 服务提供者向 eureka 发送心跳的周期， 默认为 30（秒） 		   ------------客户端
eureka.instance.lease-renewal-interval-in-seconds: 10       

# eureka 认为 其宕机的时间， 默认为 90（秒）                   ------------客户端
eureka.instance.lease-expiration-duration-in-seconds: 30

# eureka 定期剔除宕掉的服务器的时间, 默认为 60（秒）
eureka.server.eviction-interval-timer-in-ms: 30

# 关闭自我保护机制（不推荐），默认为打开状态（true）
eureka.server.enable-self-preservation: false

# euraka 的期望的心跳比例计算系数（降低自我保护机制使用，可以为0.5以下），默认为 0.85
eureka.server.renewal-percent-threshould: 0.49
```

## Ribbon

```yaml
# 负载均衡的方式（轮询、ip_Hash、url_hash、随机访问）
xxx(eureka服务id)：
	ribbon:
		NFloadBalanceRuleClassName: com.netflix.loadbalancer.RandomRule(随机访问)
```

## Retry

```yaml
# 默认为 true （即开启重试）
spring.cloud.loadbalancer.retry.enable: false

# Ribbon 连接超时时间(秒)
xxx(服务实例id).ribbon.ConnectTimeOut：250

# Ribbon 数据读取超时时间(秒)
xxx(服务实例id).ribbon.ReadTimeOut: 1000

# 是否对所有操作都进行重试
xxx(服务实例id).ribbon.OkToRetryOnAllOperations: true

# 切换实例的重复次数（即已访问几个相同实例的服务器）
xxx(服务实例id).ribbon.MaxAutoRetriesNextServer: 1

# 对当前实例的重复次数
xxx(服务实例id).ribbon.MaxAutoRetries: 1
```



