# StringUtils API(一)

### [jar包下载地址](https://repo1.maven.org/maven2/org/apache/commons/commons-lang3/3.7/commons-lang3-3.7.jar )

## 1. public static boolean isEmpty(CharSequence cs)

##### 说明： 如果字符串为null或者为""，则返回true

```java
// example
StringUtils.isEmpty(null)      = true
StringUtils.isEmpty("")        = true
StringUtils.isEmpty(" ")       = false
StringUtils.isEmpty("bob")     = false
StringUtils.isEmpty("  bob  ") = false
```

## 2. public static boolean isNotEmpty(CharSequence cs)

##### 说明： 如果字符串不为null或者“”，则返回true

```java
// example
StringUtils.isNotEmpty(null)      = false
StringUtils.isNotEmpty("")        = false
StringUtils.isNotEmpty(" ")       = true
StringUtils.isNotEmpty("bob")     = true
StringUtils.isNotEmpty("  bob  ") = true
```

## 3. public static boolean isAnyEmpty(CharSequence... css)

##### 说明：检查是否有字符串为null或“”，其中有一个为null或“”就返回true，只能接受字符串或者字符串数组，List不可以

```java
// example
StringUtils.isAnyEmpty(null)             = true
StringUtils.isAnyEmpty(null, "foo")      = true
StringUtils.isAnyEmpty("", "bar")        = true
StringUtils.isAnyEmpty("bob", "")        = true
StringUtils.isAnyEmpty("  bob  ", null)  = true
StringUtils.isAnyEmpty(" ", "bar")       = false
StringUtils.isAnyEmpty("foo", "bar")     = false
StringUtils.isAnyEmpty(new String[]{})   = false
StringUtils.isAnyEmpty(new String[]{""}) = true
```

##### 作用： 主要用于字符串数组中是否有null或“”字符串

## 4. public static boolean isNoneEmpty(CharSequence... css)

##### 说明：检查字符串是否都不为null或者“”，都不为null或者“”返回true 

```java
// example
StringUtils.isNoneEmpty(null)             = false
StringUtils.isNoneEmpty(null, "foo")      = false
StringUtils.isNoneEmpty("", "bar")        = false
StringUtils.isNoneEmpty("bob", "")        = false
StringUtils.isNoneEmpty("  bob  ", null)  = false
StringUtils.isNoneEmpty(new String[] {})  = true
StringUtils.isNoneEmpty(new String[]{""}) = false
StringUtils.isNoneEmpty(" ", "bar")       = true
StringUtils.isNoneEmpty("foo", "bar")     = true
```

##### 作用：主要用于字符串数组中是否有null或“”字符串 

## 5. public static boolean isAllEmpty(CharSequence... css)

##### 说明：用于检查全部的字符串是否为null或者“”

```java
// example
StringUtils.isAllEmpty(null)             = true
StringUtils.isAllEmpty(null, "")         = true
StringUtils.isAllEmpty(new String[] {})  = true
StringUtils.isAllEmpty(null, "foo")      = false
StringUtils.isAllEmpty("", "bar")        = false
StringUtils.isAllEmpty("bob", "")        = false
StringUtils.isAllEmpty("  bob  ", null)  = false
StringUtils.isAllEmpty(" ", "bar")       = false
StringUtils.isAllEmpty("foo", "bar")     = false
```

##### 作用：主要用于字符串数组中是否都为null或“”字符串 

## 6. public static boolean isBlank(CharSequence cs)

##### 说明： 用于检查字符串是否为null或“”或空格字符串,如果包含这些条件的其中任意一个，则返回true

```java
// example
StringUtils.isBlank(null)      = true
StringUtils.isBlank("")        = true
StringUtils.isBlank(" ")       = true
StringUtils.isBlank("bob")     = false
StringUtils.isBlank("  bob  ") = false
```

## 7. public static boolean isNotBlank(CharSequence cs)

##### 说明： 用于检查字符串是否为null或“”或空格字符串,如果包含这些条件的其中任意一个，则返回false

```java
// example
StringUtils.isNotBlank(null)      = false
StringUtils.isNotBlank("")        = false
StringUtils.isNotBlank(" ")       = false
StringUtils.isNotBlank("bob")     = true
StringUtils.isNotBlank("  bob  ") = true
```

## 8. public static boolean isAnyBlank(CharSequence... css)

##### 说明： 检查字符串数组中是否包含null，“”， “       ”，如果包含任意一个，则返回true

```java
 // example
 StringUtils.isAnyBlank(null)             = true
 StringUtils.isAnyBlank(null, "foo")      = true
 StringUtils.isAnyBlank(null, null)       = true
 StringUtils.isAnyBlank("", "bar")        = true
 StringUtils.isAnyBlank("bob", "")        = true
 StringUtils.isAnyBlank("  bob  ", null)  = true
 StringUtils.isAnyBlank(" ", "bar")       = true
 StringUtils.isAnyBlank(new String[] {})  = false
 StringUtils.isAnyBlank(new String[]{""}) = true
 StringUtils.isAnyBlank("foo", "bar")     = false
```

## 9. public static boolean isNoneBlank(CharSequence... css)

##### 说明： 检查字符串数组中是否包含null，“”， “       ”，如果包含任意一个，则返回false

```java
 // example
 StringUtils.isNoneBlank(null)             = false
 StringUtils.isNoneBlank(null, "foo")      = false
 StringUtils.isNoneBlank(null, null)       = false
 StringUtils.isNoneBlank("", "bar")        = false
 StringUtils.isNoneBlank("bob", "")        = false
 StringUtils.isNoneBlank("  bob  ", null)  = false
 StringUtils.isNoneBlank(" ", "bar")       = false
 StringUtils.isNoneBlank(new String[] {})  = true
 StringUtils.isNoneBlank(new String[]{""}) = false
 StringUtils.isNoneBlank("foo", "bar")     = true
```

## 10. public static boolean isAllBlank(CharSequence... css)

##### 说明： 检查字符串数组中是否包含null，“”， “       ”，如果所有的字符串都满足条件，则返回true

```java
 // example
 StringUtils.isAllBlank(null)             = true
 StringUtils.isAllBlank(null, "foo")      = false
 StringUtils.isAllBlank(null, null)       = true
 StringUtils.isAllBlank("", "bar")        = false
 StringUtils.isAllBlank("bob", "")        = false
 StringUtils.isAllBlank("  bob  ", null)  = false
 StringUtils.isAllBlank(" ", "bar")       = false
 StringUtils.isAllBlank("foo", "bar")     = false
 StringUtils.isAllBlank(new String[] {})  = true
```





























