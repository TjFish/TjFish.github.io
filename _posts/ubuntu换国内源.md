ubuntu20.04 更换国内源的方法

首先备份原来的源

```bash
sudo mv /etc/apt/sources.list /etc/apt/sources.list.bak
```

再输入以下命令打开sources.list配置文件更换源。

```bash
sudo vim /etc/apt/sources.list

//配置内容如下-阿里源

deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse 

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse 

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse 

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse 

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse focal
```

更新源

```bash
sudo apt-get update
```

