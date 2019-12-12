## 远程无法连接redis的问题

**问题描述**：

远程无法连接centos中的redis，错误类型（can't connect to redis-server）

**分析思路**：

centos上的redis限制了远程连接，只能进行本地连接

**解决方案**：

修改redis的配置文件，以我的centos为例，我将redis装在了`/usr/local`下，进入到此目录，然后进入到redis的目录，可以看到下面有一个配置文件叫做`redis.conf`，就是修改此配置文件

![TIM截图20191129195803](D:\typora文件\assets\TIM截图20191129195803.png)

使用vim命令打开，然后输入/ bind，进行寻找

![TIM截图20191129195937](D:\typora文件\assets\TIM截图20191129195937.png)

将此处注释掉或者删除掉就好了。

然后使用`./redis.server redis.conf`指定配置文件进行启动，然后就可以进行远程连接了。