# mysql中的字符集与排序规则

二话不说，先来看一张图

![1578142614509](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-01-04_21-36-18.png)

想必这张图大家在建立数据库的时候都见到过，而且见到的时候可能对字符集还有一点熟悉，但是对这个排序规则不是很熟，不知道是干嘛用的，我今天建立数据库的时候也是这样的，于是我就停了下来，搜了搜资料，把这个弄懂了。

## 字符集

什么是字符集？

字符集就是字符的集合，例如熟知的有ASCII字符集、Unicode字符集、GB2312字符集，每个字符集包含的字符个数是不同的，ASCII字符集包含了128字符，unicode字符集包含了1114112 个字符（目前只有少数使用，大部分还是空闲的），GB2312包含了汉字6763个，文字符号682个。

注意：GB2312字符集是不包含英文字符的，但是如果我们要在GB2312字符集中使用英文字符怎么办呢，那好办，如果在GB2312中找不到的字符，就会去ASCII字符集中去找。

细心的同学会发现，在mysql数据库中，有着两种相似的字符集，utf8和utf8mb4，那么这两种字符集是不是有什么暧昧关系？同学，我只能这么说.........你这猜想是完全正确滴！

传说啊，在mysql5.3以前，只存在utf8，而且这个utf8和我们平常所说的utf-8是有差别的，平常我们所说的utf-8表示一个字符使用的字节数为[1, 4]个字节，即[1, 4]个码元，但是，数据库里面的utf8表示一个字符所使用的字节数为[1, 3]，三个字节只能表示unicode字符集中基本语言平面的字符，是不完全的，所以千万不要把数据库中的utf8和utf-8编码弄混了，要分清他们的区别。这时，一声巨响，mysql5.3诞生了，并且还带着utf8mb4字符集一起诞生的，utf8mb4的出现完全就是弥补utf8的缺陷，使其可以表示出unicode字符集中的所有字符。

所以，推荐大家平常还是使用utf8mb4吧，可能更好的兼容utf-8编码。兼的死死的。

大家对字符集或者编码感兴趣的可以看看[字符集与编码](https://juejin.im/post/5c847cb5f265da2dd63929c2 )这篇博客，讲的非常的明了。

## 排序规则

什么又是排序规则呢？

排序规则就是按照特定的顺序对字符进行排序，也供搜索使用，例如当我们去数据库中查询表中的数据时，就会根据排序规则去查找，还有就是当我们使用order by时，也是根据排序规则进行查找的。

每一种字符集对应了多种排序规则，且每个排序规则只对应一种字符集，是一种典型的1对多的关系。

那么接下来就得介绍一下排序规则都有哪些了。以utf8字符集举例

> - **utf8_general_ci**： 查询时不区分大小写匹配
> - **utf8_general_cs**： 查询时区分大小写匹配
> - **utf8_bin**： 字符串每个字符串用二进制数据编译存储。 区分大小写，而且可以存二进制的内容，与utf8_general_cs一样，区分大小写
> - **utf8_unicode_ci** ： 和utf8_general_ci一样，不区分大小写
>
> 转载处 <https://segmentfault.com/a/1190000020339810> 

排序规则的命名是有约束的，开头只能是对应字符集的名称，中间是语言的名称，最后是类型，ci代表的是case insensitive，即大小写不敏感，cs代表的是case sensitive，即大小写敏感的，英语棒棒哒！

更多排序规则大家可以看这[官方文档](https://dev.mysql.com/doc/refman/5.7/en/charset-collation-names.html) 

那么大小写敏不敏感在mysql中有啥区别呢？

例如现在是大小写不敏感的，如果此时去查a，则会将A和a一起查出来。

如果是大小写敏感的，如果再去查a，则只会查出来a。

排序规则的基本定义说的差不多了，接下来就结合mysql进行实战吧。

## mysql支持的字符集

查询mysql支持的字符集可以使用一下命令

```sql
# 方式1
SHOW CHARACTER SET;

# 方式2
use information_schema;
select * from CHARACTER_SETS;
```

结果如下(部分结果)

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-01-04_21-57-44.png)

上面结果中的latin1、latin2可能大家很陌生，但是说到另一种字符集iso-8859-1大家可能很是熟悉，尤其是搞web的童鞋，其实latin1就是iso-8859-1的别名，iso-8859系列一共有15个，iso-8859-n（n=1,2,3,4........16）,其中不包括12，

每一种字符集都有一个默认的字符序，即上述的DEFAULT_COLLATE列。

如果查看mysql支持的字符序以及每种字符集默认的字符序呢？

```sql
# 第一种方式
show collation;

# 第二种方式
use information_schema;
select * from collations;
```

结果如下

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-01-04_22-06-30.png)

