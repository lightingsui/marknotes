## Centos7忘记mysql的root密码解决方案

由于走上了程序猿的这条路，导致记忆力急剧下降，随时有失忆的风险，这不，今天打算使用装在Centos7上的mysql数据库，连接的时候竟然把密码给忘了，因此出现了此篇文章。。。

首先，编辑`/etc/my.cnf`文件。

```bash
vim /etc/my.cnf
```

在此处添加`skip-grant-tables` 

![1574862602203](C:\Users\LIGHTI~1\AppData\Local\Temp\1574862602203.png)

重新启动`mysql`

```bash
systemctl restart mysqld
```

这时就不需要密码就能登录到`mysql`了(仅限主机)，输入mysql进行登录

```bash
mysql
```

登录到mysql之后就是修改mysql库下的user表，修改root的密码

```bash
use mysql;
update user set authentication_string=password('新密码') where user='root';
```

此时就已经修改成功了，恢复刚才修改的`/etc/my.cnf`文件

![1574862888375](C:\Users\LIGHTI~1\AppData\Local\Temp\1574862888375.png)

重新启动`mysql`，即可使用新密码进行登录

```bash
systemctl restart mysql
```

----------------------------------------END-------------------------------------------------------

