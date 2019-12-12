#  Mysql遇到的问题

## 1、Mysql报错is not allowed to connect解决办法

出现此种错误是由于mysql账户没有允许远程进行连接导致的，只需要修改mysql库下的user表即可。

``` mysql
update user set host ='%' where user ='root';
```

即将root或者其它账户改成%，远程连接。而且还需要重启mysql服务



