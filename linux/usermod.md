## usermod

命令格式：usermod [options] username

|  ID  | 参数 |          说明          |                       格式                        |                  举例                  |
| :--: | :--: | :--------------------: | :-----------------------------------------------: | :------------------------------------: |
|  1   |  -u  |   为用户指定新的uid    |              usermod -u uid username              |       usermod -u 1001 test_user        |
|  2   |  -g  |   为用户指定新用户组   |              usermod -g gid username              |            username -g 1002            |
|  3   |  -G  | 为用户添加(覆盖)附加组 | usermod -G GROUP1[,GROUP2,...[,GROUPN]]] username |  usermod -G mygrp,test_group testuser  |
|  4   |  -s  |  为用户指定新的shell   |                usermod -s username                |      usermod -s /bin/sh testuser       |
|  5   |  -c  |     更改用户的注释     |               usermod -c 'commnet'                | usermod -c '这是新的用户注释' testuser |
|  6   |  -d  |    更改用户的家目录    |           usermod -d HOME_PATH username           |   usermod -d /home/new_home testuser   |
|  7   |  -l  |       更改用户名       |           usermod -l  new_name username           |     usermod -l test_user testuser      |
|  8   |  -L  |        锁定用户        |                usermod -L username                |          usermod -L testuser           |
|  9   |  -U  |       j解锁用户        |                usermod -U username                |          username -U testuser          |
|  10  |  -e  |   修改密码的到期时间   |          usermod -e YYYY-MM-DD username           |     usermod -e 2019-12-12 testuser     |
|  11  |  -f  |  修改密码的非活动期限  |             usermod -f days username              |         usermod -f 10 testuser         |

**说明**：

1. 指明新的uid，修改uid

2. -g后可以写组名，也可以写组id

3. -G后可接组名或者组id，为用户指定信息附加组，但是，这是覆盖指定，当使用此命令后，用户原来的附加组将会被覆盖掉，若想保留用户原有的附加组，可以使用-a选项

4. 为用户指定新的shell，linux所有的用户shell放在/etc/shells里，可以对指定的用户的shell进行更改，所有的shell如下

   ```sh
   /bin/sh
   /bin/bash
   /sbin/nologin
   /usr/bin/sh
   /usr/bin/bash
   /usr/sbin/nologin
   ```

5. 更改用户的comment，这个没什么可说的。

6. 更改用户的家目录，当我们只是用这是命令时候，是`无法把指定用户原有的家目录的东西带到新的家目录中`的，例如下面的这个操作，当我们给test这个用户更改家目录之后，然后切换到test这个用户，就显示找不到家目录了，那就更别谈查看家目录的内容了，但是，我们可以使用`-m`选项，标识将`用户原有`的家目录的内容带`到新的家目录中`。

![1573821959535](C:\Users\LIGHTI~1\AppData\Local\Temp\1573821959535.png)

7. 更改用户名，后面接上新名字

8. 锁定用户，其实锁定用户就是在给用户的`shadow密码文件中的密码字段前加上一个叹号`，例如我们使用这个命令，锁定之后就是`无论输入什么密码都不能登录`，但是`root可以`切换过去

   ```sh
   usermod -L test
   
   test:!$6$3qAGuWYN$k8pugDYlj1wMUUB7rYIk0HF8IOzWHLLnXjNPEkqjZqAib3f11c/ExbRo.hCkt4E2qQ4ECTz13UqXMXNQigsk3/:18215:0:99999:7:::
   ```

9. 解锁用户，就是将锁定时候密码前面加的`叹号去掉`。

10. 修改密码`到期`的日期，对应着/etc/shadow中的`倒数第二个`字段，输入的标准日期将会被`转化为unix纪元日期`

11. 修改用户密码`到期后容忍用户登录`的时间，对应着/etc/shadow中的`倒数第三个`字段，和`passwd -i`命令是一样的