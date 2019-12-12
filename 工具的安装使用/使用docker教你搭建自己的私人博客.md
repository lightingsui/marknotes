## 使用docker教你搭建私人博客

## 一、前言

这个搭建私人博客可以说折腾了三回了，我的博客是放在阿里云服务器上的，但是第一次搭建被我搞垮是因为我买了一个域名，想开启https的支持，结果一不小心错误的执行了rm命令，我自己面壁了好几天。我通过这件事悟出的道理就是不要乱使用rm命令，我就是把rm命令改成mv命令。这不，弄好了之后，一直在稳定运行，但是近期我发现，博客访问不了了，查看发现，docker服务在开启后几秒钟后就会关闭，实在是弄不好啊，于是我就给阿里云重新装了系统，想着把之前备份的docker镜像再导入到新系统中，md，悲催，备份的tar包又损换了，我这人生处处是坑啊。

纵使博客虐我千百遍，我待博客如初恋。

再次开始咱们的安装之旅吧。

## 二、环境

> CentOS Linux release 7.4.1708 (Core)
>
> 阿里云的服务器，并且咱们要做的就还有开启https的支持 

## 三、安装步骤

### 1. 安装docker

```bash
yum install docker
```

安装完成之后启动docker

```bash
systemctl start docker
# 启动完成之后也可以查看docker的运行状态，显示为 active (running) 绿色的即为成功
systemctl status docker
```

### 2.准备docker-compose.yml

我把准备好的docker-compose.yml文件放在了百度网盘上了，大家有需要的下载

> 链接：https://pan.baidu.com/s/1Y2COvsZSnDQfh1shgTkCJg 
> 提取码：yv94 

![TIM截图20191210164803](D:\typora文件\assets\TIM截图20191210164803.png)

### 3. 拉镜像

进入到我们刚才准备好的docker-compose.yml路径下

```bash
docker-compose up -d
```

但是，此时会报错，就是找不到docker-compose命令

```bash
-bash: docker-compose: command not found
```

那接下来就安装docker-compose

首先先来安装拓展源

```bash
yum -y install epel-release
```

安装python-pip模块 

```bash
yum install python-pip
```

切换到`/usr/local/bin`下

```bash
wget https://github.com/docker/compose/releases/download/1.14.0-rc2/docker-compose-Linux-x86_64

# 改名
rename docker-compose-Linux-x86_64 docker-compose docker-compose-Linux-x86_64

# 加执行权限
chmod +x /usr/local/bin/docker-compose

# 使用docker-compose version查看版本
docker-compose version
```

这次再次执行第一条命令，等待结束。

当你看到如下界面时就代表完成了。

![TIM截图20191210163809](D:\typora文件\assets\TIM截图20191210163809.png)

### 4. 登录wordpress进行配置

打开浏览器输入ip地址即可打开配置界面，里面就是填一些配置信息，但是密码一定要记住，便于日后对博客进行管理。

### 5. 开启wordpress对https的支持

开启https需要签名的ssl证书，国内各大云厂商都免费提供这个服务，我就以阿里云为例子，下载下来就好，记得下载这个apache的。

![TIM截图20191210170656](D:\typora文件\assets\TIM截图20191210170656.png)

进入到docker中wordpress中。

```bash
# 先查看docker容器的id
docker ps

# 进入到容器内部
docker exec -it 你的容器id /bin/bash
```

由于需要修改配置文件，需要安装vim，以及上传文件所使用的的lrzsz。

```bash
apt-get update

apt-get install vim

# 上传文件用
apt-get install lrasz
```

这个`lrzsz`就是在`xshell`中，可以将`windows`的文件直接拖到`xshell`当前的路径下，文件即可上传。

接下来就是重要的几步了（修改配置文件）

首先先来检测一下服务器中是否存在`openssl`

```bash
# 直接输入openssl
openssl
```

![TIM截图20191210231612](D:\typora文件\assets\TIM截图20191210231612.png)

出现如上界面代表存在`openssl`。

```bash
# 接下来输入a1enmod或者a2enmod，有一个有结果就算通过了
a1enmod
a2enmod
```

接下来在`/etc/apache2`下建立一个ssl文件夹，将下载的证书上传（这个时候就是使用lrzsz了），如下。![TIM截图20191210231957](D:\typora文件\assets\TIM截图20191210231957.png)

