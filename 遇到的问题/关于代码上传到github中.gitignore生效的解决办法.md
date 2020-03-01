# 关于代码上传到github中.gitignore生效的解决办法

最近在代码上传到github中时，明明文件中带有.gitignore文件，并且里面也配置了忽略的文件，但是并没有生效，查阅资料找到了解决办法，原来在被忽略的文件不应该加入到git仓库中，如果加入到了仓库中，那么就算在.gitignore中设置也无法被忽略。

**情况一：**

刚创建项目，还没有添加到仓库。那么此时就不要将待忽略的文件加入到git库中，即可解决。

**情况二：**

项目已经全部添加到了git库中，此时可以删除隐藏在项目中的.git文件，然后重新初始化仓库，此时再执行步骤一。

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-03-01_22-38-07.png)