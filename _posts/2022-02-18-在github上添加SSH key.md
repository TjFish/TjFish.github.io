---
title: 在github上添加SSH key
date: 2022-02-18
categories: [小技巧]
tags: [github,ssh]  
img_path: /assets/img
---


通过在 github 上添加 SSH key，实现**免密登录**，不需要每次push 和 clone 时都输入密码。

## Https和 SSH 的区别：

- 前者可以随意克隆github上的项目，而不管是谁的；而后者则是你必须是你要克隆的项目的拥有者或管理员，且需要先添加 SSH key ，否则无法克隆。
- https url 在push的时候是需要验证用户名和密码的；而 SSH 在push的时候，是不需要输入用户名的，如果配置SSH key的时候设置了密码，则需要输入密码的，否则直接是不需要输入密码的。

## 在 github 上添加 SSH key ：

### **1、检查是否已经有 SSH key**

运行 git Bash 客户端，输入如下代码：

```bash
cd ~/.ssh
ls
```

这两个命令就是检查是否已经存在 id_rsa.pub 或 id_dsa.pub 文件，如果文件已经存在，**那么你可以跳过步骤2，直接进入步骤3**。

### **2、创建新的 SSH key**

```ruby
ssh-keygen -t rsa -C "your_email@example.com"
```

代码参数含义：

-t 指定密钥类型，默认是 rsa ，可以省略。
 -C 设置注释文字，比如邮箱。
 -f 指定密钥文件存储文件名。

以上代码省略了 -f 参数，因此，运行上面那条命令后会让你输入一个文件名，用于保存刚才生成的 SSH key 代码，如：

```bash
Generating public/private rsa key pair.
# Enter file in which to save the key (/c/Users/you/.ssh/id_rsa): [Press enter]
```

当然，你也可以不输入文件名，使用默认文件名（推荐），那么就会生成 id_rsa 和 id_rsa.pub 两个秘钥文件。

- 接着又会提示你输入两次密码（该密码是你push文件的时候要输入的密码，而不是github管理者的密码），
- 当然，你也可以不输入密码，直接按回车。那么push的时候就不需要输入密码，直接提交到github上了，如：

```bash
Enter passphrase (empty for no passphrase): 
# Enter same passphrase again:
```

接下来，就会显示如下代码提示，如：

```bash
Your identification has been saved in /c/Users/you/.ssh/id_rsa.
# Your public key has been saved in /c/Users/you/.ssh/id_rsa.pub.
# The key fingerprint is:
# 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your_email@example.com
```

当你看到上面这段代码的收，那就说明，你的 SSH key 已经创建成功，你只需要添加到github的SSH key上就可以了。

![image-20210405213257970](github设置并添加ssh.assets/image-20210405213257970.png)

创建成功

### **3、添加 SSH key 到 github**

- 首先你需要拷贝 id_rsa.pub 文件的内容，你可以用编辑器打开文件复制。

- 登录你的github账号，从右上角的设置（ [Settings](https://link.jianshu.com?t=https://github.com/settings) ）进入，然后点击菜单栏的 SSH and GPG keys 进入页面。
- 点击 Add SSH key 按钮添加一个 SSH key 。把你复制的 SSH key 代码粘贴到 key 所对应的输入框中，记得 SSH key 代码的前后不要留有空格或者回车。上面的 Title 所对应的输入框你也可以输入一个该 SSH key 显示在 github 上的一个别名，也可以不输入，默认会使用你的邮件名称。

### 4、验证是否配置成功

```bash
ssh -T git@github.com
```
成功的话如下所示

![image-20220310160828315](github设置并添加ssh.assets/image-20220310160828315.png)

之后都推荐走ssh拉取代码，再github 上选择clone地址时，选择ssh地址，入下图。这样`git push` 与 `git clone` 都可以直接走代理了，并且不需要输入密码。

![image-20220310155659787](github设置并添加ssh.assets/image-20220310155659787.png)