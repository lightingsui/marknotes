# jackson遇到的问题

```java
java.lang.NoClassDefFoundError: com/fasterxml/jackson/core/exc/InputCoercionException
com.fasterxml.jackson.databind.ObjectMapper._findRootDeserializer(ObjectMapper.java:4389)
	at com.fasterxml.jackson.databind.ObjectMapper._readTreeAndClose(ObjectMapper.java:4250)
	at com.fasterxml.jackson.databind.ObjectMapper.readTree(ObjectMapper.java:2711)
	at com.sll.utils.HttpUtilsTest.testDoGet(HttpUtilsTest.java:27)
Caused by: java.lang.ClassNotFoundException: com.fasterxml.jackson.core.exc.InputCoercionException
	at java.net.URLClassLoader.findClass(URLClassLoader.java:382)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:349)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 33 more

// 解决办法
// jackson依赖没有完全导入
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-databind</artifactId>
   <version>2.10.0</version>
</dependency>
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-core</artifactId>
   <version>2.10.0</version>
</dependency>
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-annotations</artifactId>
   <version>2.10.0</version>
</dependency>
```

-----------

```java
com.fasterxml.jackson.core.JsonParseException: Unrecognized token 'handleComboCallback': was expecting (JSON String, Number, Array, Object or token 'null', 'true' or 'false')
 at [Source: (String)"handleComboCallback({"accessories":{  "status":200  ,"data":{"wName":"Apple iPhone 11 (A2223) 128GB 黑色 移动联通电信4G手机 双卡双待","wid":100008348542,"imageUrl":"jfs/t1/59022/28/10293/141808/5d78088fEf6e7862d/68836f52ffaaad96.jpg","chBrand":"Apple","model":"iPhone 11","list":[{"accessoryShows":[{"wName":"亿色苹果11/X/XR/XS钢化膜 iPhone11Pro手机膜Xs Max全屏覆盖高清防爆手机贴膜 【11/XR通用】高清款-买1送1","wid":33011952514,"imageUrl":"jfs/t1/69724/13/13771/308809/5db15b9fE4b38dfd6/4691d805530300df.jpg","wMeprice":99.90,"wMaprice":49.90},{"[truncated 2049 chars]; line: 1, column: 20]

	at com.fasterxml.jackson.core.JsonParser._constructError(JsonParser.java:1840)
	at com.fasterxml.jackson.core.base.ParserMinimalBase._reportError(ParserMinimalBase.java:722)
	at com.fasterxml.jackson.core.json.ReaderBasedJsonParser._reportInvalidToken(ReaderBasedJsonParser.java:2867)

// 解决办法
json格式不正确，检查json格式
```





