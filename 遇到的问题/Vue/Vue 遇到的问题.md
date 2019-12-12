# Vue 遇到的问题

## 1、npm报错 This is probably not a problem with npm,there is likely additional logging output above可能的原因

出现此种错误的原因可能是在devServer中配置项的问题，可能存在非法的配置项，重新检查，去掉非法的配置项

## 2、Unexpected console statement

原因是vue项目其清了ESline语法检查，关掉ESline语法即可

![1572084922064](C:\Users\LIGHTI~1\AppData\Local\Temp\1572084922064.png)

![1572160012275](C:\Users\LIGHTI~1\AppData\Local\Temp\1572160012275.png)

## 3、sockjs.js?9be2:1606 GET http://localhost:8080/sockjs-node/info?t=1572159795581 net::ERR_CONNECTION_REFUSED

![1572160049958](C:\Users\LIGHTI~1\AppData\Local\Temp\1572160049958.png)

