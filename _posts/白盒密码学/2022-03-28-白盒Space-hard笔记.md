---
title: 白盒Space-Hardness 论文阅读笔记
date: 2022-03-28
categories: [白盒密码学]
tags: [白盒密码学]  
img_path: /assets/img/
---


原文：[White-box Cryptography Revisited: Space-Hard Ciphers](https://www.iacr.org/archive/asiacrypt2016/10031190/10031190.pdf)

**简评：白盒密码学的经典好文，开辟了白盒密码学的新方向。本文可以当作综述来看，对15年以前的白盒算法了非常好的总结。**

## 1. INTRODUCTION

**主要贡献点**

- **White-box security is based on black-box security**:提出了新的白盒算法，同时该算法的安全性依赖于内置的黑盒算法（AES），安全性有保障
- **Space hardness**：开辟了白盒密码学的新方向，Space-Hard
- **No external encoding**：新白盒算法不再依赖于外部编码，因为外部编码在实践中几乎不可用
- **Variable white-box implementation size:**可变长度的白盒实现，可以适用于不同场景

## 2. ATTACK MODELS

介绍了黑盒和白盒的攻击模型

### 黑盒

#### 黑盒场景

该模型假设攻击者能够使用已知或选择的明文或密文访问密码的输入和输出。 可以允许自适应查询。

#### 黑盒的两个安全性要求

- Key recovery security：攻击者不能计算出密钥
- Distinguishing security：攻击者不能区分随机字符串和加密字符串

### 白盒

#### 白盒场景

该模型假设攻击者可以通过任意跟踪执行、检查内存中的子结果和密钥、插入断点、修改内部代码以静态和动态方式完全控制密码的执行环境，变量等等。

#### 白盒的两个安全性要求

- Key extraction security：不能被提取出密钥
- Code lifting security：攻击者可以直接拿白盒表去做加解密。这要求白盒（代码）不能被剥离。

## 3. KNOWN WHITE-BOX TECHNIQUES

### 白盒AES实现

代表作- Chow 白盒 2002（把chow的白盒AES讲解的很透彻和简洁）

#### 安全性问题

目前（2015年）所有白盒实现几乎都被攻破了，还有一种针对SPN结构的通用攻击。

或许是因为目前的白盒实现 都在考虑 黑盒安全，或许使得白盒安全难以达到

> 一种针对SPN结构的通用攻击，可以看看
>
> 【31】Wil Michiels, Paul Gorissen, and Henk D. L. Hollmann. Cryptanalysis of a Generic Class of White-Box Implementations. In Selected Areas in Cryptography - SAC 2008, LNCS, Vol. 7707, pages 414–428, 2008.

#### 外部编码的问题

- 影响互操作性，互通性（interoperability），对于一些企业非常强调互操作性，比如银行。
- 外部编码需要安全执行环境：这个要求对于DRM环境本身就是冲突的。既然有安全执行环境，为什么不所有解密操作都在安全环境中执行呢（笑）。但移除外部编码，又会导致不安全，因为第一轮和最后一轮查找表直接可以被攻击者获取破解。

### 专用方法：ASASA

不依赖于外部编码，2014年由Biryukov提出。

#### 弱白盒安全性定义

 **Weak White-box Security**

![image-20220324152812028](白盒Space-hard笔记.assets/image-20220324152812028.png)

**不可压缩性**: 如果在完全访问 F 的情况下很难获得大小小于 T 的等效密钥，则函数 F 是 EK 的 T 安全弱白盒实现。

换句话说，攻击者在计算上应该很难找到任何小于 T 的紧凑等效函数。因此，攻击者需要大小为 T 的代码来完全复制密码的功能。 弱白盒安全性可以通过需要从白盒环境中提取的数据量来估计代码提升的难度。

>这个可以看下, 这篇文章提出了弱白盒和强白盒定义
>
>[4] Alex Biryukov, Charles Bouillaguet, and Dmitry Khovratovich. Cryptographic Schemes Based on the ASASA Structure: Black-Box, White-Box, and Public-Key (Extended Abstract). In Advances in Cryptology - ASIACRYPT 2014, LNCS, Vol. 8873, pages 63–84, 2014

#### 安全性问题

已经提出了针对ASASA的攻击方法，并且发现了AS结构的通用攻击。

## 4.  OUR DESIGN GOALS

### 安全性目标(M, Z)-space hardness

新的安全性指标 **(M, Z)-space hardness**

![image-20220324152746658](白盒Space-hard笔记.assets/image-20220324152746658.png)

在攻击者只能获取小于M大小的代码时，其能够加解密随机的明文（密文）的可能性少于2^-z。



### 功能性目标

- 不依赖于外部编码

### 性能目标

- 能和现有白盒实现相当
- 能适用于不同的场景，由多个参数调节代码大小。

## 5.SPACE: FIXED SPACE

### 算法设计

SPACE 是一个 l 行 的 Feistel 网络结构，输入是n-bit的明文，输出是n-bit的密文。

总共有r轮的变换。对于每一轮的变换，又分成l行。

![image-20220324160005048](白盒Space-hard笔记.assets/image-20220324160005048.png)

变换操作看图很好理解。这里其实是把每一轮的密钥由F函数生成。生成的密钥和其他行去做异或。

![image-20220324160013384](白盒Space-hard笔记.assets/image-20220324160013384.png)

接下来看F函数的构成，F函数是一个扩展函数。

![image-20220324160422562](白盒Space-hard笔记.assets/image-20220324160422562.png)

![image-20220324161110361](白盒Space-hard笔记.assets/image-20220324161110361.png)

在白盒环境下，F函数被做成一个可以复用的查找表。

### Feistel结构

整体来看，SPACE是使用了target-heavy Feistel 结构（Feistel结构的一种变种）

>这里提到了Feistel结构由许多变种，type-1, -2, -3 generalized Feistel ，target-heavy Feistel 

这里不使用SPN结构的原因是ASASA采用的SPN结构需要秘密的置换过程。而SPACE用到了标准的AES算法作为内置函数，自定义的置换过程不太行。

### 白盒下安全性分析

#### 密钥提取攻击

该攻击不可能做到，因为SPACE的查找表就一个F函数查找表，里面是标准AES-128加密。要从查找表提取key，就相当于要攻破AES-128，甚至更难，因为查找表还对AES的输出做了截断。

#### 代码提取攻击（ Code Lifting Attack）

假设攻击者只偷到了一部分的查找表，假设是i 条记录。

攻击者能够区分加密数据的可能性为，R为加密轮次。

![image-20220324173645246](白盒Space-hard笔记.assets/image-20220324173645246.png)

困难程度随着攻击者能偷取到的查找表而降低，如下图

![image-20220324173749744](白盒Space-hard笔记.assets/image-20220324173749744.png)



### Strong Space Hardness

在攻击者只能获取小于M大小的代码时，其能够区分随机明文的加解密结果的可能性少于2^-z。

![image-20220325201814139](白盒Space-hard笔记.assets/image-20220325201814139.png)

## 6.N-SPACE: VARIABLE SPACE

结合前面的SPACE方案，混合应用不同大小的F函数来实现可变的SPACE白盒方案。

![image-20220325203645782](白盒Space-hard笔记.assets/image-20220325203645782.png)

就是拿前面的SPACE盒子组合使用。