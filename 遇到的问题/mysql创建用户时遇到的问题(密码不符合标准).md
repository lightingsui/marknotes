## mysql创建用户时遇到的问题(密码不符合标准)

### 问题描述

**1819 - Your password does not satisfy the current policy requirements**

![1574863441991](C:\Users\LIGHTI~1\AppData\Local\Temp\1574863441991.png)

### 出现问题的原因

出现这个问题是由于mysql默认的密码策略较高，我设定的密码过于简单，因此，我的想法就是修改mysql的默认密码等级。

### 问题的解决

使用语句

```sql
SHOW VARIABLES LIKE 'validate_password%';
```

得到的结果

![1574863611813](D:\typora文件\遇到的问题\1574863611813.png)

我们将密码等级修改的更第一点就行了，修改为LOW

```sql
set global validate_password_policy=LOW;
```

![1574863677431](C:\Users\LIGHTI~1\AppData\Local\Temp\1574863677431.png)

这里还有一个密码的最短长度，这个有需要就可以修改，没需要的就不用动了，采用默认的就可以了。

-------------------------------------END---------------------------------------------------------