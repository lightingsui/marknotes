> #### 前言
>
> 在看一些GitHub上第三方开源库时总会看到很多颜色鲜艳好看的svg图标，如：
>
> 
>
> ![img](https://upload-images.jianshu.io/upload_images/1605558-1d06262953a9452d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

查看了一些第三方的README.md并百度谷歌下后发现其实很简单，只需要配置下就可以
 参考

- [svg图标官网说明https://shields.io](https://shields.io/#/)
- [GitHub上 README 增加图片标签](https://blog.csdn.net/yangbodong22011/article/details/51791085)
- [简单图标库](https://simpleicons.org/)

```
https://img.shields.io/badge/<图标前部分的文字>-<图标后部分的文字>-<颜色>.svg
注意点：
1、因为-是会用到的分割字符，如果文字中有-，如要用--来代替，如Objective-C要写成Objective--C
2、颜色可以是支持的英文，也可以是6位的16进制的字符串，如blue、0000ff
3、可以支持圆角、矩形样式、社交样式、还可以添加logo
```

------

### 具体demo：

1、瘦小的圆角矩形

```
![瘦小的圆角矩形](https://img.shields.io/badge/language-swift-brightgreen.svg?style=plastic)
```

![img](https://upload-images.jianshu.io/upload_images/1605558-6b7daaa933c41d20.png?imageMogr2/auto-orient/strip|imageView2/2/w/328/format/webp)

2、正常大小的圆角矩形

```
![正常大小的圆角矩形](https://img.shields.io/badge/language-swift-green.svg)
```

![img](https://upload-images.jianshu.io/upload_images/1605558-8920411a3fbb80ce.png?imageMogr2/auto-orient/strip|imageView2/2/w/376/format/webp)

3、正常大小的矩形

```
![正常大小的矩形](https://img.shields.io/badge/language-swift-yellowgreen.svg?style=flat-square)
```

![img](https://upload-images.jianshu.io/upload_images/1605558-2db63599b5bedb45.png?imageMogr2/auto-orient/strip|imageView2/2/w/276/format/webp)

4、普通社交样式

```
![社交样式](https://img.shields.io/badge/Stack_Overflow-10k+-yellow.svg?style=social)
```

![img](https://upload-images.jianshu.io/upload_images/1605558-def12347632ed27c.png?imageMogr2/auto-orient/strip|imageView2/2/w/402/format/webp)

5、带logo的社交样式

```
![带logo社交样式](https://img.shields.io/badge/GitHub-10k+-yellow.svg?style=social&logo=github)
```

![img](https://upload-images.jianshu.io/upload_images/1605558-9b5b98863de14392.png?imageMogr2/auto-orient/strip|imageView2/2/w/414/format/webp)

 

 

 

 

 