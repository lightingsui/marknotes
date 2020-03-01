## 关于git中拉取代码超时的问题

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-02-26_20-26-34.png)

因为我科学上网了，所以导致拉取或者提交代码超时，这时候要想正常使用git，就必须使用代理了。

可以使用全局代理或者部分代理来解决这个问题。

选择其中一个即可

```
git config --global https.proxy [http://127.0.0.1:1080](http://127.0.0.1:1080/)

git config --global https.proxy [https://127.0.0.1:1080](https://127.0.0.1:1080/)
```

取消代理

```
git config --global --unset http.proxy

git config --global --unset https.proxy
```