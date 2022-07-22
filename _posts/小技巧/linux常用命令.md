## 常用命令

### 查看端口号占用

```
lsof -i:端口号
```

或者

```
netstat -tunlp | grep 端口号
```

## docker

### Centos/Ubuntu 安装docker

```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

### 查看所有容器

```
docker ps -a
```

### 查看容器日志

```
docker logs -f da6743d61e1a
```

### 进入容器

```
docker exec -i 69d1 bash
```

### 更换阿里源提升下载速度

首先登陆 https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

查看自己的专属加速器地址，根据地址执行下面命令

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://hv2pm8pp.mirror.aliyuncs.com"]
}
EOF
# 重启docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## go

### 安装go

需要定制版本上https://go.dev/dl/查看，这里给出go1.14版本

```
# 下载go1.14，不行就手动下载再上传
wget https://golang.org/doc/install?download=go1.14.7.linux-amd64.tar.gz

# 解压到/usr/local目录下
tar -C /usr/local -zxvf go1.14.7.linux-amd64.tar.gz

#配置Go环境变量
sudo vim /etc/profile

# 写入/etc/profile
export GOROOT=/usr/local/go
export GOPATH=$GOROOT/gopath
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

# 退出保存，使得环境变量生效
source /etc/profile

#查看go版本
go version
```

### 换国内源

```
go env -w GOPROXY=https://goproxy.cn,direct
```

