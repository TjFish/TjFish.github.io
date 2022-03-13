---
title: Oracle Apex 学习路线与安装
date: 2021-07-03
categories: [Oracle Apex]
tags: [Oracle Apex]  
img_path: /assets/img/
---

## 简介

正常的应用程序一般分为三部分：前端，后端，数据库

其工作流程为：前端页面点击，发送Http请求调用后端API，后端查询数据库，返回数据

Oracle Apex 同理：

- 前端页面点击（各个区域，组件，按钮）
- 执行操作（相当于调用API，这里就是执行一些js/plsql代码，不过大多数区域组件已经预先实现了大多数操作）
- 查询数据加返回数据（源，SQL，PLSQL）

所以学习oracle apex 要从下面几个方面入手
## Oracle Apex 的安装
- [Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/)
- [Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (准备工作)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装CentOS, Tomcat, Nginx)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章5：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-5/)

## 基础部分

学习APEX 预先实现的组件的使用

这里面包括学习组件的样式（长什么样），功能（有什么用），如何交互，以及如何喂数据

- 区域（Region）
- 组件（Item）
- 按钮（Button）
- 学习操作

操作主要是指动态操作和处理，这里就是自己定义的与用户交互的地方（绝大多数交互由组件已经完成，不过还有一些交互需要自己完成，同时有时我们需要自己定义一些交互）。

- 动态操作（Dynamic Action）
- 处理（Processing）
- js/plsql 自定义交互
- 查询数据加返回数据

这一部分是使用组件必然需要用到的，主要是需要填充区域/组件 的源（Source）属性

由于使用比较频繁，所以单拉出来学习。

- 数据库表/视图 （Tabel /View）
- 共享组件 （Share Component）
- SQL 查询 （SQL Query）
- 静态值 （Static Value）

所以整个工作流程就是，选择预先定义的区域/组件，填充源（喂数据），定义动态操作（可选，比如页面间的跳转）

## 进阶部分

学完基本部分，对Oracle Apex 的基本工作流程有了了解，后面是一些进阶操作，可以提高开发效率，完善功能（导航/面包屑，SQL工作室，插件，使用第三方js/css，数据导入功能，*图表*）

包括以下方面

### SQL工作室

sql工作室使得我们可以点点点来进行数据库操作（DDL），从而极大提高开发效率。在此其中，**对象浏览器**经常使用，

对象浏览器

- 创建 表/视图/触发器
- 修改 表/视图/触发器 定义
- 查看 表/视图 数据

SQL命令

- 用来直接执行sql语句，通常用来验证自己的sql语句是否正确，是否查询出想要的值
- 保存sql 语句

### 导航与面包屑

导航与面包屑部分关乎用户的体验，一般的后台管理界面都有侧边导航栏。因此，如何定义导航栏和面包屑，在

`应用程序构建器 -> 选择应用 -> 共享组件 -> 导航部分中的导航菜单和面包屑`。在这里面可以自己定义网页的导航栏和面包屑

### 使用第三方js/css

现在有许多功能十分强大的JS代码库，Oracle Apex 可以很方便的集成它们。要使用第三方JS 代码，在`应用程序构建器 -> 选择应用 -> 共享组件 -> 文件部分中的静态工作区/应用程序文件`导入希望引入的js文件，导入成功后，复制*引用* 部分（比如#WORKSPACE_IMAGES#canvas2image.js）

将引用字符串，粘贴到想要引入js文件的页面属性 `JavaScript 文件URL中`，注意不要多空格，每个文件一行引用。之后在本页面就可以直接使用引入的js 文件中的函数了。

css文件同理

### 插件

有许多朋友开发了apex的插件（插件就相当于扩展apex默认提高的区域/组件）

[apexworld](https://apex.world/ords/f?p=100:1:::NO:::) 是关于apex的开源社区，里面提供了许多apex的插件。

下载插件后，导入插件也很简单，流程和导入应用一样。

## 更多资源

- [Oracle Apex教程（从19.1版开始）| Vinish Kapoor的博客](https://www.foxinfotech.in/2019/11/oracle-apex-tutorials-version-19-1-onwards.html)

- [Ottmar with APEX](https://ogobrecht.github.io/)

- [Oracle APEX 系列文章10：Oracle APEX Evangelion（EVA 补完计划） | 钢钢更新](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-10/)