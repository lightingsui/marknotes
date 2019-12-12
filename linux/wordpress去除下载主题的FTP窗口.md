> 转载  [hahachenchen789](https://me.csdn.net/hahachenchen789)  原文链接 <https://blog.csdn.net/hahachenchen789/article/details/80572915> 

在wordpress主题商店下载主题时，会弹出FTP的上传窗口，要求输入服务器的域名，以及用户名和密码。这对于本地安装的用户而言显然无法使用。

方法：在wp-config.php文件的最后一行后加入如下语句：

```sh
define("FS_METHOD", "direct");  
define("FS_CHMOD_DIR", 0777);  
define("FS_CHMOD_FILE", 0777);  
```

然后保存，并将wordpress这个目录下的所有文件权限改为777即可 :

```sh
sudo chmod -R 777 /var/www/html
```



