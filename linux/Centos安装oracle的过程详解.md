## Centos安装oracle的过程详解

> 主机环境：CentOS Linux release 7.7.1908 (Core)
>
> Oracle版本：Oracle 12c Release2

安装过程参考 <https://blog.51cto.com/10316297/2140427?source=dra> ，但是在安装过程中，可能会遇到下面的问题，接下来对下面的问题进行介绍

### 1、已拒绝X11转移申请

![1574729001672](C:\Users\LIGHTI~1\AppData\Local\Temp\1574729001672.png)

**解决办法**:安装`yum install xorg-x11-xauth` 

### 2、使用xstart打开图形界面无法登陆的问题

输入：/usr/bin/xterm -ls -display $DISPLAY 

![img](https://img-blog.csdn.net/20160706153659753?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) 

可能会报下面的错误

![img](https://img-blog.csdn.net/20160706153727346?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) 

**解决方案**：

```bash
yum install -y xterm
```

然后重新登陆

登陆进去之后，输入xclock，如果命令报错，则执行`yum install xorg-x11-apps` ，安装完再次输入xclock，则会出现

![img](https://img-blog.csdn.net/20160706153820299?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) 

问题解决完毕。

### 3、xhost未找到的命令

![1574729209879](C:\Users\LIGHTI~1\AppData\Local\Temp\1574729209879.png)

**解决办法**：先使用`yum whatprovides "*/xhost"`进行结果搜索，然后根据结果进行安装，例如搜索到的结果如下

![1574729273806](C:\Users\LIGHTI~1\AppData\Local\Temp\1574729273806.png)

则使用`yum install xorg-x11-server-utils-7.7-20.el7.x86_64`

### 4、xstart界面中文乱码的问题

**解决方案**：

![img](https://img-blog.csdn.net/20131017222920250) 

### 5、\>>> Could not execute auto check for display colors using command /usr/bin/xdpyinfo. Check if the DISPLAY variable is set.    Failed \<<<   

**问题描述**：无法使用命令/usr/bin/xdpyinfo自动检查显示器颜色

**解决方案**：把xstart系统注销，然后使用Oracle用户直接登录系统进入即可！还不行就安装xdpyinfo

`yum install xdpyinfo` 