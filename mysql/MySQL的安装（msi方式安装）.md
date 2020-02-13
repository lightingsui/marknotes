## MySQL的安装（msi方式安装）

我们常见的mysql服务器都是安装在类Unix系统中（Centos发行版），也有的安装在Mac中，但是由于本人过于穷，买不起Mac，所以选择安装在了windows中，我过几天会总结一篇如何在centos系统中安装mysql。无论安装在哪里，切记，一定要记住安装目录。

> 扩展知识：类Unix系统包括 FreeBSD、Linux、macOS、Solaris等，而Linux的发行版又包括很多，例如 Debian、SUSE  、Fedora 、CentOS等

Mysql在window中安装分为msi安装和zip解压方式，这次我总结的是msi安装的方式，以后再演示zip解压的方式

本人推荐安装mysql5.7，下载地址为<https://dev.mysql.com/downloads/mysql/5.7.html> 

如果是在windows中安装，则可以直接进入<https://dev.mysql.com/downloads/windows/installer/5.7.html>

直接进入第一个地址是下载不了windows版本的，需要点击进入windows版本下载界面

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E7%89%88%E6%9C%AC%E9%80%89%E6%8B%A9.png)

然后选择对应的版本

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E9%80%89%E6%8B%A9.png)

点击download后进入如下界面，直接操作即可

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E7%9B%B4%E6%8E%A5%E4%B8%8B%E8%BD%BD%E5%8D%B3%E5%8F%AF.png)

> 注意：下载下来的是msi版本，安装此版本的软件需要操作系统的支持，即widows中需要安装Microsoft Installer 2.0 ，自从windows XP版本开始，windows系统中已经默认安装了Microsoft Installer 2.0

接下来就是傻瓜式安装了，可能会比傻瓜式安装聪明一点，暂且叫笨蛋式安装吧。

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E7%AC%AC%E4%B8%80%E6%AD%A5.png)

接着next

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E7%AC%AC%E4%BA%8C%E6%AD%A5.png)

next

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E6%8E%A5%E7%9D%80%E4%B8%8B%E4%B8%80%E6%AD%A5.png)

next

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E6%8E%A5%E7%9D%80%E7%9D%80%E4%B8%8B%E4%B8%80%E6%AD%A5.png)

此处需要注意的是Port端口，mysql服务器默认监听3306端口，无特殊情况切勿修改，以免酿成大错。

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E6%8E%A5%E7%9D%80%E7%9D%80%E7%9D%80%E4%B8%8B%E4%B8%80%E6%AD%A5.png)

**接下来就是贴出几张主要步骤的图了**

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E8%BE%93%E5%85%A5Root%E5%AF%86%E7%A0%81.png)

输入Root密码，此密码一定要记住，要不然连接不上数据库，修改起来也很麻烦。

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/mysql%E6%9C%8D%E5%8A%A1%E5%BC%80%E6%9C%BA%E8%87%AA%E5%90%AF.png)

建议不要选择开机自动启动mysql服务器程序

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/%E9%AA%8C%E8%AF%81root%E5%AF%86%E7%A0%81.png)

输入之前填写的Root密码。

到此，mysql安装完成。但是我们要将mysql放在系统环境变量中，找到mysql安装路径，默认在`C:\Program Files\MySQL\MySQL Server 5.7`中，进入到bin文件夹中，选择选中bin的目录绝对路径，添加到系统环境变量中

`C:\Program Files\MySQL\MySQL Server 5.7\bin`，之前电脑配置完环境变量只需要重新打开cmd窗口即可，但是近期发现配置完环境变量还得重启电脑才能生效。

重启电脑后，我们需要先将mysqld注册为服务。

```bash
"完整的可执行文件路径" --install [-manual] [服务名]
```

完整的路径就是mysqld的路径，即`C:\Program Files\MySQL\MySQL Server 5.7\bin\mysqld.exe`，其中参数

-manual加上就表示开机不自动启动该服务，不加就表示开机自动启动该服务。服务名可以省略，mysqld默认的服务名就为MYSQL。

接下来就是要启动mysqld服务了，注意，启动服务一定要用管理员身份打开cmd，否则会报错的，错误如下

```bash
发生系统错误 5。

拒绝访问。
```

```bash
# 打开mysqld服务
net start mysql
# 关闭mysqld服务
net stop mysql
```

打开服务后，我们就可以使用客户端(mysql)尽情的玩耍了。

> 我安装完mysql并没有注册驱动，我发现在我的服务列表中，已经存在了一个名为mysql57的服务，我直接启动此服务即可