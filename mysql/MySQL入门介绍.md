> 本文章参考掘金小册，<https://juejin.im/book/5bffcbc9f265da614b11b731> 

## MySQL的 C/S 架构

说起C/S架构模型，程序猿们应该最熟悉不过了，常见的模型有B/S模型，C/S模型。

C/S架构解释Client/Server模型，即客户端-服务器模型。

我们的mysql就分为客户端和服务器，当我们需要使用mysql时。需要经历以下步骤

1. 开启mysql服务器
2. 开启mysql客户端
3. 客户端输入账号密码，发送到服务器进行身份校验
4. 校验通过后，客户端可以发送命令给服务器，服务器进行解析，将结果返回给客户端
5. 断开连接

> 常见的mysql客户端有命令行（就是黑框框）、IDE工具（sqlyoung、navicat等）

一台物理主机当然也可以同时启动多个mysql服务，每一个服务在操作系统中对应着一个进程（Process），而每一个进程都有一个唯一的ID与之对应，称之为PID（操作系统会保证所有的进程ID是不重复的），PID是进程启动时操作操作系统随机分配的，当进程生命周期结束时，操作系统会自动回收此ID，以便分配给其它进程。每个进程还有一个进程名称，这个名称不是操作系统分配的，这是由软件开发者定义的。例如，mysql服务器的默认进程名称为mysqld，mysql客户端的默认进程名称为mysql

> 每个mysql服务又称之为一个数据库的实例。

## MySQL的安装

mysql的安装可以参考我的另一篇博客<https://juejin.im/post/5e3597875188252c322535de> 

## 启动MySQL服务器程序

这次就要在类Unix系统和windows系统两种启动方式进行总结了。

**类Unix启动**

在类unix系统中，bin文件夹下共有以下几个启动文件。

1. mysqld。这是个不常用的命令

2. mysqld_safe。一个脚本程序，在执行这个脚本后，会间接调用mysqld，而且还会在后台启动一个监控进程，如果mysqld挂掉了，会尝试唤醒mysqld。而且，执行这个脚本还会将mysqld运行产生的错误或诊断信息重定向到指定文件，方便分析错误。

3. mysql.server。会间接调用mysqld_safe，注意，这不是一个真正的可执行文件，这是一个链接文件，实际指向的是../support-files/mysql.server，如果在bin下没有发现mysql.server，可以自己创建一个链接文件。

   ```bash
   # mysql.server 启动mysql服务器
   mysql.server start
   # mysql.server 停止mysql服务器
   mysql.server stop
   ```

4. mysqld.multi。之前说过，一台物理主机上可以同时运行多个mysql服务器进程，而mysqld.multi就是对多个mysql服务器进程的启动或停止进行监控，了解即可。

**windows启动方式**

在windows中启动方式就比较简单了，一种是执行bin文件夹下的mysqld，再一种就是开启之前注册的MYSQL服务（服务注册方式可以参考<https://juejin.im/post/5e3597875188252c322535de> ）。

## 启动MySQL客户端程序

关于mysql的客户端，有mysql、mysqladmin、mysqlcheck等等等，这些都可以在bin目录下发现。只重点关注mysql客户端即可。

**命令格式**

```bash
mysql -h 主机IP -u 账号 -p密码 -P 服务器监听的端口号
```

各参数意义

|   参数名   | 意义                                                         |
| :--------: | ------------------------------------------------------------ |
|     -h     | mysql服务器的ip地址，如果是服务器是本机，可以写成localhost或127.0.0.1或者省略不写。--host |
|     -u     | 登录mysql的用户名。--user                                    |
|     -p     | 登录密码。--password                                         |
| -P（大写） | 指定服务器监听的端口号，不写则为3306。--port                 |

**断开mysql客户端的方式**

```
1. exit
2. quit
3. \q
```

> 注意：-p后边应该直接输入密码，-p和密码之间不能存在空格

在类Unix系统中，如果省略了用户名，mysql会使用当前登录系统的名称作为用户名。

在windows系统中，如果省略了用户名，则会使用默认用户名ODBC来登录

## 客户端与服务器连接的过程

运行着的客户端和服务器都是单独的进程，因为mysql客户端需要连接服务器，所以这就涉及到了进程间的通信，mysql支持以下三种进程通信。

**TCP/IP**

这是一种常见的网络通信方式，是一种面向连接的通信方式，与此类似的就是UDP，是一种无连接的不可靠的通信协议，大概的通讯过程：

mysql客户端和在操作系统中申请一个端口号，然后服务端在操作系统中也申请一个端口号，一般服务端默认的端口号为3306，当客户端和服务端不处于同一台主机上时，就需要目的ip + 端口号进行通信。

> 操作系统中端口号的范围为0 - 65535

**命名管道和共享内存**

此种进程间通讯方式仅限windows中，针对的是windows用户。如果你是windows用户，那么这两种进程通讯方式就并不适合你了。

要想使用命名管道，在启动MySQL服务端的时候，需要设置启动参数`--enable-named-pipe`。然后在客户端启动时候也要加上启动参数`--pipe`或`--protocol=pipe`

共享内存通信同样需要为服务端指定启动参数`--shared-memory`，为客户端指定`--protocol=memory`

> 共享内存仅适用于客户端与服务端处于同一台物理机的时候

**Unix套接字**

