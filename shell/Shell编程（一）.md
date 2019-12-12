# Shell编程（一）

## 1、Shell编程介绍

什么是`shell`编程？`shell`编程就是对`linux`命令的罗列，并且带有逻辑的代码。

`shell`是一种过程式的、解释执行的脚本语言，并且具有一般高级语言所具有的像变量、数组、选择、判断、循环等。

要想掌握`shell`编程，就必须熟练使用`linux`的命令、管道、`I/O重定向`等，说白了就是对各个命令的结果做操作来完成我们所需要完成的功能。

`shell`的解释与执行需要依赖于`linux`平台已有的`shell`解释器，将`shell`程序中已有的文本命令解释成0或1这种的机器语言，从而`cpu`才能执行。

## 2、Shebang

`Shebang`就是指定解释器的路径以及名称，需要在`shell`脚本的开头处进行指定，如果不进行指定，那么此执行此`shell`脚本就无法将其加载进内核执行，那么就和普通的文本文件也就没啥区别了。

例如在编写`python`代码时，就需要在文件开头执行`python`解释器的位置，或`perl`语言，同样的道理

```bash
#!/bin/bash
#!/usr/bin/python
#!/usr/bin/perl
```

## 3、入门案例

新建立一个脚本（脚本一般以.sh结尾，约定俗成），程序的执行结果用来输出`/usr`目录下的文件，即`ls -l`命令

```bash
#!/bin/bash

ls -l /usr
```

因为默认创建的文件是没有执行权限的，所以需要修改文件的执行权限

```bash
chmod +x /tmp/test.sh
```

接下来直接运行脚本

```bash
./test.sh
```

我们当前是在`/tmp`目录下，既然我们执行本目录下的脚本，为什么还要指定路径呢(./   代表/tmp/)，因为如果不指定绝对路径，那么linux就会去环境变量中存在的路径下寻找test.sh这个文件，那么肯定是找不到的，因为tmp没有在环境变量中，因此就会报错，所以需要指定(使用./)指定当前脚本的路径位置，才能执行。执行结果为：

```bash
[root@oracle tmp]# ./test.sh 
总用量 280
dr-xr-xr-x.   2 root root 53248 10月  8 18:25 bin
drwxr-xr-x.   2 root root     6 4月  11 2018 etc
drwxr-xr-x.   2 root root     6 4月  11 2018 games
drwxr-xr-x.  41 root root  8192 10月  7 15:00 include
dr-xr-xr-x.  43 root root  4096 10月  7 15:00 lib
dr-xr-xr-x. 145 root root 81920 10月  7 15:12 lib64
drwxr-xr-x.  50 root root 12288 10月  7 15:00 libexec
drwxr-xr-x.  13 root root   150 11月 23 22:09 local
dr-xr-xr-x.   2 root root 20480 10月  8 18:25 sbin
drwxr-xr-x. 239 root root  8192 10月  8 18:24 share
drwxr-xr-x.   4 root root    34 5月  25 2019 src
lrwxrwxrwx.   1 root root    10 5月  25 2019 tmp -> ../var/tmp
```

还有另外的一种执行方法就是不用修改文件的权限，而是使用bash的参数来传递文件

```bash
bash test.sh
```

此时就可以不用写文件的绝对路径了，使用相对路径也是可以的并且可以不用修改权限，得到的结果是与上面相同的。

----------------------------END--------------------------------------