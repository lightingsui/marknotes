## MybatisGenerator插件的使用

MybatisGenerator插件（简称MBG），是根据数据库自动生成model、mapper、*mapper.xml文件的一个自动化插件，达到简化开发的目的。

在配置文件中增加以下配置，为generate.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!-- 数据库连接信息配置文件 -->
    <properties resource="database.properties"/>
    <context id="MySqlContext" targetRuntime="MyBatis3" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>
        <property name="javaFileEncoding" value="UTF-8"/>
        <!-- 为模型生成序列化方法-->
        <plugin type="org.mybatis.generator.plugins.SerializablePlugin"/>
        <!-- 为生成的Java模型创建一个toString方法 -->
        <plugin type="org.mybatis.generator.plugins.ToStringPlugin"/>
        <!--生成mapper.xml时覆盖原文件-->
        <plugin type="org.mybatis.generator.plugins.UnmergeableXmlMappersPlugin" />

        <commentGenerator type="com.sll.CommentGenerator">
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
            <property name="suppressDate" value="true"/>
            <property name="addRemarkComments" value="true"/>
        </commentGenerator>

        <!-- oracle数据库 -->
        <!--<jdbcConnection driverClass="oracle.jdbc.driver.OracleDriver"
                        connectionURL="jdbc:oracle:thin:@远程ip地址或localhost:1521:orcl" userId="用户名"
                        password="密码">
            &lt;!&ndash; 针对oracle数据库 &ndash;&gt;
            <property name="remarksReporting" value="true"></property>
        </jdbcConnection>-->

        <jdbcConnection driverClass="${jdbc.driver}"
                        connectionURL="${jdbc.url}"
                        userId="${jdbc.username}"
                        password="${jdbc.password}">
            <!--解决mysql驱动升级到8.0后不生成指定数据库代码的问题-->
            <property name="nullCatalogMeansCurrent" value="true" />
        </jdbcConnection>

        <!-- model -->
        <javaModelGenerator targetPackage="com.sll.model" targetProject="src\main\java"/>

        <!-- mapper.xml -->
        <sqlMapGenerator targetPackage="com.sll.mapper" targetProject="src\main\resources\mapper"/>

        <!-- mapper -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.sll.mapper"
                             targetProject="src\main\java"/>

        <!--不生成帮助类（Exmaples） -->
        <!-- enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false"
            enableSelectByExample="false" selectByExampleQueryId="false" -->
        <!--已生成的表 <table schema="demo" tableName="USER" domainObjectName="User"></table> -->
        <!--<table schema="" tableName="ABS_DEAL" domainObjectName="AbsDeal"
               enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false"
               selectByExampleQueryId="false">
        </table>-->

        <!--生成全部表tableName设为%-->
        <table tableName="%">
            <generatedKey column="id" sqlStatement="MySql" identity="true"/>
        </table>
    </context>
</generatorConfiguration>
```

数据库配置文件，结合以上xml使用，以mysql为例

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/efo?characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=false
jdbc.username=root
jdbc.password=root
```

最后，编写一个启动类，用来执行MybatisGenerator