## chmod

**命令格式**：chmod  [OPTION]...  MODE[,MODE]...  FILE...
       		   chmod [OPTION]...  OCTAL-MODE  FILE...
                   chmod [OPTION]...  --reference=RFILE  FILE...

**说明**：第一种格式是修改修改`某一类用户`的`某一位`或`多位权限`。第二种是修改`多类`用户的`多个权限`，第三种是`参考`另一个`文件或目录`来设置`当前文件`或`目录`的`权限`，

**第一种**：例如我只想修改一个文件所属主的某个权限时，就可以使用chmod命令，如下图，我想给test.file中所属主加上一个可以执行的权限。

![1574211154503](C:\Users\LIGHTI~1\AppData\Local\Temp\1574211154503.png)

`u`代表着文件或目录的`所属主`，`g`代表文件或目录的`基本组`，`o`代表着`其他人`，想给某种用户`加上某种权限`就`使用`用户的代号加上权限。

如果想一起修改两个种类用户的某个权限，可以使用

```bash
chmod ug-x test.dir
# 或者
chmod u-x, g-x test.dir
```

如果想一起修改`三类用户的权限`，可以使用

```bash
chmod a+x test.dir
```

a就代表着u、g、o，也可以把`a直接省略`，写成

```bash
chmod +x test.dir
```

还可以直接指定某类用户的权限，例如：

```bash
chmod u=rx
```

那么所属主就被指定为了r-x权限，也可以使用

```bash
chmod ug=rx
chmod a=rx
```

如果想`去除文件或目录的所有权限`，可以使用

```bash
chmod a= test.dir
```

注意，此处使用`chmod =rwx test.dir`不会将`三类用户`都指定为rwx，只会将`所属主`的权限变为`rwx`。

**第二种**：给出三位8进制数，从而代表权限。

此时我们来查看一下目录的权限。

![1574212674592](C:\Users\LIGHTI~1\AppData\Local\Temp\1574212674592.png)

现在我想把所属主的权限改为rw-，所属组的权限改为r-x，其他人改为r-x，先把这个三类权限改为8进制数，即为655，所示可以这么玩。

```bash
chmod 655 test.dir
```

**第三种**：参考其他文件或目录为当前文件或目录设置权限。

![1574213031965](C:\Users\LIGHTI~1\AppData\Local\Temp\1574213031965.png)

## chmod常用选项

-R    递归修改目录的权限。

![1574213189823](C:\Users\LIGHTI~1\AppData\Local\Temp\1574213189823.png)

当修改`目录的权限`时，`子文件或目录`的权限是`不变`的，如果想要将修改的目录下所有的子文件和父目录`同步`，就可以使用-R选项。