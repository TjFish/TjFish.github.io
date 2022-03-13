---
title: Oracle Apex 代码获取页面值
date: 2021-07-06
categories: [Oracle Apex]
tags: [Oracle Apex]  
img_path: /assets/img/
---

Oracle Apex 使用代码（JavaScript和PLSQL）获取并修改页面中各个Item 值。

## JavaScript取值

最基本的方法，使用`$v()` 取值和 `$s()`设置项目值，`$x()`确定当前页面上是否存在具有给定名称的项目

```
$v('P2_NAME')  //取值
-- TjFish
$s('P2_NAME','TjDog') //设置值
```

如果需要访问数组中的多个项目，则`$v2()`很方便。 例如，可以将复选框或穿梭控件中的多个选择值作为数组提取，下面是示例JavaScript代码

```
Arr = $v2("P2_SHUTTLE");
for (idx=0; idx<Arr.length; idx++) {
  //do something with Arr[idx];
}
```

其实上述`$v() $s() $x() $v2()`是apex.item的语法糖

如果需要*Display*值，这时可以用`displayValueFor()`函数。

```
apex.item('P2_NAME').getValue()
-- 21  //返回id
apex.item('P2_NAME').displayValueFor(21)
-- TjDog //返回显示值
//结合上面两句 
apex.item('P2_NAME').displayValueFor($v('P2_NAME'))
apex.item('P2_NAME').setValue('Display','Return')
```

## PLSQL取值

```
# 在页项前加 ":" 即可取值
:P2_NAME
# 修改值,修改方法就是PLSQL赋值语句
:P2_NAME:='修改';
```

