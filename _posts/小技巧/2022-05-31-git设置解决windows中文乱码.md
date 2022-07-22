---
title: 解决 Git 在 windows 下中文乱码的问题
date: 2022-05-31
categories: [小技巧]
tags: [github,git]  
img_path: /assets/img/
---

# 解决 Git 在 windows 下中文乱码的问题

## 原因

中文乱码的根源在于 windows 基于一些历史原因无法全面支持 utf-8 编码格式，并且也无法通过有效手段令其全面支持。

## 解决方案

在命令行下输入以下命令：

```shell
git config --global core.quotepath false  		# 显示 status 编码
git config --global gui.encoding utf-8			# 图形界面编码
git config --global i18n.commit.encoding utf-8	# 提交信息编码
git config --global i18n.logoutputencoding utf-8	# 输出 log 编码

```

## 配置windows环境变量

因为 git log 默认使用 less 分页，所以需要 bash 对 less 命令进行 utf-8 编码

新增加环境变量 LESSCHARSET = UTF-8

![image-20220531003629671](2022-05-31-git设置解决windows中文乱码.assets/image-20220531003629671.png)

## 查看效果

关掉所有终端，重新打开查看效果

![image-20220531003724773](2022-05-31-git设置解决windows中文乱码.assets/image-20220531003724773.png)

# 解决warning: LF will be replaced by CRLF"

参考链接https://stackoverflow.com/questions/17628305/windows-git-warning-lf-will-be-replaced-by-crlf-is-that-warning-tail-backwar

windows问题

```
git config --global core.autocrlf false
```

