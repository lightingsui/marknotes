## centos7中安装python3

### 1.安装相应的编译工具

在root用户下(不要用普通用户,麻烦),全部复制粘贴过去,一次性安装即可.

```
yum -y groupinstall "Development tools"
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
yum install -y libffi-devel zlib1g-dev
yum install zlib* -y
```

### 2.下载安装包

```
wget wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tar.xz
```

### 3.解压

```
tar -xvJf  Python-3.7.2.tar.xz
```

### 4.创建编译安装目录

```
mkdir /usr/local/python3 
```

### 5.安装

```
cd Python-3.7.2
./configure --prefix=/usr/local/python3 --enable-optimizations --with-ssl 
#第一个指定安装的路径,不指定的话,安装过程中可能软件所需要的文件复制到其他不同目录,删除软件很不方便,复制软件也不方便.
#第二个可以提高python10%-20%代码运行速度.
#第三个是为了安装pip需要用到ssl,后面报错会有提到.
make && make install
```

[what-does-enable-optimizations-do-while-compiling-python](https://stackoverflow.com/questions/41405728/what-does-enable-optimizations-do-while-compiling-python)

### 6.创建软链接

```
ln -s /usr/local/python3/bin/python3 /usr/local/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3
```

### 7.验证是否成功

```
python3 -V
pip3 -V
```

### 8.报错处理

错误1.

```
zipimport.ZipImportError: can't decompress data; zlib not available Makefile:1099: recipe for target 'install' failed make: *** [install] Error 1
```

需要安装依赖

```
yum -y install zlib1g-dev
```

错误2.

```
ModuleNotFoundError: No module named '_ctypes'
```

需要安装依赖

```
yum -y install libffi-devel 
```

这两个错误需要的依赖已经添加到一开始的依赖安装上去了
[参考文章](https://blog.csdn.net/elija940818/article/details/79238813)

### 9.安装pipenv

在centos中使用python3.7或以上版本,进行pip install 命令容易报错

```
pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
Could not fetch URL https:*******: There was a problem confirming the ssl certificate: 
Can't connect to HTTPS URL because the SSL module is not available. - skipping
```

### 在./configure过程中，如果没有加上–with-ssl参数时，默认安装的软件涉及到ssl的功能不可用，刚好pip3过程需要ssl模块，而由于没有指定，所以该功能不可用。解决办法是重新对python3.6进行编译安装，用一下过程来实现编译安装:

```
cd Python-3.7.2
./configure --with-ssl
make && make install
```

即可正常使用pip安装.
这个也在安装python的时候指定了.
[参考文章](https://blog.csdn.net/jeryjeryjery/article/details/77880227)

### 10.修改pip安装源

修改系统pip安装源
在家目录下新建`.pip`文件夹,进入文件夹新建文件`pip.conf`之后写入相应镜像网站地址

```
cd ~
mkdir .pip
cd .pip
vim pip.conf

#进入后添加以下内容,保存退出.
[global]
index-url = https://mirrors.aliyun.com/pypi/simple
```

修改pipenv安装源
在自己的虚拟环境中找到`Pipfile`文件,将其中的`url = "https://pypi.org/simple"`修改为你需要的国内镜像,如`https://mirrors.aliyun.com/pypi/simple/`

```
[root@localhost myproject]# vim Pipfile 


[[source]]
name = "pypi"
url = "https://pypi.org/simple" # 改为url = "https://mirrors.aliyun.com/pypi/simple/"
verify_ssl = true

[dev-packages] #这里是开发环境专属包,使用pipenv install --dev package来安装专属开发环境的包

[packages] # 全部环境的通用包,安装在这里.

[requires]
python_version = "3.7"
```

春有百花秋有月, 夏有凉风冬有雪. 若无闲事挂心头, 便是人间好时节. 春花生厌秋月影, 夏风无常冬雪凌. 不染浊尘性本空, 无心如是利有情.