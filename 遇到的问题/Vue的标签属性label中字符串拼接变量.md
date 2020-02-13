# 问题

**问题描述**：Vue的标签属性label中字符串拼接变量

**问题解决**

```html
<el-form-item :label="`参数${(index + 1)}类型`" v-for="(item, index,) in props.row.params">
    <span v-text="item"></span>
</el-form-item>
```

**结果展示**

![](https://raw.githubusercontent.com/lightingsui/Pic/master/img/TIM%E6%88%AA%E5%9B%BE20200201205306.png)