# Oracle遇到的问题

##  解决windows7无法连接CentOS7系统中oracle问题：ORA-12514 TNS 监听程序当前无法识别

1. 关闭掉监听（lsnrctl stop）
2. 开启监听 （lsnrctl start）
3. 登入到数据库
4. 在登入的数据库界面关闭数据库（shutdown immediate）
5. 重新开启数据库（startup）

+ [解决的博客](https://www.cnblogs.com/nichoc/p/6417505.html )