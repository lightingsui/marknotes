# curl命令的使用

> curl命令是一个模拟浏览器请求的命令，当你去curl一个网址的时候，会返回给你网站的html代码，因此可以代替浏览器去发送请求，适用于那些纯命令行界面的linux，给您更爽的体验，话不多说，来看看curl命令是怎么用的。

## 参数介绍

### 1、[- o]

**语法格式：**curl -o [filename] url

**举例：**curl -o /tmp/sina.txt www.sina.com

**说明：**访问www.sina.com这个网站，并且返回的结果保存在`/tmp/sina.txt`这个文件中，类似于输出重定向 `curl www.sina.com > /tmp/sina.com`

**结果：**

![1573088200871](C:\Users\LIGHTI~1\AppData\Local\Temp\1573088200871.png)



### 2、 [- i ]

**语法格式：**curl -i url

**举例：**curl -i www.sina.com

**说明：**展示http响应报文

**结果：**

![1573088721016](C:\Users\LIGHTI~1\AppData\Local\Temp\1573088721016.png)



### 3、 [- I]

**语法格式：**curl -I url

**举例：**curl -I www.sina.com

**说明：**仅展示响应的响应行和响应头，不展示响应体，也就是不会展示Html代码

**结果：**

![1573088939450](C:\Users\LIGHTI~1\AppData\Local\Temp\1573088939450.png)



### 4、[- v]

**语法格式：**curl -v url

**举例：**curl -v www.sina.com

**说明：**得到HTTP具体的请求以及响应过程，并返回html代码以及详情

**结果：**由于这段相应结果过长，我就不上图了，但是返回的结果完全**描述了http请求以及响应的过程**，后续我会详细介绍http的机制，咱们先来简单说一下，首先客户端（也就是咱们的电脑）先**和服务器进行连接**，然后**发出Request请求**，服务器处理完请求，**返回给客户端响应(Response)**。如果采用的是**Http1.0**，那么就会**立即断开**连接，如果采用的是**http1.1**，那么就**不会立即断开**连接，而是会过一段时间才断开。大家看一下自己实验返回的结果，是不是和我说的顺序对应上了。



### 5、[- L]

语法格式：curl -L url

举例：curl -L www.sina.com

说明：有些网页是带有自动跳转的功能的，使用-L选项就是开启自动跳转的这个功能

结果：返回html代码



### 6、[--trace filename]

**语法格式：**curl --trace filename www.sina.com

**举例：**curl --trace detail.txt www.sina.com

**说明：**返回比-v还要详细的客户端-服务器交互信息，并存储到文件中

**结果：**只截取了部分

![1573089809848](C:\Users\LIGHTI~1\AppData\Local\Temp\1573089809848.png)



### 7、GET和POST请求

get请求其实就比较容易一些，只需在输入的url后面拼接上参数。

**例如：**curl www.baidu.com?wd=xxxxxxx

但是post请求就麻烦了，需要手动去指明参数。

**例如：**curl -X POST -d  "name=xxx&sex=男" www.baidu.com

可以使用**-d**，也可以使用**--data**

### 8、HTTP的动作

http的动作共有八种：`GET  POST  HEAD  DELETE PUT  OPTIONS   TRACE  CONNECT`

想要指定使用哪种动作，需要指明，**默认是GET**

举例：curl POST www.baidu.com 或 curl -X POST www.baidu.com

### 9、请求时添加请求头

我们在模拟表单提交时，难免会添加一些附带的请求头，如果没有这些请求头，可能会被服务器驳回这次请求，那么，添加请求头就可以使用**--head 或者-H**

例如：curl --head "accept-encoding=gzip, deflate, br"

​	    curl -H "accetp-encoding=gzip, deflate, br"

### 10、携带cookie访问

cookie是存储在我们客户端的文件，当我们访问过某些网站之后，如果在你的电脑上**存储了一些cookie**，那么当你再次访问这个网站时，会**携带**上这些cookie，作为你**身份的标识**，可以使用**--cookie添加携带的cookie**

例如：curl --cookie "name=value"  www.baidu.com

### 11、修改userAgent

userAgent是服务器识别我们访问设备的标识，例如，当你用手机进行访问的时候，就给你返回适配手机的网页，当你用电脑访问时，就返回适配电脑的网页。我们可以修改user-agent

举例：curl --user-agent "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36"

