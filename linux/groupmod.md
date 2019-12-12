## groupmod

命令格式：groupmod [options] GROUP

| ID   | 参数 | 说明             | 格式                           | 举例                     |
| ---- | ---- | ---------------- | ------------------------------ | ------------------------ |
| 1    | -n   | 给组指定新的名字 | groupamod -n newname groupname | groupmod -n mygrp mygrp1 |
| 2    | -g   | 指定新的gid      | groupmod -g num groupname      | groupmod -g 1002 mygrp   |

## userdel

命令格式：userdel [options] username

说明：删除用户的指令，options不填则不会删除用户家目录

| ID   | 参数 | 说明                 | 格式                | 举例                |
| ---- | ---- | -------------------- | ------------------- | ------------------- |
| 1    | -r   | 删除用户并删除家目录 | userdel -r username | userdel -r testuser |

## groupdel

命令格式：groupdel [options] GROUP

说明：删除组的命令

## gpasswd

命令格式：gpasswd [options] GROUP

说明：和组密码相关的命令。用于为组指定密码，不填任何参数表示为组设定密码或修改密码

| ID   | 参数 | 说明                               | 格式                                | 举例                  |
| ---- | ---- | ---------------------------------- | ----------------------------------- | --------------------- |
| 1    | -a   | 将用户添加到指定组中               | gpasswd -a username groupname       | gpasswd -a test mygrp |
| 2    | -d   | 将用户从指定组中删除               | gpasswd -d username groupname       | gpasswd -d test mygrp |
| 3    | -A   | 将用户加入到指定的组的管理员列表中 | gpasswd -A user1,user2,…… groupname | gpasswd -A test mygrp |

**说明**：

3. 加入到组管理员列表中，就可以对组有操作修改的权限。例如修改组密码。

可能会有的疑问就是给组设置密码有什么用呢，这就涉及到下面的命令了。

## newgrp

命令格式：newgrp GROUP

说明：临时将用户切换到指定的组中，以指定组的身份执行命令。例如现在有一个mygrp的组，且组设置有密码，且test这个用户不属于mygrp这个组，那我们就可以将test这个用户临时切换到mygrp这个组中，先不进行切换，在tmp下建立一个文件a.txt，看一下此文件的所属组，现在是属于root这个组。

![1573956250451](C:\Users\LIGHTI~1\AppData\Local\Temp\1573956250451.png)

然后使用newgrp这个命令，再建立一个b.txt，看一下所属组。此时所属组就变成了mygrp这个组，这就是临时以某个组的身份执行命令。

![1573956357329](C:\Users\LIGHTI~1\AppData\Local\Temp\1573956357329.png)

此时id一下用户，可以看到用户目前属于mygrp这个组。使用exit即可退出newgrp。

![1573956590277](C:\Users\LIGHTI~1\AppData\Local\Temp\1573956590277.png)

**注意**：需要注意的是，当用户属于待newgrp这个组的时候，切换过去不需要密码，如果用户不属于这个组，切换过去就需要密码了，这就是为什么设置组密码的原因了。

## chage

命令格式：chage \[options] LOGIN

说明，修改用户的密码期限相关信息

| ID   | 参数 | 说明                                                         |
| ---- | ---- | ------------------------------------------------------------ |
| 1    | -d   | 最后一次修改密码的日期（last_day），对应/etc/shadow中的第三个字段 |
| 2    | -E   | 密码到期的时间，对应/ect/shadow中的倒数第二个字段，相当于usermod -e |
| 3    | -I   | 密码到期后容忍登录的时间，对应/ect/shadow中的倒数第三个字段，相当于usermod -f |
| 4    | -m   | 密码最短使用的时间，对应/ect/shadow中的第四个字段，相当于passwd -n |
| 5    | -M   | 密码的最长使用期限，对应/etc/shadow中的第五个字段，相当于passwd -x |
| 6    | -W   | 密码过期前的警告时间，对应/etc/shadow中的第六个字段，相当于passwd -w |

[链接到passwd命令](http://39.106.81.183:8000/passwd%e5%91%bd%e4%bb%a4/ )

[链接到usermod命令](http://39.106.81.183:8000/usermod%e5%91%bd%e4%bb%a4/ )

## 需要了解的命令

1. chfn 更改用户的comment，相当于usermod -c，只不过是更详细一些。
2. pwck，检查密码的格式是否正确。
3. finger，查看用户的信息，不过这个指令是需要安装的
4. chsh，更改用户的shell