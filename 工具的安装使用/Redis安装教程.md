## Redis安装教程

此次安装是在Centos上安装，并非Windows平台

第一件事就是去下载源码包，官方网站<https://redis.io/download> ，也可以去我的网盘下载

> 链接：https://pan.baidu.com/s/1IsbBuvMDdDkVm736CjzLHg 
> 提取码：0ahw

**解压安装包**

```bash
tar -zxvf redis-4.0.14.tar.gz
```

**移动安装包到/usr/local下**

```bash
mv redis-4.0.14 /usr/local
```

**进入安装包src下**

```bash
cd redis-4.0.14/src
```

**编译安装**

```bash
make & make install
```

> 执行此步骤需要提前安装gcc
>
> yum install gcc

执行./redis-server即可启动服务端，默认端口号为6379

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-16_00-41-17.png)

还需要进行一些配置，可以查看博客<https://blog.csdn.net/qq_40697120/article/details/103317205> 