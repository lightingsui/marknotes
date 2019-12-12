## passwd

命令格式：passwd \[options] \[username]

说明：如果passwd后不接任何参数，标识修改自己的密码，管理员可以修改任何用户的密码，普通用户只能修改自己的密码。

|  ID  |  参数   |               说明               |                  格式                  |                举例                 |
| :--: | :-----: | :------------------------------: | :------------------------------------: | :---------------------------------: |
|  1   |   -l    |             锁定用户             |           passwd -l username           |         passwd -l testuser          |
|  2   |   -u    |             解锁用户             |           passwd -u username           |         passwd -u testuser          |
|  3   |   -n    |        为用户指定最短期限        |        passwd -n days username         |        passwd -n 2  testuser        |
|  4   |   -x    |    更改用户密码使用的最大期限    |        passwd -x days username         |         passwd -x 100 test          |
|  5   |   -w    |       更改用户密码警告时间       |        passwd -w days username         |          passwd -w 10 test          |
|  6   |   -i    | 更改用户密码过期后容忍登录的期限 |        passwd -i days username         |          passwd -i 10 test          |
|  7   | --stdin |      从标准输入接收用户密码      | echo '密码' \| passwd --stdin username | echo '22222' \| passwd --stdin test |

**说明**

1. 锁定用户，同usermod -L命令（[查看usermod命令](http://39.106.81.183:8000/usermod%e5%91%bd%e4%bb%a4/)）
2. 解锁用户，同usermod -U命令（[查看usermod命令](http://39.106.81.183:8000/usermod%e5%91%bd%e4%bb%a4/ )）
3. 指定用户密码的最短使用时间，单位为天，对应/etc/shadow中的第4个字段(默认为0)
4. 指定用户密码的最长使用时间，单位为天，对应/etc/shadow中的第5个字段(默认为99999)
5. 指定用户密码的过期前的警告时间，单位为天，对应/etc/shadow中的第6个字段(默认为7)
6. 指定用户密码的过期后容忍登录的时间，单位为天，对应/etc/shadow中的第7个字段(无默认值)
7. 从标准输入中得到密码

**拓展**

1. 数据黑洞（吞噬数据然后直接丢掉）/dev/null
2. 冒泡机（一直往出生产0）/dev/zero