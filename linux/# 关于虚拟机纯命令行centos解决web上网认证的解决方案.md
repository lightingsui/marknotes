# 关于虚拟机纯命令行centos解决web上网认证的解决方案

> 虚拟机版本：CentOS Linux release 7.1.1503 (Core)

> 在我们学校，有校园网的存在，导致我在配置虚拟机的时候遇到了好多坑，如果没有校园网的存在，直接将虚拟机网络改为桥接，并且配置ip、网关、子网掩码、DNS即可实现网络的共享，但是，一切的想象都是美好的，当头一捶子呀，虚拟机配置完了这些还不行，跟使用windows相同，还是需要web网页认证，但是我的虚拟机是纯命令行的呀，哪来的浏览器啊，至此，我开始走在了填坑的道路。未完，待续。。。。。。

## 问题的发现

我的虚拟机使用的是桥接的模式，虚拟机就像是局域网内的其它电脑一样，因为我的虚拟机为搭集群准备的，其它电脑要能通过ip访问到的，像往常一样，配置完了ip、网关、DNS之后，去ping百度，结果傻眼了，根本ping不通啊，所以就一顿检查是不是这几项配错了，找了半天无果，这自信心碎了一地啊，这可咋办。后来突然想到，我的宿主机连的是校园网，是要上网认证的，是不是我的虚拟机也需要上网认证呢？结果抱着试试看的态度，用curl命令[(对curl命令不是很熟悉的小伙伴可以看一下我对curl命令的介绍)]()试了一下windows上的web认证的地址，尼玛，居然整通了，将上网认证页面的html给我返回来了。

## 问题的解决

不能上网的原因找到了，那就解决这个问题吧，首先我想的是使用curl命令去模拟表单的提交，将我的账号密码提交上去，结果试了几次没有成功，一会咱们再说为什么没有成功。

我的第二个解决的思路是使用xmanager，使用xmanager打开一个浏览器，但是此种方法是要保证centos上已经安装了浏览器的，我连网都没连上呢，哪里来的浏览器啊，果断放弃。

第三种方案就是去百度了，百度了一大堆，也没有找到解决方案，受挫太严重了，正好也到了晚饭的时间，那就去吃晚饭吧，万一突然吃饭的时候豁然开朗，想到了解决方案呢，当然，吃完晚饭也没想到解决方案，回来接着百度，我又对curl的命令百度了一下，结果，收获到了意想不到的惊喜，在chrom浏览器中，对服务器发起的请求可以复制成curl命令，给大家演示一下，<http://10.10.11.151/0.htm> 是我们学校校园认证的地址。

![1573046364617](C:\Users\LIGHTI~1\AppData\Local\Temp\1573046364617.png)

通过输入账号和密码，即可上网，在点击登陆之前，我们先打开调试工具（F12即可打开），选到network选项，如图

![1573046536160](C:\Users\LIGHTI~1\AppData\Local\Temp\1573046536160.png)

此时是一片空白，然后我们就开始点击登录按钮吧，此时就会发现，在network里多了三次请求![1573046712034](C:\Users\LIGHTI~1\AppData\Local\Temp\1573046712034.png)

其实这三个里面最主要的就是最上边的那个，剩下的两个都是对资源的请求，不是特别重要。点开第一个请求，我们可以看到请求的详细信息，不过，这也不是咱们今天的主角，咱们今天的主角是如何从chrom中找出curl已经生成好的命令，接下来，右键0.html（对应到你们自己的可能就不是0.html了，总之就是最上边这个），这个选项在copy -> copy as curl(bash)，这时就复制成功了已经生成好的curl命令了，咱们来看一下我这个自动生成的curl命令。

```sh
curl 'http://10.10.11.151/0.htm' -H 'Proxy-Connection: keep-alive' -H 'Cache-Control: max-age=0' -H 'Origin: http://10.10.11.151' -H 'Upgrade-Insecure-Requests: 1' -H 'Content-Type: application/x-www-form-urlencoded' -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3' -H 'Referer: http://10.10.11.151/0.htm' -H 'Accept-Encoding: gzip, deflate' -H 'Accept-Language: zh-CN,zh;q=0.9' --data 'DDDDD=xxxxxxx&upass=xxxxxxx&0MKKey=+&v6ip=' --compressed --insecure
```

这里面最主要的就是 --data 后的内容，这里面就是我们表单提交的的数据，账号密码等，咱们对比一下浏览器自动提交的数据。

![1573047148612](C:\Users\LIGHTI~1\AppData\Local\Temp\1573047148612.png)

表单提交的就是DDDDD和upass吧，没错吧，你们在弄的时候是参照自己表单提交的数据的。

下一步就是将自动生成的curl命令键入到虚拟机中了。整起（开始左边画龙，右边画彩虹）

怎么又给我返回一堆html代码，没事，别慌，看一下html的内容

![1573047473402](C:\Users\LIGHTI~1\AppData\Local\Temp\1573047473402.png)

在对比一下window上登陆成功的界面

![1573047584455](C:\Users\LIGHTI~1\AppData\Local\Temp\1573047584455.png)

是不是这就是登陆成功了，其实呀，遇到问题别慌，仔细的分析一下，详细的看一下内容。

这回咱们回头说一下我对这个问题的第一解决思路为什么没有成功，因为请求少了好多的请求头，服务器并没有处理我的这个请求，所以就造成了这个问题没有被解决。有点像网站的反爬虫机制。

## 总结

+ 其实解决本问题的核心思想就是使用curl命令模拟表单提交。
+ 使用chrom浏览器得到发出请求的curl命令。
+ 仔细分析界面，成功解决问题。