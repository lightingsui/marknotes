## chown

**命令格式**：

chown [OPTION]...  \[OWNER][:[GROUP]]   FILE...
chown [OPTION]...  --reference=RFILE  FILE...

**说明**：修改文件或者目录的所属主和所属组

**演示**：

例如我们在/tmp下有一个叫做a.txt的文件，所属主和所属组如下所示。

![1574214477214](C:\Users\LIGHTI~1\AppData\Local\Temp\1574214477214.png)

**我们将a.txt的所属主改为testuser，可以使用**

```bash
chown testuser a.txt
```

![1574214604773](C:\Users\LIGHTI~1\AppData\Local\Temp\1574214604773.png)

**如果我们只想修改所属组，那么可以这么使用**

```bash
chown :testuser a.txt
```

**如果既想修改所属主，又想修改所属组，则可以使用**

```bash
chown testuser:testuser a.txt
```

引用其它文件或目录修改当前文件或目录的所属主或者所属组我就不说了，和chmod的用法是大同小异的，大家可以参考[我的另一篇关于chmod讲解的博客](http://39.106.81.183:8000/chmod%e5%91%bd%e4%bb%a4/ )。

## 常用选项

-R 递归修改所属主或者所属组。

说明：类似于chmod的修改方式，这个选项还是蛮常用的。

## chgrp

**命令格式**：

chgrp [OPTION]... GROUP FILE...
       chgrp [OPTION]... --reference=RFILE FILE...

**说明**：修改文件或目录的所属组的，这个功能其实使用chown就能实现了，这个命令用的很少，了解一下就行，这个命令也有一个常用的选项-R，递归修改所属组。