## VO、PO、POJO、DTO、DAO、BO区别与联系

### 1. VO

`value object `：值对象

**作用：**用于业务层之间的数据传递，以及返回给界面的内容的封装，提供get和set方法。例如使用PO封装的对象，给界面返回时想隐藏数据库表内的字段细节或者不想将数据库中的表字段全部返回，就可以使用VO进行封装。

### 2. PO

`persistant object `：持久层对象

**作用：**PO中的封装对象必须和数据库中的表字段一一对应，且提供set和get方法。就是对数据库表字段的一个封装。

### 3. DTO

`data transfer object `：数据传输对象

**作用：**传输数据过程中使用的对象，数据传输目标是数据的访问对象从数据库中检索数据。例如：PO中的数据库字段传输到VO中。

### 4. BO

`business object `：业务对象

**作用：**将多个PO进行封装，便于业务的操作和连贯性。

### 5. POJO

`plian ordinary java object `：简单无规则java对象

**作用：**纯粹的java对象，在实际开发中，我们使用POJO代替了VO、PO，其实这是不规范的开发，POJO本身无任何意义，但是可以转换为PO、VO、DTO。

### 6. DAO

`data access object`：数据访问对象

**作用：**主要用于对数据的访问，包括对数据库的访问。