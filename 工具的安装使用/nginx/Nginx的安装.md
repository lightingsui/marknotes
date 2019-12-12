## Nginx的安装

我们安装的是`nginx`的二进制源码包，所以首先就是要获取到`nginx`的二进制源码包，这里我给大家提供了一个`nginx 1.16.0`的安装包，大家可以免费下载，网盘链接如下

> 链接：https://pan.baidu.com/s/1t2Dn7hUozYfEQBprJcSZbw 
> 提取码：g3xd 

首先将下载下来的安装包存放在`/tmp`下（大家放在哪里都行，我就将安装包放在`/tmp`下了），首先安装一个软件叫做`lrzsz`，在`xshell`下可以直接将`windows`下的文件拖到`linux`下。

```bash
yum -y install lrzsz
```

将/tmp下的安装包解压开

```bash
tar -zxvf /tmp/nginx-1.16.0.tar.gz
```

进入到解压的目录中，这时我们就可以进行源码包的安装了

首先进行`configure`，我这里还对`configure`进行了一下介绍

> 本文下面的引言部分借鉴<https://www.cnblogs.com/tinywan/p/7230039.html> 
>
> 这一步一般用来生成 Makefile，为下一步的编译做准备，你可以通过在 configure 后加上参数来对安装进行控制，比如代码:./configure –prefix=/usr 意思是将该软件安装在 /usr 下面，执行文件就会安装在 /usr/bin （而不是默认的 /usr/local/bin),资源文件就会安装在 /usr/share（而不是默认的/usr/local/share）。同时一些软件的配置文件你可以通过指定 –sys-config= 参数进行设定。有一些软件还可以加上 –with、–enable、–without、–disable 等等参数对编译加以控制，你可以通过允许 ./configure –help 查看详细的说明帮助。
>
> configure是一个shell脚本，它可以自动设定源程序以符合各种不同平台上Unix系统的特性，并且根据系统叁数及环境产生合适的Makefile文件或是C的头文件(header file)，让源程序可以很方便地在这些不同的平台上被编译连接。  

这里就不指定`-prefix`了，使用默认的装在`/usr/local`下面(说明一下，我这里安装的`nginx`要是用`ssl`，所以需要安装`ssl`模块，就是使用`-with`，大家不需要使用`https`的可以自动忽略`-with`以及之后的内容)

```bash
./configure --with-http_stub_status_module --with-http_ssl_module
```

因为我这是新安装的虚拟机，所以里面什么软件都没有，更没有`gcc`，因此报了下面的错误

```bash
[root@ia-8 nginx-1.16.0]# ./configure --with-http_stub_status_module --with-http_ssl_module
checking for OS
 + Linux 3.10.0-229.el7.x86_64 x86_64
checking for C compiler ... not found

./configure: error: C compiler cc is not found
```

那好吧，开始安装`gcc`吧

```bash
yum -y install gcc
```

安装完毕后再重新`configure`一下，`what？`在尾部又报错了？

```bash
./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option, or install the PCRE library into the system, or build the PCRE library
statically from the source with nginx by using --with-pcre=<path> option.
```

出现这个原因使我们缺少ssl的依赖库，如果不装ssl模块的小伙伴不会遇到这个问题。

其实解决这个简单，安装`pcre-devel`

```bash
yum -y install pcre-devel
```

再次`configure`。又报错，不活了，但是生活还是很美好的，接着解决错误

```bash
./configure: error: SSL modules require the OpenSSL library.
You can either do not enable the modules, or install the OpenSSL library
into the system, or build the OpenSSL library statically from the source
with nginx by using --with-openssl=<path> option.
```

解决办法

```bash
yum -y install openssl openssl-devel
```

接着`configure`，当出现下面这样的界面时，代表`configure`成功了

![1574505301000](C:\Users\LIGHTI~1\AppData\Local\Temp\1574505301000.png)

接下来就是编译了，使用`make`进行编译，同样，我也对`make`进行了一下介绍

> ​        这一步就是编译，大多数的源代码包都经过这一步进行编译（当然有些perl或python编写的软件需要调用perl或python来进行编译）。如果 在 make 过程中出现 error ，你就要记下错误代码（注意不仅仅是最后一行），然后你可以向开发者提交 bugreport（一般在 INSTALL 里有提交地址），或者你的系统少了一些依赖库等，这些需要自己仔细研究错误代码。
>
> 　　可能遇到的错误：make *** 没有指明目标并且找不到 makefile。问题很明了，没有Makefile，怎么办，原来是要先./configure 一下，再make。

```bash
make
```

检察一下有没有发现`error`，没有`error`就代表编译成功

紧接着就是要进行安装了，使用`make install`，关于`make install`的介绍如下

> 这条命令来进行安装（当然有些软件需要先运行 make check 或 make test 来进行一些测试），这一步一般需要你有 root 权限（因为要向系统写入文件）。 

```bash
make install
```

当然这里还有一个更快捷的指令就是`make && make install`，意思就是顺序的执行`make和make install`，如果编译的时候出现错误，就不会进行`make install`，安装完成后，再执行以下`make clean`，目的是清除编译产生的链接文件以及中间文件。

至此，我们的`nginx`安装完毕，

注意：有些小伙伴的防火墙是开着的，是无法访问到`nginx`的，需要开放`80`端口或者关闭防火墙

`centos7`查看防火墙的状态

```bash
firewall-cmd --state
# running 代表防火墙是开着的
# not running代表防火墙是开着的
```

**解决方案1(开放80端口)**

```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

`--permanent`代表永久开启80端口，重启之后也不受影响

**解决方案2(关闭防火墙)**

```bash
systemctl stop firewalld.service
```

开启`nginx`

进入到`/usr/local/nginx/sbin`下，执行

```bash
./nginx
```

然后在浏览器中就可以使用ip地址进行访问了

![1574506359179](C:\Users\LIGHTI~1\AppData\Local\Temp\1574506359179.png)

当出现这个界面就代表`nginx`安装成功。

紧接着就是一些善后的操作，将`nginx`设置为开机自启，并且优化`nginx`的启动方式，在`/lib/systemd/system`下建立一个文件，叫做`nginx.service`，将文件填充以下内容

```bash
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

以后启动`nginx`就方便多了，可供参考的命令

```bash
systemctl start nginx.service　（启动nginx服务）
systemctl stop nginx.service　（停止nginx服务）
systemctl enable nginx.service （设置开机自启动）
systemctl disable nginx.service （停止开机自启动）
systemctl status nginx.service （查看服务当前状态）
systemctl restart nginx.service　（重新启动服务）
systemctl list-units --type=service （查看所有已启动的服务）
```

`systemctl enable nginx.service`，设置成开机自启

**链接内容**：

1. 二进制源码包的安装 <https://www.lightingsui.com>

--------------------------------  END  -------------------------------------