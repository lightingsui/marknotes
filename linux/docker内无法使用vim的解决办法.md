这很简单，两条命令， 

进入到镜像内

1.apt-get update 

2.apt-get install vim 

只不过，在执行第一条命令的时候，会失败，因为默认的下载的地址是海外的，会网速慢或者下载失败，所以我们移步国内镜像源（以下代码复制到你的命令行下）： 

```sh
mv /etc/apt/sources.list /etc/apt/sources.list.bak
echo "deb http://mirrors.163.com/debian/ jessie main non-free contrib" >> /etc/apt/sources.list
echo "deb http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list
echo "deb-src http://mirrors.163.com/debian/ jessie main non-free contrib" >>/etc/apt/sources.list
echo "deb-src http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list
```

更新安装源 

```sh
apt-get update
```