可以看到，图中带yes所对应的记录就是前面字符集对应的默认字符序。

## 系统字符集以及字符序

当我们创建数据库时候，如果没有指定字符集和字符序，mysql就会使用默认的字符集以及字符序，也就是`character_set_server`、`collation_server` 。如何查看这些信息呢？

```sql
SHOW VARIABLES;
```

取了其中一部分结果

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/Snipaste_2020-01-04_22-12-13.png)

如何修改这些配置呢？

方法一共有三种：

**方法一 启动服务时指定**：

```sql
mysqld --character-set-server=latin1 \
       --collation-server=latin1_swedish_ci
```

此种方法也可以单独指定字符集

**方法二 配置文件指定**：

`centos7`中配置文件在`/etc/my.cnf`或着`/etc/mysql/my.cnf`

进行一下修改

```sql
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
```

重新启动mysql服务

```bash
systemctl restart mysqld
```

**方法三 运行时修改**：

```mysql
SET character_set_server = utf8;
```

注意：此种方法知识暂时有效的，下次重新启动mysql服务后，配置会被重置。

如果我们在创建数据库时想指定字符集以及编码，该怎么办呢，图形界面的操作我就不提了，我就说一说命令的方式

```mysql
CREATE DATABASE db_name
    [CHARACTER SET charset_name]
    [COLLATE collation_name]
```

如果只指定字符集，没有指定字符序，那么mysql就会使用此字符集默认的排序规则，如果只指定了字符序，而没有指定字符集，那么mysql就会使用此字符序对应的字符集。

还有就是数据库中表使用的字符集以及字符序，如果我们在创建表的时候指定了字符集以及字符序，那么此表就会使用指定的，如果没有指定字符序或者字符集，那么此表就会使用所属数据库的字符集以及字符序。

```mysql
# 创建表示指定字符集以及字符序
CREATE TABLE tbl_name (column_list)
    [[DEFAULT] CHARACTER SET charset_name]
    [COLLATE collation_name]]

# 修改表的字符集以及字符序
ALTER TABLE tbl_name
    [[DEFAULT] CHARACTER SET charset_name]
    [COLLATE collation_name]
```

表中的列也是存在字符集以及字符序的，如果在创建列的时候指定了字符集以及字符序，那么此列就会使用指定的，如果没有指定，那么就使用所属表的字符集以及字符序。

```mysql
# 创建列时指定字符集以及字符序
col_name {CHAR | VARCHAR | TEXT} (col_length)
    [CHARACTER SET charset_name]
    [COLLATE collation_name] 
    
# 修改列的字符集以及字符序
ALTER TABLE test_table ADD COLUMN char_column VARCHAR(25) CHARACTER SET utf8;
```

一个mysql下，每个库可以使用不同的字符集，每个库下的所有表也可以使用不同的字符集，以及每个表中列也可以使用不同的字符集。

一般都是创建数据库时候指定字符集，然后再创建表的时候以及创建列的时候都不会指定字符集以及字符序。

## 系统字符集的查看

**查看某个数据库的字符集以及字符序**

```mysql
use test_schema;
SELECT @@character_set_database, @@collation_database;
```

**查看表的字符集以及字符序**

```mysql
SHOW TABLE STATUS;
```

首先需要选中某个数据库，然后才可执行

**查看某一列的字符集以及字符序**

```sql
SELECT CHARACTER_SET_NAME, COLLATION_NAME FROM information_schema.COLUMNS WHERE TABLE_SCHEMA="test_schema" AND TABLE_NAME="test_table" AND COLUMN_NAME="char_column";
```