当我们的客户端与服务端同时处于同一台类Unix机器上时，就可以使用socket进行通信，只需要在启动客户端的时候指定启动参数`--protocol=socket`或者指定主机为localhost，服务端socket默认监听`/tmp/mysql.sock`文件，客户端默认也是连接这个监听文件的，如果想要更换监听文件，可以字在服务端使用启动参数`--socket=文件绝对路径`，客户端指定`--socket=绝对路径`

## 服务器处理客户端请求

每当有一个客户端连请求连接服务端时，服务端都会为此客户端分配一个线程，当有客户端断开连接时，此线程不会被回收，而是被缓存起来，下次有其它客户端请求连接时，会把此线程分给它，这样就减少了频繁申请和销毁线程带来的性能的影响了，当然，如果同时连接的客户端较多，那么分配出来的线程也就越多，服务器性能也会下降，所以要限制同时连入服务端的客户端的数量。

其它具体服务器处理客户端请求可以参考博客<https://blog.csdn.net/qq_40697120/article/details/103535061> 

## 常用存储引擎

存储引擎刚开始的时候叫做表处理器，为什么呢？我们平常所见的数据库结构都是二维表的结构，但是，物理存储上真正的数据结构是怎样的呢，这就是靠存储引擎去解析了。存储引擎将我们所见的二维表以特定的存储引擎所具有的特定物理存储结构进行双向解析，然后存储到物理介质中。

**下面列举了一些mysql中常见的存储引擎**

| 存储引擎名称 | 功能描述                             |
| ------------ | ------------------------------------ |
| ARCHIVE      | 用于数据存档（行被插入后不能再修改） |
| BLACKHOLE    | 丢弃写操作，读操作会返回空内容       |
| CSV          | 在存储数据时，以逗号分隔各个数据项   |
| FEDERATED    | 用来访问远程表                       |
| InnoDB       | 具备外键支持功能的事务存储引擎       |
| MEMORY       | 置于内存的表                         |
| MERGE        | 用来管理多个MyISAM表构成的表集合     |
| MyISAM       | 主要的非事务处理存储引擎             |
| NDB          | MySQL集群专用存储引擎                |

这么多存储引擎用哪个好的，其实我们常用的也就是用InnoDB、MyISAM。

**接下来是各个存储引擎对某些功能的支持情况**

| Feature                               | MyISAM | Memory  | InnoDB | Archive | NDB   |
| ------------------------------------- | ------ | ------- | ------ | ------- | ----- |
| B-tree indexes                        | yes    | yes     | yes    | no      | no    |
| Backup/point-in-time recovery         | yes    | yes     | yes    | yes     | yes   |
| Cluster database support              | no     | no      | no     | no      | yes   |
| Clustered indexes                     | no     | no      | yes    | no      | no    |
| Compressed data                       | yes    | no      | yes    | yes     | no    |
| Data caches                           | no     | N/A     | yes    | no      | yes   |
| Encrypted data                        | yes    | yes     | yes    | yes     | yes   |
| Foreign key support                   | no     | no      | yes    | no      | yes   |
| Full-text search indexes              | yes    | no      | yes    | no      | no    |
| Geospatial data type support          | yes    | no      | yes    | yes     | yes   |
| Geospatial indexing support           | yes    | no      | yes    | no      | no    |
| Hash indexes                          | no     | yes     | no     | no      | yes   |
| Index caches                          | yes    | N/A     | yes    | no      | yes   |
| Locking granularity                   | Table  | Table   | Row    | Row     | Row   |
| MVCC                                  | no     | no      | yes    | no      | no    |
| Query cache support                   | yes    | yes     | yes    | yes     | yes   |
| Replication support                   | yes    | Limited | yes    | yes     | yes   |
| Storage limits                        | 256TB  | RAM     | 64TB   | None    | 384EB |
| T-tree indexes                        | no     | no      | no     | no      | yes   |
| Transactions                          | no     | no      | yes    | no      | yes   |
| Update statistics for data dictionary | yes    | yes     | yes    | yes     | yes   |

这些特性肯定一下子是记不过来的，别着急，以后用到的时候再记。

## 关于存储引擎的一些操作

如果我们想查看mysql支持的存储引擎，可以使用

```mysql
show engines;
```

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-03_12-07-33.png)

其中，Engine代表存储引擎的名称，Support表示mysql是否支持此存储引擎，default表示当前mysql数据库默认的存储引擎，Comment表示对存储引擎的描述，Transactions表示表示该存储引擎是否支持事务。XA代表当前的存储引擎是否支持分布式事务。Savapoints表示是否支持事物的部分回滚。当然，如果连事务都不支持，那么就更不用提支持事务回滚了。

**创建表时指定存储引擎**

```mysql
create table temp_table
(
    id	int auto_increment,
    primary key(id)
) ENGINE = INNODB;
```

如创建表的时候没有指定存储引擎，那么就会使用默认的存储引擎作为此表的存储引擎。

**修改表的存储引擎**

```mysql
alter table temp_table ENGINE = MyISAM；
```

当修改完成后，我就可以使用一下语句查看当前表的存储于引擎了。

```mysql
show create table temp_table

# 得到的结果，此时存储引擎已经变为MyISAM
CREATE TABLE `temp_table` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8
```

> 不同的表可以使用不同的存储引擎！！！