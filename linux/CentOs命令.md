# CentOs命令

### 1、查看端口被某个程序占用

```js
netstat -naltp | grep 80
```

### 2、杀掉进程

```js
kill -9 pid
```

### 3、查看某个进程是否在运行

```js
ps -aux | grep java
```

### 4、启动oracle以及监听程序

```js
su - oracle        //切换到自己的oracle账户
lsnrctl start      //启动oracle监听
sqlplus /nolog     //登录oracle
conn /as sysdba    //连接服务
startup            //启动服务
```

### 5、 开机自启配置

1. 如果程序是通过`yum`安装的，`yum`会自动创建`systemctl`
2. 如果不是通过yum安装，就需要进入到`/lib/systemd/system/`下，新建一个文件，以nginx举例，创建一个文件，名称为`/lib/systemd/system/nginx.service`，新建的文件内容为

```shell
[Unit]
Description=nginx
After=network.target
 
[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true
 
[Install]
WantedBy=multi-user.target
```



```shell
Description:描述服务
After:描述服务类别
[Service]服务运行参数的设置
Type=forking是后台运行的形式
ExecStart为服务的具体运行命令
ExecReload为重启命令
ExecStop为停止命令
PrivateTmp=True表示给服务分配独立的临时空间
注意：[Service]的启动、重启、停止命令全部要求使用绝对路径
[Install]运行级别下服务安装的相关设置，可设置为多用户，即系统运行级别为3
```

3. 设置开机启动`systemctl enable nginx.service `

```shell
systemctl start nginx.service　（启动nginx服务）
systemctl stop nginx.service　（停止nginx服务）
systemctl enable nginx.service （设置开机自启动）
systemctl disable nginx.service （停止开机自启动）
systemctl status nginx.service （查看服务当前状态）
systemctl restart nginx.service　（重新启动服务）
systemctl list-units --type=service （查看所有已启动的服务）
```

### 6、防火墙暴露端口

1. 查看防火墙状态

```shell
firewall-cmd --state
```

2. 开启/暴露一个端口

```shell
firewall-cmd --zone=public --add-port=8080/tcp --permanent
#最后的参数的意思是永久生效，即重启也生效
```

3. 重新载入防火墙配置

```shell
firewall-cmd --reload
```

4. 关闭一个开放的端口

```shell
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

5. 查看防火墙开放的端口

```shell
firewall-cmd --zone=public --list-ports
```

6. 查看端口的监听

```shell
netstat -alnpt
```

注：telnet测试端口需要端口有监听的程序

7. 将用户加入到某个组

```sh
usermod -G groupname username
```

注意：更改用户组之后，需要重启服务器，或者重新登录账号，才会生效！！！！ 

8. 将用户从某个组删除

```sh
gpasswd -d testuser root
```









