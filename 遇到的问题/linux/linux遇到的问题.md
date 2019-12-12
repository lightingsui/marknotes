# linux遇到的问题

## 1、为何用rpm -i安装不了rpm软件包?说是找不到目录或是文件 

原因是rpm包可能出现损坏，只需要去指定地址或路径去浏览器直接下载再传回到linux即可安装



## 2、CentOS cannot find a valid baseurl for repo base/7/x86_64

网络不通，检查网络连接，重新连接即可



## 3、xshell连接不上服务器（能ping通）

出现这个原因是因为外连接端口的原因，此时可以修改/etc/shh/ssh_config文件中的port，改为22端口即可