配置`/etc/apache2/sites-enabled/default-ssl.conf` ,如果在这个目录下不存在`default-ssl.conf`不存在则需要从`/etc/apache2/sites-availabel`下拷贝过来。

修改如下几处

![TIM截图20191210232453](D:\typora文件\assets\TIM截图20191210232453.png)

我在网上看到有像下面这样修改的，和我的完全不同，但是我这是可以运行的。

![img](https://upload-images.jianshu.io/upload_images/11223715-e00070dbb4b651a1.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

接下来检查一下配置，在/ect/apache2/mods-enabled是否存在ssl.conf，ssl.load。如果检测不存在，那就是没有执行a1enmod或者a2enmod 。

接下来再来修改配置文件，`/etc/apache2/sites-available/000-default.conf`，这个就是做http强行跳转https的。

![TIM截图20191210233453](D:\typora文件\assets\TIM截图20191210233453.png)

改上面的这些配置文件一定要仔细，如果修改错误，将来重新启动docker，那么docker是无法运行这个容器的，就得重新拉取docker-compose.yml。

重新启动docker

```bash
systemctl restart docker
```

接下来就是修改home和siteurl了，这时候就需要去数据中修改，因为我们的数据库在docker中，所以需要进入到docker容器中修改。

查看容器id

![TIM截图20191211083200](D:\typora文件\assets\TIM截图20191211083200.png)

```bash
# 进入到docker中
docker exec -it 容器id bash
```

这就需要输入我们在docker-compose.yml中定义的密码了，选中workpress数据库

```sql
update wp_options set option_value='https://www.lightingsui.com' where option_name='siteurl';
update wp_options set option_value='https://www.lightingsui.com' where option_name='home';
```

那么docker中wordpress开启对https的支持就做完了，接下来打开就可以进行https访问了。但是需要在wordpress中装两个关于ssl的插件。

![TIM截图20191210233919](D:\typora文件\assets\TIM截图20191210233919.png)

彻底完成。

## 四、其他问题

### 1. wp-postview统计

使用wp-postview可以很轻松的帮助我们统计每篇文章的浏览数量，以及网站的访问人数，只需要在php中嵌入如下代码即可帮助我们进行统计

```php
<?php if(function_exists('the_views')) { the_views(); } ?>
```

使用wp-postview期间我自己遇到了bug，就是每次访问之后，访问次数会加2，让人很头疼，解决办法就是修改wp-postview的代码，找到 插件 > 插件编译器，然后选中wp-postview插件

![TIM截图20191211082742](D:\typora文件\assets\TIM截图20191211082742.png)

选中这个里面 + 1的代码，修改成加0.5即可。

### 2. 其它统计

日志(文章)总数：

```php
<?php $count_posts = wp_count_posts(); echo $published_posts = $count_posts->publish;?>
```

草稿数目：  

```php
<?php $count_posts = wp_count_posts(); echo $draft_posts = $count_posts->draft; ?>
```

评论总数： 

```php
<?php echo $wpdb->get_var("SELECT COUNT(*) FROM $wpdb->comments");?>
```

建站天数： 

```php
# 记得修改日期
<?php echo floor((time()-strtotime("2012-11-22"))/86400); ?>
```

标签总数： 

```php
<?php echo $count_tags = wp_count_terms('post_tag'); ?>
```

页面总数： 

```php
<?php $count_pages = wp_count_posts('page'); echo $page_posts = $count_pages->publish; ?>
```

分类总数： 

```php
<?php echo $count_categories = wp_count_terms('category'); ?>
```

链接总数： 

```php
<?php $link = $wpdb->get_var("SELECT COUNT(*) FROM $wpdb->links WHERE link_visible = 'Y'"); echo $link; ?>
```

用户总数： 

```php
<?php $users = $wpdb->get_var("SELECT COUNT(ID) FROM $wpdb->users"); echo $users; ?>
```

最后更新： 

```php
<?php $last = $wpdb->get_results("SELECT MAX(post_modified) AS MAX_m FROM $wpdb->posts WHERE (post_type = 'post' OR post_type = 'page') AND (post_status = 'publish' OR post_status = 'private')");$last = date('Y-n-j', strtotime($last[0]->MAX_m));echo $last; ?>
```



