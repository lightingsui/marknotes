## SQLite

### 数据库的创建方式

1. 自定义一个类，继承抽象类SQLiteOpenHelper，实现抽象类中的两个抽象方法
2. 定义构造方法

```java
// @param 上下文
// @param 数据库文件名称
// @param 默认为null即可
// @param 数据库版本
public SQLiteOpenHelper(@Nullable Context context, @Nullable String name,
        @Nullable CursorFactory factory, int version) {
    this(context, name, factory, version, null);
}
```

3. 实现自定义的类

```java
MyDataBaseHelper myDataBaseHelper = new MyDataBaseHelper(getApplicationContext());

// 创建或者获取数据库，当第一次时，是创建数据库，否则是获得数据库
SQLiteDatabase writableDatabase = myDataBaseHelper.getWritableDatabase();

// 创建或者获取数据库，当第一次时，是创建数据库，否则返回数据库，但是，如果存储空间
// 满了，将返回只读数据库
SQLiteDatabase readableDatabase = myDataBaseHelper.getReadableDatabase();
```

自定义类的完整代码

```java
package com.example.databaseinit;

import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

import androidx.annotation.Nullable;

public class MyDataBaseHelper extends SQLiteOpenHelper {
    public MyDataBaseHelper(@Nullable Context context) {
        super(context, "test.db", null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {

    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```

### 数据库的初次创建

```java
@Override
public void onCreate(SQLiteDatabase db) {
    db.execSQL("create table info (_id integer primary key autoincrement, name varchar(20))");
}
```

数据库初次创建时候执行，多用于创建表结构

### 数据库版本升级

```java
@Override
public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

}
```

当数据库版本升级时执行，多用于更新表结构

> 数据库版本不可回退，回退版本将报错

### 知识补充

+ SQLite数据库底层存储的都是字符串
+ SQLite中的varchar哪怕存储的长度超出了指定最大长度，也是可以存储的，不会报错

### 小技巧

+ 主键一般以下划线开头