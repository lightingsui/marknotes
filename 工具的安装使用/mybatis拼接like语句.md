> 转载 原文链接：<https://blog.csdn.net/m0_37897396/article/details/80237920> 

```sql
<!-- ******************** 模糊查询的常用的3种方式:********************* -->
    <select id="getUsersByFuzzyQuery" parameterType="User" resultType="User">
        select <include refid="columns"/> from users
        <where>
            <!--
                方法一: 直接使用 % 拼接字符串 
                注意:此处不能写成  "%#{name}%" ,#{name}就成了字符串的一部分,
                会发生这样一个异常: The error occurred while setting parameters,
                应该写成: "%"#{name}"%",即#{name}是一个整体,前后加上%
            -->
            <if test="name != null">
                name like "%"#{name}"%"
            </if>
            <!--方法二: 使用concat(str1,str2)函数将两个参数连接 -->
            <if test="phone != null">
                and phone like concat(concat("%",#{phone}),"%")
            </if>
            <!--方法三: 使用 bind 标签,对字符串进行绑定,然后对绑定后的字符串使用 like 关键字进行模糊查询 -->
            <if test="email != null">
                <bind name="pattern" value="'%'+email+'%'"/>
                and email like #{pattern}
            </if>
        </where>
    </select>
```

4. 表达式:name like '%'||#{name}||'%'

这个不能满足要求,直接把数据库中的所有数据查询出来了,不符合我的要求,在mysql中||代表是or的意思

==>  Preparing: select count(0) from (select *from bbs_brand WHERE name like'%'||?||'%' and falg=?) as total

==>Parameters: 莲(String), 1(Integer)